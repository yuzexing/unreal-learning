# C++预处理过程

##### 预处理过程做了什么？
> 将预处理

##### 预处理指令有哪些？
> - 所有由#开头的指令
> - 特殊的情况有：Module的import/export指令也属于预处理指令（是头文件某些场景下的的替代方案）

##### 预处理器的常见功能有哪些：
> - 条件编译源文件的一部分内容，可以通过指令控制```#if, #ifdef, #ifndef, #else, #elif, #elifdef, #elifndef，#endif``` 
> - 替换#include
> - 文件包含#pragma once

Tip：预处理指令不能是来自于宏指令的展开例如：
```
#define EMPTY
EMPTY   #   include <file.h> // not a preprocessing directive
```

##### Module的import/export的应用场景是什么？与头文件的区别有哪些？

> 待补充优先级五

[Reference](https://en.cppreference.com/w/cpp/preprocessor)

##### 宏指令和inline的区别

> 待补充


