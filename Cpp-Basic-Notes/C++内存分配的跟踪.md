# C++内存分配的跟踪


### 重载new操作符

```
void* operator new(size_t size) {
	std::cout << "new:" << size << std::endl;
	return malloc(size);
}
```


### 重载delete操作符

```
void operator delete(void* ptr, size_t size) {
	std::cout << "delete:" << size << std::endl;
	free(ptr);
}
// delete还可以重载其他参数，补充原因
```


### 为什么可以重载多个参数的？
> 待补充 优先级五


### delete[]和delete的区别？
> 待补充 优先级五

### 为什么重载了new和delete不会影响构造函数？
> 待补充 优先级五


#### 类中重载的delete和new操作符默认是静态函数
> 待补充 优先级五

### VS内置内存分配跟踪分析工具
> 待补充 优先级五
