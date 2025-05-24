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

### make_unique 的三种方式

源码中存在三种方式``make_unique``：
```
_EXPORT_STD template <class _Ty, class... _Types, enable_if_t<!is_array_v<_Ty>, int> = 0>
_NODISCARD_SMART_PTR_ALLOC _CONSTEXPR23 unique_ptr<_Ty> make_unique(_Types&&... _Args) { // make a unique_ptr
    return unique_ptr<_Ty>(new _Ty(_STD forward<_Types>(_Args)...));
}

_EXPORT_STD template <class _Ty, enable_if_t<is_array_v<_Ty> && extent_v<_Ty> == 0, int> = 0>
_NODISCARD_SMART_PTR_ALLOC _CONSTEXPR23 unique_ptr<_Ty> make_unique(const size_t _Size) { // make a unique_ptr
    using _Elem = remove_extent_t<_Ty>;
    return unique_ptr<_Ty>(new _Elem[_Size]());
}

_EXPORT_STD template <class _Ty, class... _Types, enable_if_t<extent_v<_Ty> != 0, int> = 0>
void make_unique(_Types&&...) = delete;
```

第一种：针对单个对象
第二种：针对可变数组
第三种：针对固定数组（被删除）

为什么第三种被**特地**删除：
1. unique_ptr没有专门处理T[N]这种情况
2. 如果不显示删除，则会匹配第一种模式：将T[N]当做单一对象进行管理，违反直觉
3. 如果将T[N]当做可变数组处理，即匹配第二种模式：违反了铁律—— something sth = make<something>();

也就是变为了``unique_ptr<T[]> t = make_unique<T[50]>()``这样的奇怪模样


[stackoverflow](https://stackoverflow.com/questions/16596950/why-is-make-uniquetn-disallowed)

其中代码解释：

``_EXPORT_STD``宏用来标识是否在``std``中被``export``

``is_array_v<>``等价于``is_array<T>::value``，用于判断T类型是否是数组

``extent_v<T, unsigned N = 0>``等价于``extent<T>::value``，用于判断T类型在数组的情况下的第N维的长度是多少

```
enable_if_t<!is_array_v<_Ty>, int> = 0
```
表示模板在``_Ty``不是数组的情况下生效，也就是针对于对象的情况

```
<class... _Types> // 可变参数模板
make_unique(_Types&&... _Args)  // 万能引用
new _Ty(_STD forward<_Types>(_Args)...) // 完美转发
```

三个代码片段表示将可变参数模板的参数，完美转发到构造函数的参数列表中



```
enable_if_t<is_array_v<_Ty> && extent_v<_Ty> == 0, int> = 0
```
表示是数组并且数组长度位置的情况下模板可用：T[]

> 待补充