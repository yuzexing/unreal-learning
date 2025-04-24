# C++预处理过程

##### 预处理过程做了什么？
> 识别文件中所有预处理指令进行翻译，将翻译单元传递给编译器。

##### 预处理指令有哪些？
> - 所有由#开头的指令
> - 特殊的情况有：Module的import/export指令也属于预处理指令（是头文件某些场景下的的替代方案）

##### 预处理器的常见功能有哪些：
> - **条件编译**源文件的一部分内容，可以通过指令控制```#if, #ifdef, #ifndef, #else, #elif, #elifdef, #elifndef，#endif``` 
> - **宏展开**，例如指令：```#define #undef```
> - **文件包含**，例如：```#include``` #pragma once
> - **产生错误** 例如：```#error``` or ```#warning```,(C++23)

Tip：预处理指令不能是来自于宏指令的展开例如：
```
#define EMPTY
EMPTY   #   include <file.h> // not a preprocessing directive
```

##### 对预处理器的控制
> - 通过#pragma 或者 _Pragma 指令进行控制
> - 例如 ```#pragma once```


##### Module的import/export的应用场景是什么？与头文件的区别有哪些？

> 待补充优先级五

[Reference](https://en.cppreference.com/w/cpp/preprocessor)

##### 宏指令和inline的区别

> 待补充


