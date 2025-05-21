# C++unique_ptr源码分析


### 0 == sizeof(T)的情况
在``default_delete``代码中，有一个断言类型不能小于0：
```
_EXPORT_STD template <class _Ty>
struct default_delete { // default deleter for unique_ptr
    constexpr default_delete() noexcept = default;

    template <class _Ty2, enable_if_t<is_convertible_v<_Ty2*, _Ty*>, int> = 0>
    _CONSTEXPR23 default_delete(const default_delete<_Ty2>&) noexcept {}

    _CONSTEXPR23 void operator()(_Ty* _Ptr) const noexcept /* strengthened */ { // delete a pointer
        static_assert(0 < sizeof(_Ty), "can't delete an incomplete type");
        delete _Ptr;
    }
};
```
思考：空类size至少1btye，什么情况下能等于0呢？
> 类的前置声明未定义的情况：例如
```
class B; // 声明

//class B {}; // 定义

template <typename T>
void p() {
    static_assert(0 < sizeof(T), "can't delete an incomplete type");
}

int main() {
    p<B>(); // "can't delete an incomplete type"
}
```


### enable_if

用于根据条件启用或者禁用模板：
```

_EXPORT_STD template <class _From, class _To>
_INLINE_VAR constexpr bool is_convertible_v = __is_convertible_to(_From, _To);

_EXPORT_STD template <class _Ty>
struct default_delete {
    template <class _Ty2, enable_if_t<is_convertible_v<_Ty2*, _Ty*>, int> = 0>
    _CONSTEXPR23 default_delete(const default_delete<_Ty2>&) noexcept {}
}
```
解释：用于模板构造函数的编译时期合法性判断，当_Ty2*和_Ty*指针可以转换时，模板存在，且模板第二个参数为int默认值=0,(后面int=0没有作用，只是为醋包的饺子)；否则整个构造函数模板不存在。

SFINAE机制： Substitution failure is not an error

> 待补充