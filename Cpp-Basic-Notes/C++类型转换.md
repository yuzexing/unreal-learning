#　C++类型转换

### C风格类型转换

#### 强制类型转换
```
double b = 5.5;
int a = b; // 隐式类型转换
a = (int)b; // 显示类型转换
```

#### 类型双关
```
#include <iostream>
#include <vector>
struct A {
	int x = 6, y = 3;
};
int main() {
	A a;
	int* p1 = (int *) & a;
	int* p2 = (int*)(char*)(& a + 4);
	std::cout << p1[0] << std::endl;
	std::cout << p1[1] << std::endl;
	std::cout << p2 << std::endl;
}
```


### C++风格类型转换

C++风格类型转换是语法糖，C风格类型转换做不到的事，C++风格的类型转换也做不到。

主要作用：
1. 提高语义
2. 提供更多信息给编译器，做转换时的检查，提供必要报错信息
3. 提高安全性（编译时或运行时的类型检查）


1. dynamic_cast
2. static_cast
3. reinterpret_cast
4. const_cast

[cppreference](https://en.cppreference.com/w/cpp/language/static_cast)

### dynamic_cast

多用于执行多态基类的指针或引用转换为派生类的指针或引用。

进行运行时类型检查，如果转换不安全，则会返回nullptr或者异常。

```
#include <iostream>
#include <vector>

class A {
public:
};

class B: public A {
public:
};

int main() {
	B* b = new B();
    A* a = dynamic_cast<A*>(b);
    if (a == nullptr) {
        cout << "unsafe dynamic_cast" << endl; // 输出：unsafe dynamic_cast
    }
	std::cin.get();
}
```

#### dynamic_cast使用场景

1. 类型检查，判断一个基类类型的对象是否是派生类对象，例如：
```
class Entity {
public:
virtual void play(){} // 建立虚表避免dynamic_cast报错
};
class Player : Entity {
};
class Enemy : Entity {
};
int goHome(Entity* e) {
    Player* p = dynamic_cast<Player*>(e);
    if (p == nullptr) {
        return 0;
    }
    return 1;
}
```

#### dynamic_cast的原理和开销

运行时有额外的判断，会有较小的性能损失。

1. 空间上记录了Run-Time Type Information，RTTI
2. 时间上需要判断转换类型是否合法，不合法则返回空指针
3. 使用dynamic_cast需要打开RTTI，否则编译时会警告，运行时异常

> 在多态中补充RTTI和虚表的关联

### static_cast

无运行时检查，对应C风格的显示类型转换
```
double b = 5.5;
int a = (int)b; // 显示类型转换
a = static_cast<int>(b);
```

### reinterpret_cast


用来处理无关类型之间的转换；会产生一个新值，该值域原始参数具有相同的比特位。

简单的说：将一段二进制数据重新解释。

```
int* p1 = (int *) & a;
int* p2 = (int*)((char*)&a + 4);
std::cout << p1[0] << std::endl;
std::cout << p1[1] << std::endl;
std::cout << *p2 << std::endl;

// 改为使用reinterpret_cast实现
int* p3 = reinterpret_cast<int*>(&a);
std::cout << p3[0] << std::endl;
std::cout << p3[1] << std::endl;
```

### const_cast

### 定义
转换不同类型的CV限定符，即增加/移除const或者volatile

const_cast通常针对引用、指针，主要是将指针常量改为普通指针，使得指向数据可以被修改

Note:
- "Modifying a const object through a non-const access path and referring to a volatile object through a non-volatile glvalue results in undefined behavior"
- "Pointers to functions and pointers to member functions are not subject to const_cast."

[cppreference](https://en.cppreference.com/w/cpp/language/const_cast)


```
const int a = 1;
int* c = const_cast<int*>(&a);
// *c = 6;
std::cout << a << std::endl; // 1 = 6 ，属于未定义行为，如Note1所说

int i = 3;                 // i is not declared const
const int& rci = i;
const_cast<int&>(rci) = 4; // OK: modifies i
std::cout << "i = " << i << '\n';
```

```
struct type
{
    int i;
 
    type(): i(3) {}
 
    void f(int v) const
    {
        // this->i = v;                 // compile error: this is a pointer to const
        const_cast<type*>(this)->i = v; // OK as long as the type object isn't const
    }
};
int main()
{
    void (type::* pmf)(int) const = &type::f;  // pointer to member function
    // const_cast<void(type::*)(int)>(pmf);   // compile error: const_cast does
    // 如Note2所说
}
```