# 静态数组

std::array


优势：
1. 在一定debug等级下有越界判断(可以关闭，进一步优化速度)
2. 维护数组大小了(return _Size;内部不需要维护一个数组长度的整型变量)
3. 与动态数组相比，存储空间在栈中，性能更优
4. 拥有迭代器，支持STL的算法

尽量使用std::array这样的C++风格的静态数组，而不是C风格的数组。

初始化
```
// 初始化一个大小为10的int数组，所有元素为0
std::array<int, 10> arr1 = {0};
std::array<int, 10> arr1{};

// 初始化一个大小为10的int数组，前两个元素为34和45，其余为0
std::array<int, 10> arr2 = {34, 45};

// 使用fill()函数将所有元素设置为4
std::array<int, 10> arr3;
arr3.fill(4);
```

部分源码展示:
```
_EXPORT_STD template <class _Ty, size_t _Size> // _Size
class array { // fixed size array of values
public:
    static_assert(is_object_v<_Ty>, "The C++ Standard forbids containers of non-object types "
                                    "because of [container.requirements].");

    ...

    _NODISCARD constexpr size_type size() const noexcept {
        return _Size; // 直接返回模板数值，存储更优
    }

    _NODISCARD constexpr size_type max_size() const noexcept {
        return _Size; // 直接返回模板数值，存储更优
    }

    ...

        _NODISCARD _CONSTEXPR17 reference operator[](_In_range_(0, _Size - 1) size_type _Pos) noexcept /* strengthened */ {
#if _CONTAINER_DEBUG_LEVEL > 0
        _STL_VERIFY(_Pos < _Size, "array subscript out of range"); // level > 0 则越界判断
#endif // _CONTAINER_DEBUG_LEVEL > 0

        return _Elems[_Pos];
    }
```
