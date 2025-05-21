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

#### 异常安全是什么？如何实现异常安全的？

异常安全分为三种：
1. 不抛出任何异常
2. 强异常安全：如果抛出异常，程序回退函数调用前的状态（回滚）
3. 基本异常安全：如果抛出异常，保证程序处于有效状态（1. 不泄露任何资源；2. 不破坏任何数据结构）


[learn.microsoft](https://learn.microsoft.com/en-us/cpp/cpp/how-to-design-for-exception-safety?view=msvc-170)

unique_ptr的开销：很低

### API

- get()
- get_deleter(); // 获取删除器的引用
- release(); // 放弃指针的控制权，由外部进行指针的回收
- swap(ptr); // 交换两个智能指针的 ptr和deleter
- reset(); null/ptr // 获取目标指针的资源，放弃自己原本的资源

### deleter是什么？

删除器``std::default_delete<T>``一般来说是一个函数对象(函数指针，lambda表达式，std::function等)

> 待补充

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
> 通过引用计数器实现，追踪指针有多少个引用，当引用计数为零时，自动调用delete回收内存。

#### 细节：
> 将两次new分配，合并为一次，因为shared_ptr内部需要一个内存块存储计数

shared_ptr的开销：额外的内存块进行引用计数

### weak_ptr

弱引用指针，用来解决shared_ptr的循环引用问题：
```
struct Node {
  std::shared_ptr<Node> next;
  std::shared_ptr<Node> prev;
};
int main() {
	{
		std::shared_ptr<Node> s_ptr = std::make_unique<Node>();

		s_ptr->next = std::make_shared<Node>();

		s_ptr->next->prev = s_ptr;
	}
	return 0;
}
```
执行结果
```
construct
construct
```

解决代码：
```

struct Node {
	std::shared_ptr<Node> next;
	std::weak_ptr<Node> prev;

	Node() {
		std::cout << "construct" << std::endl;
	}
	~Node() {
		std::cout << "destruct" << std::endl;
	}
};


int main() {
	{
		std::shared_ptr<Node> s_ptr = std::make_unique<Node>();

		s_ptr->next = std::make_shared<Node>();

		s_ptr->next->prev = s_ptr;
	}
	return 0;
}
```
执行结果
```
construct
construct
destruct
destruct
```
正确的析构了

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
        // swap 交换智能指针的指向
    }
}
```

[learn microsoft](https://learn.microsoft.com/zh-cn/cpp/standard-library/weak-ptr-class?view=msvc-170#lock)

#### 理解

weak_ptr不会使底层对象保持存活，不会增加强引用计数器，但是会增加弱引用计数器。

#### 使用场景
1. 解决循环引用
2. 观察``std::shared_ptr``对象的声明周期

> 待补充 优先级五，如何解决循环引用


### shared_ptr构造与make_shared的使用场景：

在大部分情况下，按照官方推荐使用``make_shared``，除非：
1. make_shared内无法访问类的构造函数的情况。
2. 对象存储空间是瓶颈的情况。

> 解释第二点：shared_ptr对于对象的**存储周期的生命周期**更友好。也就是说，在强引用计数归零后，shared_ptr会立刻销毁对象，返还内存空间；而make_shared需要存储弱引用计数，所以在强引用计数归零后，会立刻销毁对象，但是要等到所有的弱引用计数归零后，才会释放内存空间。所以，当对象所需空间是性能瓶颈的时候，需要考虑使用shared_ptr。

### 代码对比
编译器：MSVM
C++版本：C++14
IDE：VS2022
```
#include <iostream>
// 测试类
class Dummy {
public:
	int m_a = 1;
	Dummy(int);
	void print();
	~Dummy();
};
void Dummy::print() {
	std::cout << "Dummy Print:" << m_a << std::endl;
}
Dummy::Dummy(int a): m_a(a){
	std::cout << "Dummy Construct" << std::endl;
}
Dummy::~Dummy() {
	std::cout << "Dummy Destruct" << std::endl;
}
void* operator new(size_t size) {
	std::cout << "new:" << size << std::endl;
	return malloc(size);
}
void operator delete(void* ptr, size_t size) {
	std::cout << "delete:" << size << std::endl;
	free(ptr);
}
int main() {
	using namespace std; 
    {
        std::weak_ptr<Dummy> w_ptr;
        {
            std::shared_ptr<Dummy> s_ptr;
            {
                //s_ptr = std::make_shared<Dummy>(1); // make_shared
                s_ptr = std::shared_ptr<Dummy>(new Dummy(1)); // 对比shared_ptr
                w_ptr = s_ptr; // 增加弱引用计数
            }
        }
        std::cin.get(); // 在此强引用计数归零
    }
    // 在此弱引用归零，make_shared调用delete
	return 0;
} 
```
#### make_shared输出结果：
```
new:16 // 创建并分配智能指针和Dummy对象
Dummy Construct
Dummy Destruct
in
delete:16 // 统一销毁
```
#### shared_ptr输出结果：
```
new:4
Dummy Construct
new:16
Dummy Destruct // 销毁Dummy对象
delete:4 // 先释放Dummy内存
in
delete:16 // 弱引用归零后，释放shared_ptr内存
```


[stackoverflow](https://stackoverflow.com/questions/75250585/why-make-shared-call-the-destructor-even-if-there-are-still-weak-pointer)

[a-tale-of-two-allocations](https://medium.com/pranayaggarwal25/a-tale-of-two-allocations-f61aa0bf71fc)