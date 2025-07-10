# C++设计模式


### 单例设计模式

C++中的单例设计模式，只是一种组织全局变量和静态函数的方式，实现的方式有很多，例如单一的cpp，类，命名空间等，最重要的是实现封装成员数据，开放对外操作数据的接口。

#### 类的实现方式：
1. 保证实例全局唯一（私有构造函数，删除拷贝构造，删除赋值操作运算符）
2. 隐藏需要操作的数据，对外提供数据操作的方法


使用类实现单例的优势：
1. 语义性好
2. 允许存在一个实例进行操作（命名空间、单一的cpp无法做到实例化一个对象）


#### 问题一：静态函数中创建的局部变量在哪里？
> 栈

#### 问题二：函数中创建的局部静态变量又在哪里？
> 在静态区，函数内的静态变量初始化只会**在第一次进入函数时初始化一次**，后续调用不会重新初始化，而是沿用上次的值(通过编译器实现)

举例：
```
void test() {
	static int b = 1; // 首次执行时初始化一次
	b++;
	cout << b << endl;
}

int main() {
	test(); // 2
	test(); // 3
	test(); // 4
}
```
[learncpp](https://www.learncpp.com/cpp-tutorial/static-local-variables)|
[cppreference](https://en.cppreference.com/w/c/language/static_storage_duration)

> 对于全局和静态变量，有静态初始化和动态初始化两种初始化方式。
> 1. 静态初始化：零初始化和常量初始化,常发生在编译时期
> 2. 动态初始化：程序运行时期初始化一次。


> 静态存储周期的对象初始值只在程序运行前初始化一次(零初始化和常量初始化)。
> 静态变量的**动态初始化**，发生在首次执行声明时。

```
// debug 模式下的粗糙动态初始化，Release模式下可能会被编译器内联然后被静态初始化
int dynamic_init(int x) {
	return x * 2;
}

void test() {
	static int z = dynamic_init(3); // 首次执行时动态初始化
	z++;
	std::cout << z << std::endl;
}

static int a = dynamic_init(3); // 也是全局变量的动态初始化

int main() {
	test(); // 7
	test(); // 8
}
```

#### 全局变量的动态初始化的问题

每个翻译单元内是按顺序初始化的，所以静态初始化是安全的。
但是动态初始化，需要关注动态初始化的时机：
1. main函数执行之前
2. 程序运行中

由于不同翻译单元之间的动态初始化顺序是不确定的，就会导致动态初始化顺序混乱的问题：
```
// a.cpp
int duplicate(int n)
{
    return n * 2;
}
auto A = duplicate(7); // A is dynamic-initialized
```
```
// b.cpp
#include <iostream>

extern int A;
auto B = A; // dynamic initialized

int main()
{
  std::cout << B << std::endl;  // 14 or 0
  return EXIT_SUCCESS;
}
```

解决方法：
1. 重构代码，避免翻译单元之间的初始化依赖
2. 采用类似单例设计模式的方法，使用时初始化

```
// a.cpp
int duplicate(int n)
{
    return n * 2;
}

auto& A()
{
  static auto a = duplicate(7); // Initiliazed first time A() is called
  return a;
}

```
```
// b.cpp
#include <iostream>
#include "a.h"

auto B = A();

int main()
{
  std::cout << B << std::endl; // always 14
  return EXIT_SUCCESS;
}

```
[static-variable-initialization](https://pabloariasal.github.io/2020/01/02/static-variable-initialization)

#### 单例设计的使用场景

> 需要一个全局唯一的可操作的实例时，例如网络通信的socket，获取随机数，数据库连接池，线程池等

### 工厂模式




