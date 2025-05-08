# C++复制和拷贝构造函数

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




