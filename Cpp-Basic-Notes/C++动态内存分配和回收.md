# C++动态内存分配和回收

在C++中一般通过new关键字进行内存的动态分配，通过delete关键字进行内存的回收。也可以通过malloc函数和free函数分配和回收堆内存。

<image src="https://github.com/user-attachments/assets/f09b2e08-b945-4421-85e7-9fd6f4c13b2e" width=200 height=300>

### 动态内存分配(new malloc)
动态内存分配器维护一个进程的虚拟内存区间，一般是堆或者自由存储区(以下统一称为堆)。堆从.bss区域开始向上生长(向高地址)，内核中维护的brk指向堆顶。

分配器将堆视为一组不同大小的块的集合，每一个块就是连续的虚拟内存片，要么是已分配的要么是未分配的。空闲块的组织方式有：
1. 空闲分配表 
2. 隐式空闲链表（块信息在头部保存，返回有效载荷出地址作为指针地址）
3. 显示空闲链表（保存前后空闲块的指针，优点是减少了空闲块分配的时间）

空闲块的分配算法：
1. 最佳适应算法
2. 首次适应算法
3. 最坏适应算法

#### 隐式空闲链表项
<image src="https://github.com/user-attachments/assets/275884f1-73f3-4e40-a05c-e36d17e8fd3e" width=400 height=200>

#### 显示空闲链表项
<image src="https://github.com/user-attachments/assets/ac56c602-3d7b-48f6-915a-209ad06cac83" width=400 height=200>


### C++堆内存分配过程

#### 当申请的内存空间 < 128k

1. 通过new操作符请求分配空闲块
2. 分配器查询合适的空闲块
3. 如果没有合适大小的空闲块，则进行空闲块合并
4. 如果还是不够，则分配器可以调用```sbrk```函数，向内核申请额外的堆内存。(同时向高地址调整```brk指针```)

> 释放时，归还给brk(内存池)，依赖于堆顶对象被释放，才能减少大小

#### 当申请的内存空间 > 128k

通过``mmap``经过系统调用，在**内存映射区**分配虚拟内存，释放时，立刻归还给操作系统

### mmap和brk的区别

两种内存分配方式的关键差异体现在：
- 地址连续性：brk分配连续地址空间，mmap分配独立内存块
- 分配粒度：brk支持按字节移动指针，但物理映射以页为单位
- 释放机制：mmap分配的内存可单独释放，brk依赖高位释放

### 内存分配的系统调用的中断次数

1. 如果申请内存堆<128k，并且内存足够，可能会有缺页中断（看情况的）
2. 如果申请内存堆<128k，内存不够，brk一次中断，至少一次缺页中断
3. 如果申请内存堆>128k，mmap一次系统中断分配，至少一次缺页中断

### 问题一：通过new/malloc获取的指针是物理地址吗？

> 虚拟地址。每一个虚拟页与一个真实页框相对应，由PCB中的页表实现地址转换。再初次分配是可能没有相应的物理页框，需要缺页中断

### 问题二：为什么要使用动态内存分配器？(为什么要在堆上分配内存？)

> 1. 需要较大的空间
> 2. 需要变量具有较长的生命周期

### new/delete和malloc/free的区别

简单的说:
1. new/delete是C++操作符，而malloc/free是函数
2. new/delete会调用构造函数和析构函数
3. ~可以被重载
4. ~是类型安全的
5. malloc/free只能用于单纯的内存分配和回收
6. malloc和new申请的动态内存空间，尽量使用对应的方法进行释放（而不是new+free）
7. new在自由区(free store)申请动态内存空间，而malloc在堆内存(heap)申请。
8. new 分配失败会抛异常，malloc分配失败获得空指针
9. new 分配内存自动计算大小，malloc分配内存需要手动计算内存大小

### 问题一：malloc分配的是虚拟内存还是物理内存？

虚拟内存

### 问题二：malloc分配的内存是否立刻得到物理地址？

不一定，因为malloc只分配虚拟内存，不会做初始化操作，只有在使用内存时，才会去获取物理内存，进行页表查询，进行物理内存分配

而``new``操作符，会进行构造函数的调用，初始化成员变量

### 问题三：虚拟内存和物理内存的区别

通过``malloc``分配的虚拟内存，可能在页表中没有分配相应的物理内存，所以需要进入缺页中断，分配相应的物理内存(存储在磁盘/内存)

### 问题四：free是如何知道释放多大的内存的？

在malloc分配的内存块头部，会存放内存块的信息，通过内存块的信息知道释放多少内存

### 问题五：free释放内存后，内存还在吗？

可能会被归还内存池，也可能会被操作系统回收，取决于分配方式是brk还是mmap

[参考](https://zhuanlan.zhihu.com/p/311527161)

### free store和heap的区别

> heap是操作系统上的概念，而free store是C++中的抽象概念，取决于new的底层实现，如果new是通过malloc实现的，那么free store=heap \
> 个人感觉free store是C++为了兼容性而衍生出来的概念。

[stackoverflow](https://stackoverflow.com/questions/240212/what-is-the-difference-between-new-delete-and-malloc-free)|
[microsoft](https://learn.microsoft.com/en-us/cpp/cpp/new-operator-cpp?view=msvc-170)|
[free store VS heap](https://www.cnblogs.com/QG-whz/p/5060894.html)
