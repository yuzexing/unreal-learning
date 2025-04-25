# C++宏定义和inline的区别

[Reference1](https://stackoverflow.com/questions/1137575/inline-functions-vs-preprocessor-macros?utm_source=chatgpt.com)
[Reference2](https://en.cppreference.com/w/c/language/inline?utm_source=chatgpt.com)

宏定义(#define)与inline函数有些类似，需要细致区分

## 1.inline的作用
**提示**编译器内联展开函数，减少函数调用开销

> - 适用于小型，简单，频繁调用的函数
> - 内联只是一个建议，编译器不一定会去执行展开操作


## 2.inline可能不展开的场景



### 2.1 inline递归函数

## 3.inline绝对不展开的场景

> 在 Visual Studio 的 Debug 模式下（默认 /Od + /Ob0），会完全禁用内联展开
> 在 Release 模式（默认 /O2 + /Ob2），编译器才会对小函数进行自动内联
> 属于我调试时候遇到的问题

[Reference](https://stackoverflow.com/questions/1137575/inline-functions-vs-preprocessor-macros?utm_source=chatgpt.com)



#### 相同点

- 1


#### 不同点

- what
