# C++智能指针

> unique_ptr 

如何实现避免复制或者拷贝 
异常安全是什么 不调用new

低开销

> shared_ptr 引用计数器 将两次new分配，合并为一次，因为shared_ptr内部需要一个内存块存储计数

有点开销

> weak_ptr