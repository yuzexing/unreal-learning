# C++inline和宏展开的区别

[Reference1](https://stackoverflow.com/questions/1137575/inline-functions-vs-preprocessor-macros?utm_source=chatgpt.com)|
[Reference2](https://en.cppreference.com/w/c/language/inline?utm_source=chatgpt.com)

宏定义展开与inline函数有些类似，需要细致区分

## 1.inline的作用
**提示**编译器内联展开函数，减少函数调用开销

> - 适用于小型，简单，频繁调用的函数
> - 内联只是一个建议，编译器不一定会去执行展开操作


## 2.inline可能不展开的场景

### 2.1递归函数
```
inline int add(int n) {
  if (n <= 0) {
    return 0;
  }
  return add(n - 1) + n;
}
```
原因：递归调用次数未知，编译器可能无法展开，即使展开后，其中的if判断也可能会冲刷流水线，其次增加代码体积\
问题：递归调用什么时候展开？\
回答：可以配置编译器选项，指定展开的最大次数
```
#include <iostream>
#pragma inline_recursion(on) // 开启递归函数内联
#pragma inline_depth(16) // 指定递归函数内联最大深度

_inline int add(int n) {
    if (n <= 0) {
        return 0;
    }
    return add(n - 1) + n;
}

int main() {
    int i = add(3);
    std::cout << i << std::endl;
    return 0;
}
```
同时需要在Inline Function Expansion Ob1或Ob2下递归内联函数
![image](https://github.com/user-attachments/assets/1b833f42-5529-463d-af54-e5f76a1decfe)

虽然按照要求配置了，但是在汇编代码中还是能看到call add，待补充为什么不能像文档里一样内联递归函数呢？
![image](https://github.com/user-attachments/assets/faa146d2-6c6c-40a1-97d5-75961e6ceec6)

[Reference1](https://learn.microsoft.com/en-us/cpp/preprocessor/inline-recursion?view=msvc-170&utm_source=chatgpt.com)|[Reference2](https://stackoverflow.com/questions/190232/can-a-recursive-function-be-inline)


### 2.2 函数体过大
### 2.3 控制流复杂
### 2.4 函数指针调用
1. 运行时才可以确定具体的目标函数，但是有例外
```
inline int add(int a, int b) {
    return a + b;
}

int main() {
    int a = 1, b = 2;
    std::cout << a << std::endl; // 在call add前打断点
    int (*funcPtr)(int, int) = add;
    std::cout << funcPtr(a, b) << std::endl;
    return 0;
}
```
在汇编代码中没有call add\
![image](https://github.com/user-attachments/assets/646ef049-3e8a-4cbe-b619-3b34f3dc1d35)


2. 对比注释inline，且在/Ob1下运行的汇编代码：
```
int add(int a, int b) {
    return a + b;
}

int main() {
    int a = 1, b = 2;
    std::cout << a << std::endl; // 在call add前打断点
    int (*funcPtr)(int, int) = add;
    std::cout << funcPtr(a, b) << std::endl;
    return 0;
}
```
![1745587871714](https://github.com/user-attachments/assets/683aa798-6d78-466a-ba5b-99e45eb5aab9)\
可以看到正常调用add。\

### 2.5 取函数地址
```
inline int add(int a, int b) {
    return a + b;
}

int main() {
    int a = 1, b = 2;
    int (*ptr)(int, int) = &add;
    std::cout << ptr(a, b) << std::endl;
    return 0;
}
```
与2.4同理

3. 对于在编译时确实无法确定地址的指针，无法内联
```
#include <iostream>
#include <random>
#pragma inline_recursion(on)
#pragma inline_depth(16)

inline int add(int a, int b) {
    return a + b;
}

inline int sub(int a, int b) {
    return a - b;
}

int main() {
    int a = 1, b = 2;
    std::cout << a << std::endl; // 在call add前打断点
    std::random_device rd;
    std::mt19937 generator(rd());
    int randNum = generator();
    int (*funcPtr)(int, int);
    if (randNum % 2 == 0) {  // 通过运行时随机数确定目标内联函数
        funcPtr = add;
    }
    else {
        funcPtr = sub;
    }
    std::cout << funcPtr(a, b) << std::endl;
    return 0;
}
```

![1745588497242](https://github.com/user-attachments/assets/54ff8803-ced5-4482-b4b6-1c227e219d24)


### 综上，内联函数展开的必要条件为**在编译时期确定具体的目标函数**





## 3.inline必定不展开的场景

### 3.1 禁用inline function expansion
> 在 Visual Studio 的 Debug 模式下（默认 /Od + /Ob0），会完全禁用内联展开
> 在 Release 模式（默认 /O2 + /Ob2），编译器才会对小函数进行自动内联
> 属于我调试时候遇到的问题

[Reference](https://stackoverflow.com/questions/1137575/inline-functions-vs-preprocessor-macros?utm_source=chatgpt.com)


### 3.2 虚函数
依赖动态绑定，运行时才确定调用目标，无法内联


#### 相同点

- 都可能会将一段代码进行展开


#### 不同点

- **展开时机不同**：宏展开为预编译时期；inline展开在编译时期；
- **应用场景不同**：宏在开发中的功能，逐步被```constexpr```、inline、模板等语言特性代替；而其发挥的场景主要在**头文件保护**，**条件编译**，**调试**等。
- **展开条件不同**：当宏定义的标识符出现在代码中时，就**一定会被展开**；但是inline的展开依赖于**编译器的收益成本评估**来决定是否展开，其次也不一定能展开递归函数等第二节描述的有条件展开的情况。

参考[Replacing the Preprocessor in Modern C++](https://learnmoderncpp.com/2023/12/29/replacing-the-preprocessor-in-modern-c/)


#### 补充细节

1. __forceinline覆盖了收益成本分析，而直接内联（可能无法内联递归函数、虚函数、等等编译时期无法确定的函数）

2. 在C++中，当inline函数不被编译器内联展开时，通过将inline函数标记为弱符号类型，再写入目标文件中，来避免链接过程中的多重定义错误。

3. 符号表中的弱符号只会被保存一份有效的定义，保留的定义可能与目标文件在**链接命令中的顺序**有关（也有听说是随机的）

```
// source1.cc
#include <iostream>
inline double constant() { return 3.14159; }
void print_pi() {
  std::cout << constant() << std::endl;
}

// source2.cc
#include <iostream>
inline double constant() { return 2.71828; }
void print_e() {
  std::cout << constant() << std::endl;
}

// main.cc
void print_e();
void print_pi();

int main() {
  print_e();
  print_pi();
  return 0;
}
```

4. 在C++中如果不同inline的定义不同，就会存在意想不到的问题，例如上述代码的链接顺序不同，会导致最终输出结果不同：

```
$ g++ source1.cc -c -O0
$ g++ source2.cc -c -O0
$ g++ main.cc -c -O0
$
$ g++ main.o source1.o source2.o -o main
$ ./main
3.14159
3.14159
$
$ g++ main.o source2.o source1.o -o main
$ ./main
2.71828
2.71828
```
5. 通过将inline函数声明为静态内联函数，将其符号类型改为local，并且为每一个#include的翻译单元创建一份代码副本，可以解决该问题
```
// static
inline static double constant() { return 2.71828; }
```

6. 或者通过命名空间来创建多个弱符号，将弱符号冲突解决，实现保留所有定义的弱符号：

```
// named namespace
namespace source1_private_ns {
  inline double constant() { return 2.71828; }
}

// anonymous namespace
namespace {
  inline double constant() { return 2.71828; }
}
```



参考[Dangers of linking inline functions](https://gudok.xyz/inline/?utm_source=chatgpt.com)