# C++中处理多返回值


### 常见的方式

0. strcut/class
1. std::pair
2. std::tuple
3. std::array
4. std::tie
5. std::vector
6. 将指针或引用作为参数传入
7. 结构化绑定（C++17）

[cppreference](https://en.cppreference.com/w/cpp/language/structured_binding)

### 结构体

优点：
1. 更强的语义性
2. 允许多种类型数据组合
缺点：需要额外的结构体/类定义
代码：
```
struct Entity {
    int a;
    std::string s;
};
Entity get0() {
    return Entity{ 1, "2" }; // 统一初始化（列表初始化）
}
Entity get1() {
    return { 1, "2" }; // 聚合类型的
}
```

配合的C++特性：
1. 返回值优化 (Return Value Optimization)(RVO)
2. 统一初始化
3. 聚合类成员初始化


> {}作为统一初始化的符号，为了避免编译器Most Vexing Parse的问题，例如，在一定情况下编译器可能无法分辨是初始化还是申明函数
```
void f(double my_dbl) {
  int i(int(my_dbl));
}
```
其中``int i(int(my_dbl));``至少有两种解释：
1. 声明int型变量，以强制类型转换的double作为初始化参数
2. 申明``int i (int my_dbl)``，C语言中允许参数被多余的括号包裹

[zhihu](https://zhuanlan.zhihu.com/p/391558669)

### Return Value Optimization

直接在调用者的栈帧上构造返回的对象，避免了拷贝构造/移动的操作。

> 待补充实现原理，优先级五

#### RVO限制
1. 多个返回点，编译器无法判断在哪里返回
2. 返回不同的对象，编译器无法判断哪个对象被返回
3. 只能作用于右值（临时变量）

衍生出NRVO，可以对左值进行RVO（C++11）
同时还可以讨论C++17的保证的复制消除（待补充）

> NRVO (Named Return Value Optimization) 命名返回优化


### pair/tuple/array

这三个可以放在一起讨论，因为都是专门用于一组数据的关联。缺点都是语义性较差
例子：
```

std::pair<int, float> get0() {
    return std::make_pair<int, float>(1, 1.0f);
}
std::tuple<int, float, std::string> get1() {
    return std::make_tuple<int, float, std::string>{1, 1.0f, "123"};
}
std::array<int, 4> get2() {
    return std::array<int, 4>{1, 1, 1, 1};
}
// 也都可以通过{1,1,1,1}省略较长的声明
```
访问方式：
```
std::pair<int, float> p = get0();
p.first;
p.second;

using tName = std::tuple<int, float, std::string>;
tName t = get1();
int a = std::get<0>(t);

std::array<int, 4> arr = get2();
arr[0];


std::get<N>() // get是可以适用于获取这三种数据的方法
```

性能考虑：
pair和tuple在``-flto``下，性能几乎差不多，但仍然受编译器版本和上下文的影响。

(stackoverflow)[https://stackoverflow.com/questions/26863852/why-is-stdpair-faster-than-stdtuple]


### std::tie

一种语义性更强的读取数据的方式
```
int i;
float f;
std::string str;
std::tie(i, f, str) = get1();
std::cout << i << f << str << std::endl;
```

弊端是需要额外的数据声明

### std::vector

与std::array比，更适合返回动态数据

### 结构化绑定

C++17下，更现代化的获取元组数据的方式

```
// auto [a, b] = get0();
// auto [a, b, c] = get1();
auto [a, b, c, d] = get2();
```

[cppreference](https://en.cppreference.com/w/cpp/language/structured_binding)