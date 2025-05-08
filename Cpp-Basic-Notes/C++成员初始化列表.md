# C++成员初始化列表

C++中有四种常见的类成员初始化方式
1. 成员初始化列表
2. 成员就地初始化
4. 成员默认初始化
3. 构造函数赋值


### 成员初始化列表注意事项


成员初始化列表的顺序与成员初始化顺序无关，但是：
**成员初始化列表的顺序，应该与成员在类定义出现的中顺序保持一致**
因为：可能会错误的依赖未初始化的成员。
```
class myClass
{
public:
   int A;
   int B;
   myClass();
};
myClass::myClass() :
B(42), A(B) {}
int main() {
	myClass C;
	std::cout << C.A << "|" << C.B << std::endl; // -858993460|42
}
```

[stackoverflow](https://stackoverflow.com/questions/24285112/why-must-initializer-list-order-match-member-declaration-order)

### 为什么无关成员初始化列表的顺序与成员初始化顺序无关

因为C++需要保证析构函数的顺序总是与构造函数的顺序相反。如果顺序由成员初始化列表的顺序控制，那么无法保证可靠的反向析构。

[cppreference](https://en.cppreference.com/w/cpp/language/constructor)

### 最佳实践

使用成员列表进行初始化操作，使用构造函数进行逻辑处理。

注意：如果在有``成员初始化列表``或者``成员默认初始化``时，再在构造函数中进行初始化操作，可能会产生两次初始化操作。例如下面的Class B被创建了两次：
```
#include <iostream>
static int times = 0;
class B {
public:
int b = 0;
	B() {
		b = times++;
	}
};
class A {
public:
	B* z = new B();
	A() {
		z = new B();
	}
};
int main() {
	A a;
	std::cout << a.z->b << std::endl; // 1
}
```


### 初始化顺序
1. 虚基类的构造函数（继承树深度优先从左到右顺序）
2. 基类的构造函数（继承说明符中从左到右的顺序）
3. 非静态成员初始化（类定义中先后顺序）
4. 构造函数执行

### 成员初始化方法优先级

顺序如最开始介绍顺序所示。
1. 当成员初始化列表和成员就地初始化都存在于类定义中。成员就地初始化被忽略。
2. 当成员初始化列表中省略某成员时，则被省略成员采用就地初始化。
3. 当既没有成员列表也没有成员就地初始化时，成员采用默认初始化。


[cppreference](https://en.cppreference.com/w/cpp/language/data_members)

### 就地初始化和初始化成员列表的区别是什么

非常细微的区别，但是确实不同，先补充说明核心区别，但是具体的区别不太明白

1. 就地初始化采用的是**复制初始化**
2. 初始化成员列表采用的是**直接初始化**

当开启C++11拷贝省略后，两者性能一样。否则直接初始化性能更优，复制初始化会有额外的拷贝构造函数调用。(在本地未成功复现，因为没有找到禁用拷贝省略的配置，待补充)

> 待补充 优先级五 直接初始化和复制初始化的区别
> 待补充 C++11的拷贝省略
```
int a = 1; // 拷贝初始化(拷贝构造) C++17前
int a(1); // 直接初始化(直接构造) 对于基本数据类型来说，拷贝和直接初始化几乎没有差别

class A {
public:
	int m_a;
	explicit A (int a) {} // 显示构造
};

A a(); // 直接初始化
A a = 1; // 隐式转换 + 拷贝初始化
A a = A(1) // 拷贝初始化

```

[stackoverflow](https://stackoverflow.com/questions/27352021/c11-member-initializer-list-vs-in-class-initializer)


### 成员中的default，0，delete的含义

- =default，显式请求编译器为特殊成员函数（构造函数、析构函数、拷贝/移动构造/赋值运算符）生成默认实现。
- =0，将成员函数声明为纯虚函数
- =delete，不希望编译器自动生成函数，显示禁用函数或者运算符。

```
struct Widget {
    Widget() = default;                // 默认构造
    Widget(const Widget&) = delete;    // 禁止拷贝构造
    Widget(Widget&&) noexcept = default; // 移动构造

    virtual void draw() const = 0;       // 纯虚，令 Widget 抽象

    int width = 100;                   // 类内默认初始化
    constexpr static int max_instances = 10; // C++17 inline constexpr
};
```

(stackoverflow)[https://stackoverflow.com/questions/6502828/what-does-default-mean-after-a-class-function-declaration]