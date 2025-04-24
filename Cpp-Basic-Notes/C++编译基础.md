# C++编译基础

#### 1.1、C++配置项中Debug和Release有什么区别？

> 默认的Release状态下：
> 1. Optimization：Maximum Optimization (Favor Speed) (/O2)，开启模块内部的代码优化，速度优先
> 2. Enable Intrinsic Function：使用内部函数(intrinsic fucntion)加快生成速度，代价是代码体积会更大
> 3. Enables whole program optimization：使用全局代码优化，将代码生成推迟至链接过程

Reference：[Stackoverflow](https://stackoverflow.com/questions/13938089/whats-c-optimization-whole-program-optimizatoin-in-visual-studio)|[learn-microsoft](https://learn.microsoft.com/en-us/cpp/build/reference/gl-whole-program-optimization?view=msvc-170)|[Intrinsic function in wikipedia](https://en.wikipedia.org/wiki/Intrinsic_function)

#### 1.2、Maximum Optimization (Favor Speed) (/O2) 做了什么？（待补充，优先级四）

#### 1.3、Intrinsic Function (内部函数)是什么，与内联函数的关系是什么？
> 内部函数是由编译器生成的函数，通常使用内联方式插入该函数的代码，避免函数调用的开销。
> 区别：
> 1. 内部函数通常比内联函数更好优化，因为编译器中的优化程序对内部函数拥有更多先验知识。
> 2. 内部函数由编译器提供，C++程序员不可编写。

#### 1.4、Intrinsic Function (内部函数) 识别和生成的时机？

> - 在语义分析中识别。
> - 生成的时机可以简单的理解为在中间代码生成和中间代码优化阶段。

#### 1.5、whole program optimization 将代码生成推迟到链接过程，那么编译阶段输出的结果还是.o文件吗？（待补充，优先级四）

#### 2.1、函数调用的开销有哪些？

> 1. 创建栈帧(stack frame)
> 2. 函数传递参数的拷贝（参数6个内使用寄存器传递，超过6个的部分使用的栈传递）
> 3. 保存现场和恢复现场的开销（调用方法前的PC，寄存器数据入栈 以及恢复）
> 4. 冲刷指令流水线


