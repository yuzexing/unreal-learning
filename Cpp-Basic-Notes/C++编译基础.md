# C++编译基础

##### 1.1：C++配置项中Debug和Release有什么区别？

> 默认的Release状态下：
> 1. Optimization：Maximum Optimization (Favor Speed) (/O2)，开启模块内部的代码优化，速度优先
> 2. Enable Intrinsic Function：使用内部函数(intrinsic fucntion)加快生成速度，代价是代码体积会更大
> 3. Enables whole program optimization：使用全局代码优化，将代码生成推迟至链接过程

Reference：[Stackoverflow](https://stackoverflow.com/questions/13938089/whats-c-optimization-whole-program-optimizatoin-in-visual-studio)|[learn-microsoft](https://learn.microsoft.com/en-us/cpp/build/reference/gl-whole-program-optimization?view=msvc-170)|[Intrinsic function in wikipedia](https://en.wikipedia.org/wiki/Intrinsic_function)

##### 1.2：Maximum Optimization (Favor Speed) (/O2) 做了什么？（待补充，优先级四）

##### 1.3：Intrinsic Function 是什么？

##### 1.4：whole program optimization 将代码生成推迟到链接过程，那么编译阶段输出的结果还是.o文件吗？（待补充，优先级四）

