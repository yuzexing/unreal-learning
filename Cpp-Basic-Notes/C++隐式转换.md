# C++隐式转换


C++中编译器允许一次隐式类型转换，例如：
```
class A {
public:
    std::string m_s;
    int m_b;
    A() = default;
    A(std::string s):
        m_s(s), m_b(0){}
    A(int b) : m_s(""), m_b(b) {}
};
void printA(const A& a) {}
int main() {
    std::string s = "abc"; // char [] 隐式转换为 std::string
    A a = 1; // int 隐式转换为 A(1) 等价于 A a(1);
    printA(1); // = printA(A(1))
    printA("abc"); // error 需要两次隐式转换
}
```

### 如何避免隐式转换？

> 使用``explicit ``关键字修饰方法
```
class A {
public:
    int m_b;
    A() = default;
    explicit A(int b) : m_b(b) {}
};
explicit void printA(const A& a) {}
int main() {
    A a = 1; // error
    printA(1) // error 
}
```
使用``explicit``避免模糊的方法调用和类创建，从而避免意料之外的情况产生