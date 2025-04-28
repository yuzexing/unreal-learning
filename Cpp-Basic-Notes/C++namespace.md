# C++namespace

[Reference](https://learn.microsoft.com/zh-cn/cpp/cpp/namespaces-cpp?view=msvc-170)

### 什么是namespace
命名空间(namespace)是一个声明性的区域，为其内部的标识符提供一个作用域，可以用来**防止命名冲突**。命名空间内的标识符彼此可见。在命名空间之外，可以通过每个标识的完全限定名称来访问成员，也可以通过using声明和using指令来访问。


### 命名空间的声明
```
namespace ContosoData
{
    class ObjectManager
    {
    public:
        void DoSomething() {}
    };
    void Func(ObjectManager) {}
}
```

### 用法

1. 使用完全限定名称：
```
ContosoData::ObjectManager mgr;
mgr.DoSomething();
ContosoData::Func(mgr);
```
2. 使用using声明，将一个命名空间的标识符引入当前作用域：
```
using ContosoData::ObjectManager;
ObjectManager mgr;
mgr.DoSomething();
```

3. 使用using指令，将一个命名空间的所有标识符引入当前作用域：
```
using namespace ContosoData;

ObjectManager mgr;
mgr.DoSomething();
Func(mgr);
```

### using指令和using声明使用的场景
- 使用命名空间内的一到两个标识符时，考虑使用using声明
- 也可以全部使用完全限定名称，提高可读性，避免隐藏的命名冲突问题

### 命名空间中的声明与定义分离的情况
声明和定义分离时，必须使用完全限定名称进行定义
```
// contosoData.h 中进行声明
#pragma once
namespace ContosoDataServer
{
    void Foo();
    int Bar();
}
```
```
#include "contosodata.h" // contosodata.cpp进行定义
using namespace ContosoDataServer; // 即使使用using指令，也需要使用完全限定名称进行定义

void ContosoDataServer::Foo() // use fully-qualified name here
{
   // no qualification needed for Bar()
   Bar();
}

int ContosoDataServer::Bar(){return 0;}
```

### 命名空间的合并
编译器会将同一翻译单元内的同一命名空间合并为一个命名空间，甚至多个翻译单元的同名命名空间也会被合并到同一个namespace?
```
// main.cpp
namespace sp {
	int a;
}
namespace sp {
	int b = 10;
}

using namespace sp;
// 包含 sp::a 和 sp::b
```

[Reference](https://chatgpt.com/c/680f6068-b798-800a-b0ba-16fe3f30edad)


### 全局命名空间
如果未在显式命名空间中声明某个标识符，则该标识符属于隐式全局命名空间的一部分。 通常情况下，如果可能，尝试**避免在全局范围内进行声明**，入口点 main 函数除外，它必须位于全局命名空间中。例如：
```
// main.cpp

int a = 1; // 未在显式命名空间中声明标识符a，属于全局命名空间

int main() {
    ::a = 2; // 使用全局限定名称访问a
    return 0;
}
```

### 补充：
### 完全限定名称

[Reference1](https://stackoverflow.com/questions/56273345/what-is-a-fully-qualified-name)|
[Reference2](https://en.wikipedia.org/wiki/Fully_qualified_name)|

### 嵌套命名空间


### 限定查询和非限定查询

原理：查看符号表

### 问题一：命名空间内的变量是局部变量还是全局变量？如果是局部变量那么作用域是什么呢？
> 回答：全局变量。命名空间不改变变量的作用域，只是提供了作用域隔离的作用。

### 问题二：使用using声明或者using指令与当前翻译单元的变量命名冲突时，会怎么样？
> 回答：与局部变量命名冲突时，隐藏命名空间的变量，与全局变量命名相同时，出错。举例：
```
#include <iostream>
namespace sp {
	int a = 1;
}
using namespace sp;
int main() {
    int a = 10;
	std::cout << a << std::endl; // print 10
    return 0;
}

// 全局变量重名则报错
#include <iostream>
namespace sp {
	int a;
}
using namespace sp;
int a = 10;
int main() {
	std::cout << a << std::endl; // E0266 a is ambiguous
    return 0;
}
```
### 问题三：命名空间的合并中，编译器是不知道其他翻译单元的内容，为什么能合并不同翻译单元中的同名命名空间的内容？
> 来源
```A namespace can be declared in multiple blocks in a single file, and in multiple files. The compiler joins the parts together during preprocessing and the resulting namespace contains all the members declared in all the parts. An example of this is the std namespace which is declared in each of the header files in the standard library```
> 待补充 查看一下STL源码看看。。\
[source](https://learn.microsoft.com/en-us/cpp/cpp/namespaces-cpp?view=msvc-170)

### 什么情况可以用namespace
1. 较小的作用域，例如if语句中大量使用了某个类


### 什么情况下不可以用namespace
1. 使用了多个方法或者类会重复的公共库时
2. 头文件中



### 为什么最好不用namespace

1.阅读性(看到代码就知道这个类来自于哪个库)
2.对于
2.代码bug调试
3.举例