# C++引用和指针的区别

指针和引用所提供的功能是相同的，但是写法用法上有些细微不同，引用是指针的语法糖。

### 区别

- 指针是一个变量，占据实际的内存空间，可以是空的；
- 引用不是一个变量，不一定占据内存空间，引用必须指向已经存在的变量，引用更像一种"别名"，会被编译器有条件的优化。

最主要的区别在于参数传递，尽量使用引用作为参数进行传递，因为：
0. 引用对函数调用者透明，指针则不透明
1. 引用作为参数传递不会为空指针，除非需要空指针存在的情况，可以使用指针参数
2. 引用一旦创建，在其生命周期只会指向一个对象，可以避免重新修改引用
3. 指针无法对临时变量取地址，例如以下的代码：
```
#include <iostream>
struct Temp {
};

void f(const Temp* t) {}

int main() {
    f(&Temp());
    return 0;
}
// error C2102: '&' requires l-value
```
但是引用可以做到，(条件是增加const，因为对外部临时变量的修改是没有意义的，同时扩展临时变量的生命周期以匹配引用)：
```
#include <iostream>
struct Temp {
};

void f(const Temp& t) {}

int main() {
    f(Temp());
    return 0;
}
```
4. 重载操作符时，无法对指针对象进行重载，例如``string s = &str1 + &str2;``。但是引用可以做到
待补充，优先级五


综上，引用使用更方便，产生bug的概率也更新，是官方推荐的参数传递做法

[cppreference](https://en.cppreference.com/w/cpp/language/reference)|
[stackoverflow](https://stackoverflow.com/questions/334856/are-there-benefits-of-passing-by-pointer-over-passing-by-reference-in-c/334944)

### 右值引用
不太明白
```
const int& c = 6;
```
> 待补充 

### 左值 右值 左值引用 右值引用

> 补充临时变量生命周期，为什么会被const 引用扩展？

> 待补充
[move](https://en.cppreference.com/w/cpp/utility/move)|
[zhihu](https://zhuanlan.zhihu.com/p/335994370)|
[wiki](https://en.wikipedia.org/wiki/Reference_%28C%2B%2B%29?)
