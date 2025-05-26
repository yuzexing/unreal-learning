# 内存对齐


### 什么是内存对齐
内存对齐是在计算机内存中排列和访问数据的方式。它由三个独立但相关的问题组成：数据对齐、数据结构填充和打包。

只要存储字长大于等于计算机所支持的最大的原始数据类型，那么对齐的访问始终访问单个内存字

[wikipedia](https://en.wikipedia.org/wiki/Data_structure_alignment)

<img src="https://github.com/user-attachments/assets/2ac49746-705f-47db-a4ff-d0d84d6ea13c" width="300" height="200" />


### 为什么要内存对齐

#### 1.提高速度
CPU总按照字长读取数据（32位处理器上，32位的存储字长和数据总线的情况下为4字节32位），因此当CPU进行非对齐地址访问时，处理器可能读取多个字。在最坏情况下，访存事务总数增加两倍。

#### 2.增加访存指令可用位数

边界对齐的情况下(依然是在32位机器上，字节寻址)，任何地址位数都可以减少2位。多出的两位可以用于增加寻址空间大小，或者用于标记位。

#### 3.减少异步错误

因为一次内存的读写是原子性的，但是在非对齐的情况下，多次的内存读操作可能会被中断，在没有额外处理的情况下（加锁等?），可能导致两次读出的结果既不是更新前的也不是更新后的值。（这种情况非常少见）

#### 4.向后兼容

兼容老版本的处理器


[stackoverflow](https://stackoverflow.com/questions/381244/purpose-of-memory-alignment)

### 如何实现内存对齐

1. 对于单独的数据项，编译器通常在对齐的边界上分配地址
2. 对于结构体，编译器会插入额外的padding以保持对齐要求
```
struct mystruct_A {
    char a;
    char gap_0[3]; /* inserted by compiler: for alignment of b */
    int b;
    char c;
    char gap_1[3]; /* -"-: for alignment of the whole struct in an array */
} x;
```
3. 通过```#pragma pack(n)```指令调整结构体成员的对齐和填充方式，常用于节省空间
4. 通过```alignas()```更改对齐方式

[stack overflow](https://stackoverflow.com/questions/4306186/structure-padding-and-packing)


### 问题一：为什么结构体的最小对齐方式需要等于结构体内最大元素的对齐方式？
便于结构体内最大元素的对齐

### 问题二：在编译阶段还没有分配运行时内存地址，那么编译器是如何控制内存对齐和内存对齐规则的呢？

回答：简单的说：在编译和链接的过程中分别记录了每个段所要求的对齐方式和程序运行时要求的对齐方式。\
具体的说：在编译过程中，编译器通过在ELF文件中的节头表(Section header Table)中记录每个段的sh_addralign，来表示每个段的内存对齐方式。在连接过程中，读取每个节投的对齐需求，生成程序头中的p_align，来表示该程序的内存对齐方式。在程序装载时，按照p_algin加载到对应的内存地址。

[stackoverflow](https://stackoverflow.com/questions/63391927/what-is-p-align-in-elf-header)|
[docs oracle](https://docs.oracle.com/cd/E19683-01/816-7777/6mdorm6jj)



### 问题三：内存对齐是对齐虚拟内存还是物理内存？

> 编译器对虚拟内存进行对齐，C++程序员关心的是对虚拟内存的对齐。

### 问题四：栈和堆的对齐方式是什么样的？

> 栈规定4字节对齐，SP指针低位默认为00，不显示 \
> 堆内存的对齐由**内存分配器**控制，在调用malloc函数或者new进行内存分配时，分配器返回已对齐的物理内存块确保 可以为任何数据对象类型做对齐。对齐依赖于编译代码在32位模式(gcc -m32)还是64位模式(gcc -m64)中运行的。32位模式中，malloc返回的块的地址总是8的倍数。64位中块的地址总是16的倍数。

#### 为什么8或者16就可以物理内存对齐呢？
> 根据下面的malloc定义，32位一般返回满足8字节基本对齐要求的块，64位返回满足16字节基本对齐的块。是不是这样更好理解一些? \

[microsoft learn](https://learn.microsoft.com/en-us/cpp/c-runtime-library/reference/malloc)|
[stack overflow](https://stackoverflow.com/questions/70692795/why-is-malloc-16-byte-aligned)

### 问题五：内存对齐时，编译器为什么也要在结构体末尾padding？

> 便于结构体后的未知元素进行对齐，例如结构体数组等，举例在32位存储字长的计算机中：
```
struct A {
    short a;
    int b;
    char c;
}
// sizeof(A) == 12

A a[2];
```
> 当A结构体末尾插入padding时，数组a中下一个结构体```a[1]```自动内存对齐

### 问题六：alignas和编译器扩展指令#pragma pack的区别是什么？

| 特性  | alignas | #pragma pack |
| ------------- | ------------- | ------------- |
| 作用  | 过度对齐  | 减少对齐  |
| 作用对象  | 变量、类型别名、类/结构/联合、枚举  | 结构体/联合体/类的成员  |
| 场景  | 缓存行对齐  | 网络包  |

1. alignas不会减少默认对齐，不可以用于去除结构体内部的padding
2. #pragma pack不能增加对齐


#### 个人理解
> alignas对结构体使用时，是在内部增加padding，会影响结构体大小；而对于基本类型变量使用时，不会更改变量实际大小(sizeof)，是通过增加前面的padding来实现对齐的。

> alignas的常见应用场景：缓存行对齐，待补充 优先级五
[Reference](https://stackoverflow.com/questions/62489128/practical-use-cases-for-alignof-and-alignas-c-keywords)



### 概念补充：
1. CPU最小寻址单元是字节，但是每次从内存中读出的数据是一个字长
2. 如果从一个字长的数据中获取有效数据，是怎么实现的呢？通过掩码无关bit等方式，不确定具体实现方式
3. 假设存储字长是32位，那么内存条中一共4个存储单元，每个单元分别存储8个Bit，一次从4个存储单元读取连续的4个字节数据到数据总线。如果边界对齐，那么数据必定在这32位中，如果获取的数据小于32位，那么一次可以读取完成。如果边界不对齐，那么可能会需要多次访存。属于空间换时间的策略，内存便宜。

[Reference](https://softwareengineering.stackexchange.com/questions/237293/how-byte-loading-storing-is-implemented-by-the-cpu)
