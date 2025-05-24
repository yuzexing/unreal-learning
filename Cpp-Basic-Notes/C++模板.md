# C++模板

### 用法


基础用法：

```
template<typename T, int N>
class Test {
public:
    T m_a;
    int m_b = N;
    Test(T a): m_a(a) {
    }
    void print() {
        std::cout << "yuzexing" << m_a << m_b << std::endl;
    };
};
int main() {
    Test<std::string, 1> t("1");
    // Test<int, 2> t(1);
    // Test<float, 3> t(1.0f);
    // Test<char, 4> t('z');
    t.print();
}
```


### 细节
1. 只有当代码中执行了相应类型的函数，编译器才会生成对应的函数，否则不占用任何空间
2. 原理是由编译器，代替开发者在编译时期进行代码的生成

### 模板的使用场景

模板既有好处又有坏处，当一个模板太过复杂，就最好不要使用模板，避免代码晦涩难懂


### enable_if

用来限制模板的生成条件

原理：利用编译器的 SFINAE 原则，使得模板实例化失败但是不报错，从而匹配其他重载函数
```
template <typename T>
typename enable_if<std::is_integral<T>::value, T>::type
func(T t) {
    return t;
}
func(1); // ok
func(1.0); // error
```
SFINAE:Substitution Failure Is Not An Error

