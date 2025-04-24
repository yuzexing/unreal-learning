# C++预处理过程

#### 预处理过程做了什么？
> 识别文件中所有预处理指令进行翻译，将翻译单元传递给编译器。

#### 预处理指令有哪些？
> - 所有由#开头的指令
> - 特殊的情况有：Module的import/export指令也属于预处理指令（是头文件某些场景下的的替代方案，待补充）

#### 预处理器的常见功能有哪些：
> - **条件编译**源文件的一部分内容，可以通过指令控制```#if, #ifdef, #ifndef, #else, #elif, #elifdef, #elifndef，#endif``` 
> - **宏展开**，例如指令：```#define #undef```
> - **文件包含**，例如：```#include``` #pragma once
> - **产生错误** 例如：```#error``` or ```#warning```,(C++23)

Tip：预处理指令不能是来自于宏指令的展开例如：
```
#define EMPTY
EMPTY   #   include <file.h> // not a preprocessing directive
```

#### 对预处理器的控制
> - 通过#pragma 或者 _Pragma 指令进行控制
> - 例如 ```#pragma once```


#### Module的import/export的应用场景是什么？与头文件的区别有哪些？

> 待补充优先级五

[Reference](https://en.cppreference.com/w/cpp/preprocessor)

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



