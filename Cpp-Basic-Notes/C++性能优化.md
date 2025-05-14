# C++性能优化

判断小于而不是小于等于，汇编代码中小于可以通过一行指令得出，而小于等于需要两条指令

### 多线程优化
，std::async


### 字符串优化

性能在字符串的分割、创建等，会进行堆内存的分配

C++17，string_view
本质一个指针和一个大小，获取内存中字符串的一段内容（一个窗口视图）

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

C++17以下，使用裸指针读取字符串的一段内容。


