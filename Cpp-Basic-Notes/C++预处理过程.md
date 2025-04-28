# C++预处理过程

[Reference](https://en.cppreference.com/w/cpp/preprocessor)

#### 预处理过程做了什么？
> 识别文件中所有预处理指令进行翻译，将翻译单元传递给编译器。

#### 预处理指令有哪些？
> - 所有由#开头的指令
> - 特殊的情况有：Module的import/export指令也属于预处理指令（是头文件某些场景下的的替代方案，待补充）

#### 预处理器的常见功能有哪些：
> - **条件编译**源文件的一部分内容，可以通过指令控制```#if, #ifdef, #ifndef, #else, #elif, #elifdef, #elifndef，#endif``` 
> - **宏展开**，例如指令：```#define #undef```定义了宏，然后在使用的宏地方用宏定义进行替换
> - **文件包含**，例如：```#include```
> - **产生错误** 例如：```#error``` or ```#warning```,(C++23)

Tip：预处理指令不能是来自于**宏展开**例如：
```
#define EMPTY #include <file.h> 
EMPTY // not a include preprocessing directive
```
> 原因：减少复杂度，提高可维护性
> - tips_1:预处理器虽然会在宏展开后的代码再次扫描并进行宏展开，但是并不会继续识别其中预处理指令。
> - tips_2:预处理器在处理宏指令时，是遇到一条立刻处理一条，如果遇到宏展开，那么会对展开内容再次进行扫描，识别是否需要继续展开的定义（递归展开），但是会避免无线递归的情况（例如宏展开中包含自身）。
> - 这里被宏递归展开绕晕了，等以后有了实践经验再理解（也就是为什么#include的文件可以继续识别其中的#include或者其他宏指令，但是宏展开#define中包含宏指令就不行）

[Reference1](https://port70.net/~nsz/c/c11/n1570.html#6.10.3.4p3) | [Reference2](https://gcc.gnu.org/onlinedocs/cpp/The-preprocessing-language.html?utm_source=chatgpt.com)


#### 对预处理器的控制
> - 通过#pragma 或者 _Pragma 指令进行控制
> - 例如 ```#pragma once```

#### Module的import/export的应用场景是什么？与头文件的区别有哪些？

> 待补充优先级五

#### 宏定义的作用域

> 宏定义的作用域为文件作用域，无块级作用域，但是可以通过```#undef```控制作用域
```
void func() {
  #define VALUE 10
  #undef VALUE
  printf("%d\n", VALUE); // 编译报错
}
```
特殊的情况一：
> - 在Property Pages -> Configuration Propterties -> C/C++ -> Preprocessor 中定义的宏
> - ![image](https://github.com/user-attachments/assets/0e3e71ea-6d67-43ca-ba03-53eba34c32ff)
> - 相当于在每个翻译单元顶部添加#define x，属于文件作用域

#### 宏的应用场景

> 常用于进行调试，有较多高级用法例如自定义关键字new用来追踪变量的内存分配情况，待补充优先级五

#### 宏中的特殊符号
> 符号#: 将内容转化为字符串表示
```
#define A(x) #x
int main() {
    std::cout << A("10") << std::endl; //output "10"
}
```
> 符号##: 将内容进行连接
```
#define B(x, y) x##y
int main() {
    std::cout << B(1, 2) << std::endl; //output 12
}
```
> 符号/：将空格转义，允许宏定义跨行
```
#define DOIT(m, n) for(int i = 0; i < (n); ++i) \
{ \
m += i; \
}
```

#### 头文件文件保护符

场景：防止头文件被多次包含到同一个翻译单元

多次包含相同头文件到同一个翻译单元可能产生的问题：
1. 多重定义（如果在.h文件中进行了定义，则会产生多重定义错误，虽然规范不建议这么做）
2. 头文件互相include导致的错误
3. 增加预处理过程的时间（预处理器是遇到一条宏指令就处理一条还是识别并扫描所有的指令，再统一处理？）

文件保护符的实现方法：
1. 宏定义
```
// log.h 头文件中
#ifndef _LOG_H
#define _LOG_H
struct Player {};
#endif
```

2. 预处理指令
```
#pragma once
struct Player {};
```

#### 在#include 后，使用尖括号和双引号的区别是什么？

> 回答：最大区别在于预处理器的搜索路径：
> - 对于#include <file.h>，预处理器的默认行为是搜索系统路径，而具体的搜索路径取决于目标系统、编译器的配置以及安装位置。可以通过指令修改搜索的系统路径。
> - 对于#include "file.h"，默认情况下，预处理器首先查询file.h所在目录，然后在查询系统目录。例如：main.cpp路径为"F:\Cpp_store\Project1\src\main.cpp"下，那么预处理器则先查询"F:\Cpp_store\Project1\src\"目录中是否包含"file.h"，如果没有查找到，则查找系统目录。
> - 一般实践为：<>用于包含系统库，""用于包含用户定义的头文件。

[Reference1](https://gcc.gnu.org/onlinedocs/cpp/Search-Path.html)|
[Reference2](https://stackoverflow.com/questions/21593/what-is-the-difference-between-include-filename-and-include-filename)

#### GCC是什么？MSVS是什么？

> 待补充 优先级五