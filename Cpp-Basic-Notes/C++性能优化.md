# C++性能优化

### 多线程优化
，std::async


### 字符串优化

性能在长字符串的分割、创建等，会进行堆内存的分配，堆内存的分配较为消耗性能。

C++17，string_view
本质一个指针和一个大小，获取内存中字符串的一段内容（一个窗口视图）

C++17 Debug模式下：
```
#include <iostream>
using namespace std;

static int s_Allocate_Count = 0;

void* operator new(size_t size) {
    s_Allocate_Count++;
    std::cout << "allocate:" << s_Allocate_Count << std::endl;
    return malloc(size);
}

#define PRINT_TYPE 0

#if PRINT_TYPE == 0
void printName(const std::string& firstName, const std::string& secondName) {
    cout << firstName << secondName << endl;
}
#else
void printName(std::string_view firstName, std::string_view secondName) {
    cout << firstName << secondName << endl;
}
#endif

int main() {
#if PRINT_TYPE == 1
    const char* str = "yu zexing"; // 0次内存分配
    std::string_view firstName(str, 2);
    std::string_view secondName(str + 3, 6);
#elif PRINT_TYPE == 2
    std::string str = "yu zexing"; // 1次内存分配
    std::string_view firstName(str.c_str(), 2);
    std::string_view secondName(str.c_str() + 3, 6);
#else
    std::string str = "yu zexing"; // 3次内存分配
    std::string firstName = str.substr(0, 2);
    std::string secondName = str.substr(4, 6);
#endif
    printName(firstName, secondName);
}
```

C++17以下，可以使用裸指针读取字符串的一段内容。


### 小字符串优化

当处于Release模式下，C++会对短字符串进行优化，存入栈缓冲区中，而不是进行堆内存的分配。

```
static constexpr size_type _BUF_SIZE = 16 / sizeof(value_type) < 1 ? 1 : 16 / sizeof(value_type);
static constexpr size_type _Small_string_capacity = _Scary_val::_Small_string_capacity;

-----allocate------
if (_Count <= _Small_string_capacity) {
    _My_data._Mysize = _Count;
    _My_data._Myres  = _Small_string_capacity;

    if constexpr (_Strat == _Construct_strategy::_From_char) {
        _Traits::assign(_My_data._Bx._Buf, _Count, _Arg);  // 将char _Arg 复制 _count到buf中
        _Traits::assign(_My_data._Bx._Buf[_Count], _Elem()); // 写入终止符
    } else if constexpr (_Strat == _Construct_strategy::_From_ptr) {
        _Traits::copy(_My_data._Bx._Buf, _Arg, _Count); // 将_Arg指向的char数组复制到buf中，长度为count
        _Traits::assign(_My_data._Bx._Buf[_Count], _Elem()); // 写入终止符
    } else { // _Strat == _Construct_strategy::_From_string
#ifdef _INSERT_STRING_ANNOTATION
        _Traits::copy(_My_data._Bx._Buf, _Arg, _Count + 1); // 完整拷贝，+1包括终止符
#else // ^^^ _INSERT_STRING_ANNOTATION / !_INSERT_STRING_ANNOTATION vvv
        _Traits::copy(_My_data._Bx._Buf, _Arg, _BUF_SIZE);  // 拷贝整个buf的长度
#endif // !_INSERT_STRING_ANNOTATION
    }

    _Proxy._Release();
    return;
}
```


### 为什么字符串需要进行堆内存的分配

> 字符串长度可变，C++使用堆内存来分配动态内存


### 当栈中字符串增加超过栈缓冲区的大小时，会发生什么？

在C++17，Vs2022，msvm编译器下，栈缓冲区大小为16。
当我们创建长度为15的字符串时，（终止符1位，共16位）再push_back一个字符时，将数据拷贝并再堆中进行分配。

```
#include <iostream>


void* operator new(size_t size) {
	std::cout << "allocate:" << size << std::endl; // push时打印
	return malloc(size);
}

int main() {
	std::string str = "qqewqqqqqqqqqqq"; // 长度15 ，栈地址0x000000F260BAFDE8
	std::cout << str << std::endl;
	str.push_back('a'); // 拷贝并在堆中创建字符串 
	std::cout << str << std::endl;
}
```

### 空基类优化(EBCO)

Empty Base Class Optimization：

compressed_pair

> 待补充 优先级五
