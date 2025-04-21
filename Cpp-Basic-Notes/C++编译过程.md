# C++编译过程

> 这里讨论了一个Cpp文件，如何变为可执行文件的简要过程。较为深入的探讨了链接器的作用，对于链接过程中未明确的点，提供了自己的理解和思路。

为什么要研究链接过程？因为在大部分描述链接器作用的文章中，几乎没有人提及链接器如何实现引用和定义之间的关联。为了弄清楚其中关联的细节并记录自己的学习过程。

首先，总体流程分为四个步骤：
- **预处理过程**
  - 预处理器展开CPP中所有宏定义并包含语句（以及任何以#开头的语句），并将结果传递给实际的编译器。
- **编译过程**
  - 编译过程中包含词法分析，语法分析，语义分析，中间代码生成，中间代码优化过程，汇编代码生成，汇编代码优化过程。在词法分析过程生成符号表，语法语义分析过程继续填充其中额外的符号信息，例如绑定信息，符号类型等。
- **汇编过程**
  - 汇编器将汇编代码转化为机器码。其中可重定位表通过编译器和汇编器生成。
- **链接过程**
  - 链接过程由链接器主导，将每个独立的.o文件合并为一个可重定位目标文件，合并过程称为重定位过程，最终获得执行性文件。

由汇编过程得到的ELF格式的.o目标文件包括以下内容：

![image](https://github.com/user-attachments/assets/fd2e0e26-ff90-48f4-aa1a-63bcfa49d2bf)

符号表项的结构：
```
typedef struct {
    int name;           //byte offset into str table that points to symbol name
    int value;          //symbol's addr: section offset, or VM addr
    int size;           //obj size in bytes
    char type:4,        //data, func, sec, or src file name (4 bits)
        binding:4;      //local or global (4 bits)
    char reserved;      //unused
    char section;       //section header index, ABS, UNDEF or COMMON
} Elf_symbol;
```
重定位表项的结构：
```
typedef struct {
    int offset;     //offset to the ref to relocate
    int symbol:24,  //symbol the ref should point to
        type:8;     //relocation type, tell the linker how to modify the new ref
} Elf32_Rel;
```

每一个section的定义可以[参考](https://docs.oracle.com/cd/E23824_01/html/819-0690/chapter6-46512.html#scrolltoc)

在链接过程中对引用和定义之间关联起到关键作用的两个结构体，一个是符号表，一个是重定位表。

重定位表每一个表项都关联了一个引用的位置和其对应的符号在符号表中的索引。

符号表每一个表项关联了一个符号与符号定义的具体位置。

这看起来就可以实现引用和定义之间的关联，但是在实际过程中，会有更复杂的情况需要讨论。

1. **重定位表项的生成条件**
  - 外部引用
  - 内部跨节引用
2. **符号表中同名符号冲突如何处理**
  - .o文件的同名强符号覆盖其他.o文件的弱符号
  - .o文件的同名弱符号覆盖.so文件的强符号（弱符号同理）
  - .o文件的同名强符号与其他.o文件的强符号冲突报错
3. **当多个.o文件进行节(section)合并到一个可重定位的目标文件中时，如何保持重定位表和符号表能够保持原本的关联性**

对于第三个问题，查询多个文档和文章无果，所以我提出了自己浅薄的理解试图实现链接过程中offset更新过程：
1. **首先需要了解section merge的过程，不同.o文件的相同的section合并到对应的section中**
2. **根据1可以知道：**
   - 同一个.o文件中，同节的引用相对位置不变，依然可以通过偏移量去定位
   - 不同节之间的相对位置发生变化，需要重新计算
   - 每个符号所指向的section的index可能需要更新，也就是更新st_shndx
3. **对于符号表和重定位表中的offset的重新计算公式可以定义为：**
   - ```offset = offset + s.size```
   - 在merge过程中，动态的更新offset的大小，确保其节内相对位置的准确性
   - 而对于节的定位在section header table可以查询每个section的相对地址，所以最终的相对地址为：
   - ```VMA = offset + ADDR(s) // ADDR(s)表示引用或定义的所在节的相对地址```
  
这样可以简单的实现引用与定义之间的关联。

对于提出的第三个问题，也是重定位过程中的步骤，合并节重定位和符号定义重定位。

第二个步骤是重定位所有引用，伪代码为：

```
foreach section s {              //iterate over each section
    foreach relocation entry r { //iterate over each reloc entry in each sec
        refptr=s+r.offset;       //ptr to ref to be relocated
        
        //relocate a PC-relative ref
        if(r.type == R_386_PC32) {
            //ADDR(s): run-time addr for the sec
            refaddr = ADDR(s)+r.offset; //ref's runtime addr
            //ADDR(r.symbol): run-time addr of the symbol
            *refptr = (unsigned) (ADDR(r.symbol)) + *refptr - refaddr);
        }
    
        //relocate an absolute ref
        if(r.type == R_386_32)
            *refptr = (unsigned) (ADDR(r.symbol) + *refptr);
    }
}
```
### 问题一：为什么遍历每一个section s而不是直接遍历.rel.text和.rel.data获取重定位项？

> 回答：避免hard code，因为重定向表所在的section也可能是.rela.text和.rela.data（例如64位x86架构使用.rela存储重定向表，32位x86架构使用.rel存储重定向表，还有其他架构不一一例举）

### 问题二：为什么遍历每一个section s能够获取重定位项r？

> 回答：遍历的sections是section header table，每一个section有对应的sh_type，通过sh_type == SHT_REL || sh_type == SHT_RELA找到重定向表所在的section，进行遍历


section header table的例子
```
Section Headers:
  [Nr] Name              Type            Addr     Off    Size   ES Flg Lk Inf Al
  [ 0]                   NULL            00000000 000000 000000 00      0   0  0
  [ 1] .note.gnu.build-i NOTE            080480b4 0000b4 000024 00   A  0   0  4
  [ 2] .gnu.hash         GNU_HASH        080480d8 0000d8 000018 04   A  3   0  4
  [ 3] .dynsym           DYNSYM          080480f0 0000f0 000010 10   A  4   1  4
  [ 4] .dynstr           STRTAB          08048100 000100 000001 00   A  0   0  1
  [ 5] .rel.text         REL             08048104 000104 000020 08  AI  3   6  4
  [ 6] .text             PROGBITS        08048124 000124 00002d 00 WAX  0   0  1
  [ 7] .eh_frame         PROGBITS        08048154 000154 000000 00   A  0   0  4
  [ 8] .dynamic          DYNAMIC         08048154 000154 000080 08  WA  4   0  4
  [ 9] .data             PROGBITS        080481d4 0001d4 000008 00  WA  0   0  4
  [10] .bss              NOBITS          080481dc 0001dc 000004 00  WA  0   0  4
  [11] .comment          PROGBITS        00000000 0001dc 00002d 01  MS  0   0  1
  [12] .symtab           SYMTAB          00000000 00020c 000180 10     13  16  4
  [13] .strtab           STRTAB          00000000 00038c 000050 00      0   0  1
  [14] .shstrtab         STRTAB          00000000 0003dc 000079 00      0   0  1
Key to Flags:
  W (write), A (alloc), X (execute), M (merge), S (strings), I (info),
  L (link order), O (extra OS processing required), G (group), T (TLS),
  C (compressed), x (unknown), o (OS specific), E (exclude),
  p (processor specific)
```



> 此处没有细致的讨论编译过程和静态链接、动态链接过程，后续会继续补充。








