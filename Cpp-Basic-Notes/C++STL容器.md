
# C++STL容器

### Vector

构造函数
使用 vector<int> v(i, j); // 初始化 i个 元素为j的vector
使用 vector<int> v = { i, i, i }; // 手动指定某个元素以及数量作为初始化

拷贝构造函数
vector<int> v1
vector<int> v2(v1.begin(), v1.end()); // 左闭右开，v1.end指向容器最后一位的后一位

常用api
push_back();
front(); // 返回容器第一个元素的引用
back(); // 返回容器最后一个元素的引用

begin() // 该迭代器指向向量第一个元素
end()  //该迭代器指向向量最后一个元素之后的元素
rbegin()
rend()

细节：其中begin()获取的迭代器不能和rbegin()/rend()迭代器进行比较

[参考api](https://learn.microsoft.com/zh-cn/cpp/standard-library/vector-class?view=msvc-170)

### Set

常用api
erase：删除元素

### unordered_Set

常用api

insert
find
count
contains C++20

与set的区别

### Map

特性：
1. 大小可变的关联容器
2. 可逆（双向迭代器）
3. 有序
4. 唯一

常见API:

1. insert
2. begin
3. end
4. clear
5. contains(C++20)
6. erase // 可传入迭代器 或者迭代器的起点和终点，或者传入key
7. find
8. empty
9. size
10. count
11. value_type 允许在map中存储的元素的对象类型


### unordered_Map

### deque
双端存储的数据结构，通常是栈/队列中封装的容器

常用方法：
push_back
push_front
pop_front
pop_back
fornt()
back()
empty()

### string

### 注意事项

- 为什么在实例化模板类时，不指定模板参数类型编译器会报错？例如
- ![image](https://github.com/user-attachments/assets/e57c4384-6b97-4a7b-952b-03e501cdad70)
todo 学习模板和类的区别，学习静态类型语言和动态类型语言的区别。


