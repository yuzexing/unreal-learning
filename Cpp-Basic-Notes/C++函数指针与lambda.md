# C++函数指针与lambda

函数指针常用于回调函数等场景

### 原始函数指针(raw function pointer)

使用的例子：
```
void foo(int a) {
    std::cout << "yuzexing" << a << std::endl;
}
int main() {
    auto test = foo;
    void(*function)(int) = foo;
    typedef void(*functionPtr)(int); // 较为特殊的typedef使用方法
    functionPtr fooPtr = foo;
    function(2);
    test(2);
    fooPtr(2);
}
```

回调函数的例子：
```
#include <iostream>
#include <vector>

void foo(int a) {
    std::cout << "yuzexing" << a << std::endl;
}
void printVectorValue(const std::vector<int> & v, void(*f)(int)) {
    for (auto it = v.begin(); it != v.end(); it++) {
        f(*it);
    }
}
int main() {
    auto print = foo;
    printVectorValue(v, print);
}

```

> auto的用法更常见


### std::function

std::function 是 ``<functional> ``头中提供的一个通用多态函数封装模板，可以封装可调用实体。适用性更广，但是与原始函数指针比，体积更大，调用开销更高。
例如：
```
#include <iostream>
#include <vector>

void foo(int a) {
    std::cout << "yuzexing" << a << std::endl;
}
void printVectorValue(const std::vector<int> & v, std::function<void(int)> f) {
    for (auto it = v.begin(); it != v.end(); it++) {
        f(*it);
    }
}
int main() {
    std::function<void(int)> print = foo;
    printVectorValue(v, print);
}

```

> 补充std::function的开销


[cppreference](https://en.cppreference.com/w/cpp/utility/functional/function)

### lambda表达式

临时函数，不会发生链接，本质是一个"闭包类"。

```
struct lambda {
   lambda(int x) : x(x) { }
   int operator ()(int y) { return x + y; }

private:
   int x;
};

std::function<int(int)> meta_add(int x) {
   lambda add(x);
   return add;
}
```

```
[=](int a) {
    // do sth.
}
```

### lambda表达式的捕获模式

1. [=]表示使用值传递捕获所有自动变量
2. [&]表示使用引用捕获所有自动变量
3. [this]表示捕获当前对象的引用
4. [*this]表示捕获当前对象的拷贝
5. []表示什么都不捕获

``mutable``说明符表示允许修改捕获的目标，允许调用捕获对象的非``const``方法

[cppreference](https://en.cppreference.com/w/cpp/language/lambda)

> 只能捕获自动生命周期的变量，需要考虑被捕获的引用或者对象的生命周期

### lambda表达式的生命周期

捕获变量的生命周期
1. 将捕获的变量，作为成员变量
2. 创建对象，重写调用操作，执行函数

> 所以：捕获的变量的生命周期分为两种：
1. 引用或指针，生命周期由被捕获的变量决定
2. 拷贝复制的变量，由lambda表达式决定

lambda表达式的生命周期：

1. 由持有lambda表达式的对象决定

[stackoverflow](https://stackoverflow.com/questions/7941562/what-is-the-lifetime-of-a-c-lambda-expression)

### 原始函数指针与std::function

原始函数指针直接保存函数地址，只支持普通函数和无捕获的lambda，调用开销低

std:function支持的调用对象：
1. 普通函数
2. lambda
3. 函数对象（待补充）
4. bind表达式（待补充）
5. 成员函数指针（待补充）

> 待补充std::function的特性
