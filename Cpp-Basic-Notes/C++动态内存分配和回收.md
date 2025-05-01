# C++动态内存分配和回收

在C++中一般通过new关键字进行内存的动态分配，通过delete关键字进行内存的回收。也可以通过malloc函数和free函数分配和回收堆内存。这种成为显示分配和回收。


### new/delete和malloc/free的区别

简单的说:
1. new/delete是C++操作符，而malloc/free是函数
2. new/delete会调用构造函数和析构函数
3. ~可以被重载
4. ~是类型安全的
5. malloc/free只能用于单纯的内存分配和回收
6. malloc和new申请的动态内存空间，尽量使用对应的方法进行释放（而不是new+free）
7. new在自由区(free store)申请动态内存空间，而malloc在堆内存(heap)申请。

### free store和heap的区别

> heap是操作系统上的概念，而free store是C++中的抽象概念，取决于new的底层实现，如果new是通过malloc实现的，那么free store=heap \
> 个人感觉free store是C++为了兼容性而衍生出来的概念。


[stackoverflow](https://stackoverflow.com/questions/240212/what-is-the-difference-between-new-delete-and-malloc-free)|
[microsoft](https://learn.microsoft.com/en-us/cpp/cpp/new-operator-cpp?view=msvc-170)|
[free store VS heap](https://www.cnblogs.com/QG-whz/p/5060894.html)