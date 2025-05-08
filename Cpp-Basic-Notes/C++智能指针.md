# C++智能指针

### unique_ptr 

unique_ptr是作用域指针，当超出作用域时，自动调用delete释放内存，异常安全，创建智能指针的两种方式：
```
int main() {
    {    
        std::unique_ptr<A> p(new A(2));  // 为了保证异常安全，避免这样的写法
        std::unique_ptr<A> p1 = std::make_unique<A>(21); // 推荐的写法
    }
    // 作用域结束时，释放内存
}
```

#### 特性：避免复制和拷贝以保证唯一，通过禁用``=``操作符和禁用``拷贝构造``实现：
```
unique_ptr(const unique_ptr&)            = delete;
unique_ptr& operator=(const unique_ptr&) = delete;
```

> 待补充：异常安全是什么？如何实现异常安全的？

unique_ptr的开销：很低

### shared_ptr 

#### 用法：
```
std::shared_ptr<A> p1(new A(3));    // 不推荐的写法
std::shared_ptr<A> p = std::make_shared<A>(1); // 建议的写法

```

#### 建议的写法的好处
1. 将两次分配内存的请求合并为一次(new 一次，指针计数器所需的内存块一次)
2. 保证异常安全

#### 实现方法：
> 大部分情况下通过引用计数器实现，追踪指针有多少个引用，当引用计数为零时，自动调用delete回收内存。

#### 细节：
> 将两次new分配，合并为一次，因为shared_ptr内部需要一个内存块存储计数

shared_ptr的开销：额外的内存块进行引用计数

### weak_ptr

#### 用法：
```
std::shared_ptr<A> p2;
{
	p2 = std::make_shared<A>(1);
	std::shared_ptr<A> p3 = p2; // 引用计数+1
	std::weak_ptr<A> p4 = p2; // 不参与引用计数
    std::weak_ptr<A> p5 = std::weak_ptr<A>(p2);  // 不参与引用计数
    {
        // 通过lock获取对内存块的控制，如果*this已过期，则返回一个空的shared_ptr
        std::shared_ptr<A> p6 = p4.lock(); // 引用计数+1
        p4.expired() // 查看是否过期
        p4.reset() // 释放弱引用
        p4.use_count() // weak查看引用计数
        p3.use_count() // shared查看引用计数
    }
}
```

[learn microsoft](https://learn.microsoft.com/zh-cn/cpp/standard-library/weak-ptr-class?view=msvc-170#lock)

#### 理解

weak_ptr不会使底层对象保持存活，但是正常使用。

#### 使用场景
1. 解决循环引用
2. 观察``std::shared_ptr``对象的声明周期

> 待补充 优先级五，如何解决循环引用
