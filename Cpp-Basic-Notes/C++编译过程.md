# C++编译过程

> 这里讨论了一个Cpp文件，如何变为可执行文件的简要过程。较为深入的探讨了链接器的作用，对于链接过程中未明确的点，提供了自己的理解和思路。

为什么要研究链接过程？因为在大部分描述链接器作用的文章中，几乎没有人提及链接器如何实现引用和定义之间的关联。所以我打算弄清楚其中关联的细节。

首先，总体流程分为四个步骤：
- **预处理过程**
  - 由预处理器驱动，
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

介绍每一个section的作用

在链接过程中对引用和定义之间关联起到关键作用的两个结构体，一个是符号表，一个是重定位表。

重定位表每一个表项都关联了一个引用的位置和其对应的符号在符号表中的索引。

符号表每一个表项关联了一个符号与符号定义的具体位置。

这看起来就可以实现引用和定义之间的关联，但是在实际过程中，会有更复杂的情况需要讨论。

1. **重定位表项的生成条件**

2. **符号表中符号冲突如何处理**

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

> 此处没有细致的讨论编译过程和静态链接、动态链接过程，后续会继续补充。





