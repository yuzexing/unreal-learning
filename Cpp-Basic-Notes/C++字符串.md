# C++字符串

### 字符串的本质
是一个数字数组，每个数字可以映射为一个字符展示。常见的映射为ASCII码表，其使用7位或8位二进制数表示128种或256种字符。
```
const char* text = "abc";
char* text2[4] = { 'a', 'b', 'c', '\0' }; // 通过\0或者0表示字符串的结尾
```

### 字符串字面量

所有字面量存储在.rodata只读数据区。对比下面代码：
```
main() {
    const char* p_rodata = "abc";
    char p_stack[] = "abc"; 
}
```

指针p_rodata直接指向常量区字符串字面量"abc"，无法修改
变量p_stack表示常量区的字符串"abc"在栈中副本的地址

### 为什么不允许使用非常量指针指向字符串字面量?

> 避免.rodata只读数据区的字面量常量被修改，这种C++未定义行为可能会导致一些隐性的bug。


### 宽字符

C++11和C++20引入的用于区分不同编码的新数据类型

#### wchar_t (C++11)
表示宽字符
``wchar_t``类型是实现定义的宽字符类型。 在 Microsoft 编译器中，它表示一个 16 位宽字符，用于存储编码为 UTF-16LE 的 Unicode。

#### char8_t、char16_t和char32_t 
char8_t、char16_t和char32_t 分别表示8位、16位和32位宽字符。分别用于存储UTF-8、UTF-16和UTF-32的Unicode。

char8_t是C++20引入的，char16_t和char32_t是C++11引入的。

在C++标准库中，分别有对应的字符串容器来存储宽字符，例如：
1. wchar_t对应std::wstring
2. char8_t对应std::u8string

**作用**：使代码更具有语义性和安全性，避免编码混用导致异常问题

**弊端**：一些三方库甚至标准库不支持宽字符的操作，可以尝试对其进行转化

**变体** LE、BE表示小段存储，BE表示大端存储，例如UTF-16BE，UTF-32LE

> 补充转化参考代码

[参考](https://learn.microsoft.com/zh-cn/cpp/cpp/char-wchar-t-char16-t-char32-t)

### C++14 string_literal

C++14提供的对字面量的操作库，作用：
1. R"test_str" 忽略其中转义字符
2. L"test_str" 表示将其中``char``转为``wchar_t``
3. 补充    using namespace std::string_literals;


> 待补充 无优先级，需要时再看

(参考)[https://en.cppreference.com/w/cpp/language/string_literal]|
(知乎)[https://zhuanlan.zhihu.com/p/17874484194]


### 自己实现的粗略的String类
```
class String {
private:
    char* m_buffer;
    size_t m_size;
public:
    String(const char* c) {
        m_size = strlen(c); // strlen不包含结束符
        m_buffer = new char[m_size + 1];
        /*for (int i = 0; i < m_size + 1; i ++) {
            m_buffer[i] = c[i];
        }*/
        // 或者
        memcpy(m_buffer, c, m_size + 1);
    }
    friend std::ostream& operator << (std::ostream&, const String&);
    String(const String& str) {
        this->m_size = str.m_size;
        m_buffer = new char[m_size + 1];
        memcpy(this->m_buffer, str.m_buffer, str.m_size + 1);
    }

    char& operator[](const size_t index) const {
        return this->m_buffer[index];
    }

    ~String() {
        delete[] m_buffer;
    }
};

std::ostream& operator<<(std::ostream& stream, const String& s) {
    stream << s.m_buffer;
    return stream;
}

int main() {
    String str("yuzexing");
    String str2 = str; // 深拷贝
    str2[1] = '1';
    str[4] = 'z';
    std::cout << str << std::endl;
    std::cout << str2 << std::endl;
}
```

> 对于``std::string``，总是使用 const引用传递，避免拷贝
> 兼容左值和右值


### sizeof(std::string) == 32的原因

因为字符串对短字符串进行了优化：SSO:small string optimization
当字符串大小在16以内时，会使用栈缓冲区作为存储空间；当超过时，会进行堆内存的分配
粗略的定义：
```
template <typename T>
struct basic_string {
    char* begin_;
    size_t size_;
    union {
        size_t capacity_;
        char sso_buffer[16];
    };
};
```
解读这里的union：
1. 节省空间，当SSO触发时，采用sso_buffer存储字符串内容
2. 当长度过长时，union存储长字符串的capacity
3. size_ 始终存放有效字符数


[参考](https://stackoverflow.com/questions/3770781/why-is-sizeofstring-32)


### 字符串或者字符转为整型

场景1: char a = '1';转为int a = 1;
```
int a = '1' - '0'; // int a = 1;
```
原理：'1'的ASCII码值减去'0'的ASCII码值，等于'0'到'1'的距离


场景2：string a = "123"; 转为int a = 123;
```
#include <string>

string a = "123";
int b = std::stoi(a); // C++11
```

stoi的记忆方法：**s**tring **to** **i**nteger

[string-functions](https://learn.microsoft.com/zh-cn/cpp/standard-library/string-functions?view=msvc-170#stoi)

### API

substr(begin, len) // 从begin开始后的len长度的字符串元素
