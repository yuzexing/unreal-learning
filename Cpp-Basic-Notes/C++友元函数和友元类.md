# C++友元函数和友元类

### 友元的作用
1. 访问私有成员和保护成员
2. 重载运算符


### 定义方法
三种定义方法：
1. 全局函数作为友元函数
2. 友元类
3. 成员函数作为友元函数

### 友元函数
目的：允许一个函数访问另一类中私有/保护的成员
```
class A;
class B {
public:
    void printA(A& a);
};

class A {
private:
    int a = 1;
    friend void printA(A& a);
    friend void B::printA(A& a); // 通过完全限定名声明友元函数
};

void printA(A& a) {
    a.a; // ok
}
void B::printA(A& a) {
    a.a; // ok
}
```

> 这里的代码顺序比较重要，需要A的前向声明。


### 友元类
目的：允许一个类访问另一类中私有/保护的成员

```
class A {
friend class B;
private:
    int a = 1;
};
class B {
private:
    int b = 1;
public:
    void printA(A& a) {
        a.a;
    }
};
```

### 注意事项
1. 友元函数不属于类成员函数，不可以被继承，不能被类作用域运算符访问

