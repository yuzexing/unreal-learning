# C++多态

### 虚析构函数

在继承的情况下，析构函数的顺序与构造函数的顺序相反。

虚析构函数表示，在多态时，可能存在被子类覆盖的析构函数需要被先调用。

例子：
```
#include <iostream>
#include <vector>

class A {
public:
	A() {
		std::cout << "A construct" << std::endl;
	}
	virtual ~A() {
		std::cout << "A destruct" << std::endl;
	}
};

class B: public A {
public:
	B() {
		std::cout << "B construct" << std::endl;
	}
	~B() {
		std::cout << "B destruct" << std::endl;
	}
};

int main() {
	A* a = new B();
	delete a;
	std::cin.get();
    // A construct
    // B construct
    // B destruct
    // A destruct
}
```

### 虚函数表

> 待补充

> RTTI待补充