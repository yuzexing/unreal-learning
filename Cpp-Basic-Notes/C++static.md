# C++static

### 介绍
static可以分为类/结构体内部声明的static，和外部声明的static：
- 类外部声明的static表示该仅在本翻译单元可见，具有内部链接性，链接器链接时不会在外部翻译单元寻找static修饰的符号的定义。类似于私有变量。
- 类内声明的静态变量被所有类的实例共享，该静态变量只有一个实例。类中的静态方法类似，类中的静态方法不会有实例被传入到方法中，所以没有隐式的this指针，也就无法直接访问非静态成员，但是可以通过完全限定名称访问静态类或者静态方法。

> 除非一个函数或者变量真的需要跨翻译单元链接时，否则尽量使用static修饰，不然会导致隐性的bug。

### 静态局部变量
作用域：仅在其被声明的作用域
生命周期：静态存储周期
举例：
```
void add() {
	static int i = 0;
	i++;
	std::cout << i << std::endl;
}
int main() {
	add();// 1
	add();// 2
	add();// 3
	add();// 4
    i; // undefined
	return 0;
}
```
> 作用域仅在add函数内，但是生命周期是静态存储周期。

> 个人感觉这样的``i``，存储在静态区中，在``add()``不再调用后，会不会是一种内存泄漏？

### 细节
1. 任何静态成员和静态方法必须与包含他的类名不同
2. 待补充 https://en.cppreference.com/w/cpp/language/static

### 问题一：静态类static class有什么用？

> 待补充优先级四

### 问题二：什么是静态存储周期

> 待补充 优先级四


[cppreference](https://en.cppreference.com/w/cpp/language/static)|

[Reference](https://stackoverflow.com/questions/2285915/non-static-vs-static-function-and-variable)