# C++数组

### 数组的定义方式

```
main() {
    int array1[5]; // 栈中
    int* array2 = new int[5]; // 堆中
}
```
### 问题一：C++为什么不能在栈中设置动态的数组大小？为什么C语言却可以呢？
```
main() {
    int num = 5;
    int array[num]; // error
}
```
> C++标准要求栈中分配的数组大小，需要在编译期就能计算出其大小。
> 简单的来说，是为了：
> - 性能
> - 方便 \
> 性能方面具体来说(MIPS下)：这么做是因为过程调用栈分配时通常通过帧指针+变量大小作为偏移量来访问栈帧中的变量，当编译时期变量大小已知时，通过一条指令即可访问到指定的栈帧中的变量。例如代码块中：
```
void foo(void)
{
    int a;
    char b[10];
    int c;
```
> 可以通过``FRAME_POINTER + 0``访问a，通过``FRAME_POINTER + 4``来访问b。
> 当变量大小未知时，通常在编译时期无法计算出大小。这对于编译器的开发者来说会更加复杂，因为必须编写出访问``FRAME_POINTER + N``的代码；同时``N``本身也需要存储在某个地方，所以至少需要一次额外的访存来实现这个``FRAME_POINTER + N``的代码。\
[stackoverflow](https://stackoverflow.com/questions/4341570/why-does-a-c-c-compiler-need-know-the-size-of-an-array-at-compile-time)

细节：
- sp是栈顶指针。
- fp是帧指针。


### 问题二：在全局通过new定义的数组，是分配在堆区还是.data区

> 待补充
> 分为静态初始化和动态初始化。

### 问题三：如何在栈上实现动态数组？

> 待补充
