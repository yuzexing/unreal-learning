# C++标准库内置算法

### std::sort

默认升序排序，可以传入排序器。
内置的降序排序``std::greater``
```
std::vector<int> data = {3, 1, 4, 1, 5, 9, 2, 6, 5, 3};
std::sort(data.begin(), data.end(), std::greater<int>());
```

### std::low_bound/upper_bound

```
int target = 5;
auto it = upper_bound(v.begin(), v.end(), target);
```
二分查找，返回第一个不小于target的迭代器

``upper_bound``，二分查找，返回第一个大于target的迭代器
