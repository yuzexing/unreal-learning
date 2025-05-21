# C++左值、右值和移动语义

### 什么是左值和右值、左值引用和右值引用

- 左值是具有存储支持的变量
- 右值是临时值或即将被销毁的对象，常用来传递数据
- 左值引用仅接受左值，**除非是const修饰**
- 右值引用仅接受右值，主要用于实现移动语义与完美转发

我的理解：
0. 左值用于存储资源，右值是被获取的资源，也就是资源本身。
1. 字面量都是右值，函数返回值是右值
2. 使用const修饰的左值引用接受右值时，编译器会创建一个左值，并使用该左值为引用。
```
int &a = 1; // error
const int &a = 1; // ok 
// -----等价于------
int temp = 1;
const int& a = temp;
```

### 移动语义

移动语义std::move，可以将一个左值/左值引用转换为右值引用

移动语义表示允许将该变量a的资源，转移到其他对象。

我的理解：移动语义表示将该对象标记为临时资源。

[cppreference](https://en.cppreference.com/w/cpp/utility/move)

### 右值引用

也是引用，但是该引用只能指向右值，同时表示该对象中的资源可以被移动。

右值引用，本身是一个左值

### 引用折叠

又称万能引用，规则如下：
1. && && 等于 &&；如T&& && -> T&&
2. 只要包含&，则是左值；例如T&& & –> T&

```
&  &  -> &
&  && -> &
&& &  -> &
&& && -> &&
```

从理解的角度出发：左值引用会传播

[zhihu](https://zhuanlan.zhihu.com/p/645328162)

### 移动赋值和赋值操作符的区别
- 移动赋值是在已有实例的基础上进行移动操作
- 赋值操作符可能会触发移动构造，创造新一个新的实例


### 样例代码
```
class String {
private:
    char* m_buffer;
    size_t m_size;
public:
    String(): m_buffer(nullptr), m_size(0) {
    }
    String(const char* c) {
        m_size = strlen(c); // strlen不包含结束符
        m_buffer = new char[m_size + 1];
        memcpy(m_buffer, c, m_size + 1);
        std::cout << "construct" << std::endl;
    }
    String(const String& str) {
        this->m_size = str.m_size;
        m_buffer = new char[m_size + 1];
        memcpy(this->m_buffer, str.m_buffer, str.m_size + 1);
        std::cout << "copy" << std::endl;
    }
    // 移动构造
    String(String&& str) noexcept {
        std::cout << "move" << std::endl;
        this->m_size = str.m_size;
        this->m_buffer = str.m_buffer;
        str.m_buffer = nullptr;
        str.m_size = 0;
    }

    String& operator=(String&& string) noexcept {
        if (&string == this) {
            return *this;
        }
        delete[] this->m_buffer;
        this->m_buffer = string.m_buffer;
        this->m_size = string.m_size;
        string.m_buffer = nullptr;
        string.m_size = 0;
        return *this;
    }

    char& operator[](const size_t index) const {
        return this->m_buffer[index];
    }
    void Print() {
        for (uint32_t i = 0; i < m_size; ++i)
            printf("%c", m_buffer[i]);

        printf("\n");
    }
    ~String() {
        delete[] m_buffer;
        std::cout << "destruct" << std::endl;
    }
};

class Entity {
public:
    Entity(const String& name)
        : m_Name(name) {
    }
     Entity(String&& name)
         : m_Name(std::move(name)) {}
    void PrintName() { m_Name.Print(); }
private:
    String m_Name;
};

int main() {
    Entity e(String("yuzexing")); // 没有copy 但是有析构
    e.PrintName();
    String apple = "apple";
    apple.Print(); // apple
    String temp;
    temp.Print(); // 空
    temp = std::move(apple);
    apple.Print(); // 空
    temp.Print();  // apple
    std::cin.get();
}
```

### 为什么临时变量会被回收啊

临时变量在创建他们的完整表达式求值的最后一步被销毁，除非：
1. 绑定临时对象的引用来延长生命周期

[lifetime](https://en.cppreference.com/w/cpp/language/lifetime)

### std::move 可以移动栈中的数据吗

> 待补充

### C++三法则

当对一个类实现了析构函数后，需要正确的实现拷贝构造函数和赋值运算符

### C++五法则

在三法则的基础上，加上移动构造函数和移动赋值运算符的实现

### 移动语义和完美转发的对比

移动语义
1. 本质是强制类型转换，即``static_cast<T&&>(x)``
2. 只能转换为右值

完美转发：
1. 编译时期的模板技术，依赖于类型推导
2. 保持左值于右值的属性不变，常配合万能引用使用
3. 在右值引用转发时，与移动语义操作相同：``static_cast<T&&>(x)``


源码分析：
```
template <class _Ty>
constexpr remove_reference_t<_Ty>&& move(_Ty&& _Arg) noexcept {
    return static_cast<remove_reference_t<_Ty>&&>(_Arg);
}
```
其中：remove_reference_t是将T的引用剥离，只保留类型

```
template< class T >
using remove_reference_t = typename remove_reference<T>::type;
```
对于为什么要添加``typename``，是为了告诉编译器``remove_reference<T>::type``是一个类型，而不是成员变量


### 右值参数的优势：

对于非基础类型：
1. 允许调用移动构造函数，避免额外的拷贝
2. 对于右值引用

不论是完美转发还是移动语义，将参数转化为右值的原因

