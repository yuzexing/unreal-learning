# C++构造函数


## 默认构造
```
class A {
public:
  A();            // 显式声明的默认构造
  A(int x = 0);   // 参数都有默认值，也可作为默认构造
};
```
## 参数化构造
```
class B {
public:
  B(int x, double y);
};
```
## 拷贝构造
```
class C {
public:
  C(const C& other);
};
```

## 移动构造
```
class D {
public:
  D(D&& other) noexcept;
};
```
## 转换构造/隐式构造
```
class E {
public:
  E(int x);    // 可以把 int 隐式转换为 E
};
E e = 42;      // 等价于 E e(42);
```
> 1. 未使用``explicit``声明的构造函数
> 2. 只有一个参数，或者其他参数有默认值
满足以上两个条件，可以完成隐式类型转换

## 委托构造
一个构造函数可以调用同一个类中另一个构造函数来复用初始化逻辑。先执行被委托的构造函数，再执行当前构造的内容
```
class F {
public:
  F(): F(0) { }        // 委托给下面这个
  F(int x) { /* ... */ }
};
```

## 继承构造
在派生类中直接引入基类的构造函数，避免重复写转发代码
```
struct Base {
  Base(int);
};
struct Derived : Base {
  using Base::Base;   // 将 Base 的构造函数
};

```

## 常量表达式构造

## C++拷贝构造函数

### 复制的场景

任何等号复制的情况下，总是复制。（除了引用，因为引用只是别名，引用可能会被编译器优化）

基本类型复制:
```
int main() {
    int a = 2;
    int b = a; // 复制a的值到b中
    b = 3; // 复制3到b中
}
```
对象类型的复制：
```
struct Vector2 {
    float x,y;
    Vector2(int t_x, int t_y): x(t_x), y(t_y) {
        std::cout << "构造" << std::endl;
    }
    Vector2(const Vector2& v): x(v.x), y(v.y) {
        std::cout << "拷贝构造" << std::endl;
    }
};
int main() {
    Vector2 a = { 1, 2 };
    Vector2 b = a; // 通过拷贝构造函数创建新Vector对象b
    Vector2& fake = a; // 并非复制，在开启编译优化的情况下，对fake的使用可能被替换为操作a

    Vector2* c = new Vector2(1, 2); // 堆内存创建对象，获取指针
    Vector2* d = c; // 指针复制，两个指针指向同一个地址
}
```




