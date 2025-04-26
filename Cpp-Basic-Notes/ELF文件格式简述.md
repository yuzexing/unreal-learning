# ELF文件格式简述

#### 说明
本节讨论的格式为ELF文件格式，主要是为了加深了解程序运行时的内存状态。

##### 可重定位的目标文件包含以下内容：\
<picture>
  <img width=400 height=400 src="https://github.com/user-attachments/assets/fd2e0e26-ff90-48f4-aa1a-63bcfa49d2bf">
</picture>


##### 程序内存的经典布局：

<picture>
  <img width=400 height=400 src="https://github.com/user-attachments/assets/0d4d3542-c8e2-465f-8e6e-9c97498cf0c6">
</picture>

[Reference1](https://refspecs.linuxbase.org/LSB_3.0.0/LSB-Embedded/LSB-Embedded/specialsections.html#:~:text=)
|
[Reference2](https://www.cnblogs.com/zhcpku/p/14437940.html?utm_source=chatgpt.com)

##### ELF文件中的段包含：
1. .text段
2. .data段，.bss段，.rodata段
3. .rel.text段 .rel.data段
4. .strtab段、.symtab段、.shstrtab段
5. Section Header Table
6. .stack段、.heap段

#### .text段

.text段为代码段，用于存放指定序。程序加载到内存后，通常为只读，大小固定。

#### data段，bss段，rodata段
**.data段**为数据段，用于存放编译时期就能确定的全局数据，包括**已初始化**的**全局变量**和**静态变量**。加载到内存后，可读可写，大小固定。\
##### 补充：
静态变量包括：静态局部变量和静态全局变量；只要是已初始化的静态变量都会存储在.data段。
```
int i = 3;
char a[] = "Hello World";
static int b = 2023;    // Initialized static global variable
void foo (void) {
  static int c = 2023; // Initialized static local variable
}
```

**.bss段**存储未显示初始化的静态数据，包括变量和常量，不论是全局变量还是局部变量。例如：
```
static int i;
static char a[12];
```
.bss段在可执行文件中占用地址空间，不占用文件空间，通过Section Header记录大小，在加载到内存后再由加载器分配空间并且清零。

**.rodata段**属于常量区(Read-Only Data Section)，存放只读数据，比如字符串常量，全局const变量；加载到内存后，内容只读，大小固定；\
特殊情况：
1. 常数折叠或者直接作为立即数写入指令中，所以不存在常量区
2. 字符串通常在常量区共享一份数据
3. 在某些系统中可能存在.rodata段被多个进程共享的情况（真的吗）

#### .rel.text段、rel.data段

**.rel.text段**和**rel.data段**分别是针对text段和data段的重定位表；在可执行文件中，如果采用动态链接的方式，可能会保留 **.rel.text段** 和 **.rel.data段**；


#### strtab段、symtab段、shstrtab段

**.strtab段**是字符串表，可以存储符号名(st_name)数据，供symtab段关联 \
**symtab段**是符号表，用于存储变量、函数符号的相关数据，例如符号名称在字符串标的索引、可见范围、数据类型、定义地址等数据\
**shstrtab段** 也是字符串表，可以存储节名(sh_name)数据，供 ELF 头和节头表通过索引定位各节名称使用 \

#### Section Header Table

**节头标(Section Header Table)**是描述ELF文件中各个节的数据结构。节头标通过ELF头的字段指示出其在文件中的偏移和大小，通过节头表可以定位所有节在文件中的位置以及类型等信息。



[Reference1](https://docs.oracle.com/cd/E19455-01/806-3773/elf-2/index.html)|
[Reference2](https://refspecs.linuxbase.org/LSB_3.0.0/LSB-Embedded/LSB-Embedded/specialsections.html#:~:text=)|



#### 6. .stack、.heap


[Data segment wikipedia](https://en.wikipedia.org/wiki/Data_segment)




