# C++中处理可选返回值

### C++17std::optional实现

实例代码：
```
#include "iostream"
#include "optional"
std::optional<int> goHome(int a) {
    if (a == 1) {
        return 0;
    }
    return {};
}
std::optional<int> option = goHome(1);
if (option) { // 重写了布尔判断的操作符
    std::cout << *option << std::endl;
}
int a = option.value_or(100); // 可以做无值时的默认值处理
```


### C++17std::variant实现

std::variant允许单一变量存放多种类型的数据。

实例代码:
```
#include <iostream>
#include <optional>
#include <variant>

using namespace std;

enum class ErrorCode
{
    None = 0, NotFound = 1, Error = 2
};

std::variant<std::string, ErrorCode> goHome(int a) {
    if (a == 1) {
        return ErrorCode::NotFound;
    }
    return "123";
}

int main() {
    //std::variant<std::string, int> option = goHome(1);
    auto option = goHome(1);

    if (auto* value = std::get_if<std::string>(&option)) {
        std::string str = *value;
        std::cout << str << std::endl;
    }
    else {
        ErrorCode code = std::get<ErrorCode>(option);
        switch (code) {
            case ErrorCode::Error:
                std::cout << "Error" << std::endl;
                break;
            case ErrorCode::None:
                std::cout << "None" << std::endl;
                break;
            case ErrorCode::NotFound:
                std::cout << "NotFound" << std::endl;
                break;
            default:
                std::cout << "default" << std::endl;
                break;
        }
    }
}
```


#### 问题一：variant与union的区别？

- std::variant允许单一变量存放多种类型的数据，每种数据类型空间独立，类型安全
- union也允许单一变量存放多种类型的数据，但是每种数据空间相同，类型不安全，占用内存更少

#### 问题二：variant与结构体的区别？

最多只能有一个数据有效，可以通过index()方法查看有效的数据类型下标
例如:
```
int main() {
    std::variant<int, float> v = 1;
    //v = 2.0f;
    cout << v.index() << endl;
    cout << std::get<float>(v) << endl;
}
```
或者std::get_if

### C++17以下

> C++17以下可以通过结构体返回布尔值进行判断，或者将布尔值的引用传入，方法内部进行赋值，外部判断。


