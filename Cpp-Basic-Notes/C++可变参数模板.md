# C++可变参数模板

[pack](https://en.cppreference.com/w/cpp/language/pack)|
[microsoft](https://learn.microsoft.com/zh-cn/cpp/cpp/ellipses-and-variadic-templates?view=msvc-170)

> 知道有C风格的可变参数实现方法-va_list，但是挺难用的，例如会修改参数类型，耦合性强

### 可变参数模板


#### 声明方式：
```
template<typename... Arguments> class classname;
```
常见的是省略号跟着typename

参数数量0-n个，也可以指定固定数量的参数，加可变参数：
```
template <typename First, typename... Rest> class classname;
```
类似于js的剩余参数，但是这个是模板

变体用法：
```
template <typename... Arguments> returntype functionname(Arguments&... args);
template <typename... Arguments> returntype functionname(Arguments&&... args);
template <typename... Arguments> returntype functionname(Arguments*... args);
```

可以通过``sizeof...(args)``获取可变参数个数


#### 省略号的位置

```
template <typename... Types> void func1(std::vector<Types...> v1);
// 是vector模板的可变参数

template <typename... Types> void func2(std::vector<Types>... v2);
// 是函数参数的可变参数
```


#### 对于可变参数的使用方法

1. 递归使用实例：
```
void print() {
	std::cout << std::endl;
}

template<typename T>
void print(T t) {
	std::cout << t << std::endl;
}

template<typename T, typename... Args>
void print(T out, Args... arg) {
	std::cout << out << ",";
	print(arg...);
}

int main() {
	using namespace std;
	print(1, 2, 3.f, "qweq");
	return 0;
}
```

2. 

### 折叠表达式

### 完美转发