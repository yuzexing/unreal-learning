
# C++STL容器

### Vector

构造函数
使用 vector<int> v(i, j); // 初始化 i个 元素为j的vector
使用 vector<int> v = { i, i, i }; // 手动指定某个元素以及数量作为初始化
常用api
push_back();
front(); // 返回容器第一个元素的引用
back(); // 返回容器最后一个元素的引用

begin()
end()
rbegin()
rend()

细节：其中begin()获取的迭代器不能和rbegin()/rend()迭代器进行比较


### Set

### unordered_Set

常用api

insert
find
count
contains C++20

与set的区别

### Map

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


