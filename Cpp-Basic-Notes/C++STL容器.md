# C++ STL 容器

---

## Vector

### 构造函数

```cpp
vector<int> v(i);           // 初始化i个0
vector<int> v(i, j);        // 初始化i个j
vector<int> v = {1, 2, 3};  // 手动指定初始化
```

### 拷贝构造

```cpp
vector<int> v1;
vector<int> v2(v1.begin(), v1.end()); // 左闭右开
```

### 常用 API

* `push_back(val)`
* `emplace_back(args...)` 就地构造插入
* `emplace(pos, args...)`
* `insert(pos, val)` / `insert(pos, n, val)`
* `erase(pos)` / `erase(start, end)`
* `pop_back()`
* `resize(n)`
* `front()` / `back()`
* `begin()` / `end()`
* `rbegin()` / `rend()`

> 说明: `begin()` 和 `rbegin()` 不可相比

### 示例: 迭代删除

```cpp
vector<int> v{1,2,3,4,5};
for (auto it = v.begin(); it != v.end(); ) {
    printf("%d\n", *it);
    it = v.erase(it); // 删除当前元素
}
```

[vector API 参考](https://learn.microsoft.com/zh-cn/cpp/standard-library/vector-class?view=msvc-170)

---

## Set

* 唯一元素
* 有序
* 常用: `insert`, `erase`, `find`, `count`, `contains (C++20)`

## unordered\_set

* 无序
* 快速 O(1) 接口
* 最坏情况下性能差
* API: `insert`, `erase`, `find`, `count`, `contains (C++20)`

---

## Map

* 有序，key 唯一
* 可反向迭代

### API

* `insert`
* `find`
* `erase` (by key or iterator)
* `contains` (C++20)
* `empty`
* `size`
* `clear`
* `begin()` / `end()`
* `count()` // 只有0或1
* `value_type` // pair\<const Key, T>

## unordered\_map

* 无序
* API同 map

---

## deque

* 双端队列结构
* 适用于做 stack / queue 的基类

### API

* `push_back`, `push_front`
* `pop_back`, `pop_front`
* `front()`, `back()`
* `empty()`

---

## queue

* 先入先出 (FIFO)

### API

* `push()`
* `pop()`
* `front()`
* `empty()`

## stack

* 先入后出 (LIFO)

### API

* `push()`
* `pop()`
* `top()`
* `empty()`

---

## string (std::string)

* 字符字组容器，拥有 SSO (短字符串优化)

### 迭代器 API

* `begin()`, `end()`
* `cbegin()`, `cend()` // const\_iterator
* `rbegin()`, `rend()` // 反向迭代

### 操作

* `at(pos)` / `operator[]`
* `front()`, `back()`
* `data()` // 指向首字符的指针
* `push_back(ch)`, `pop_back()`
* `insert(pos, str)`
* `replace(pos, len, str)`
* `append(str)`

[详情见 cppreference](https://en.cppreference.com/w/cpp/string/basic_string)
