# C++类和结构体

### 类的作用
1. 维护一部分数据
2. 提供对内部数据的操作的方法


#### 类的理解
> 类使我们的代码可读性更高，使代码更简洁。但如果一个功能不使用类无法实现，那么使用类也无法实现，类的本质是语法糖。使复杂的事变的简单，而不是将不可能的事变为可能。

### 类的构造函数

1. 一般用于进行初始化操作，C++中需要显示的初始化所有基本数据类型。
2. 类有默认的构造函数。
3. 当不希望类被实例化时，可以通过隐藏构造函数达到该效果。
  - private consturct()
  - construct() = delete;

### 类的析构函数

1. 一般用于进行内存的回收操作
2. 虽然可以手动调用析构函数，但是不推荐这么做
```
定义
~construct() {
	// 释放内存
}
```

### 类的继承

C++中是多继承。

```
class A1 {};
class A2 {};
class A3 {};

class B : public A1, A2, private A3 {
	// C++中继承的写法
}
```
#### 问题： ``B : public A``中所声明的public是什么意思？换成private会怎么样?

> 回答：C++中的继承分为公有继承、保护继承、私有继承，不同的继承方式会影响从父类继承成员的可见性。从父类继承的成员有``public``和``protected``的可见性的成员。换成``private``则仅当前类可见

#### 公有继承、保护继承、私有继承的区别

属于C++可见性的范畴，可见性旨在与更好的组织代码，提高代码的可读性，可维护性。对于CPU无用。

> 只有公有继承可以多态?

#### 继承的作用
1. 减少重复代码，提高可维护性（）
2. 提高

> 待补充 优先级二

#### 多继承的常见问题
1. 菱形继承
2. 命名冲突

> 待补充

### 虚函数

允许多态下调用子类中重写的方法。代价：1.虚表(空间)，2.查表(时间)。

样例：
```
class A {
public:
	virtual void func() {
		std::cout << 'A' << std::endl;
	}
};
class B : private A {
public:
	void func() overide { // override还必须写在这里
		std::cout << 'B' << std::endl;
	}
};
A b = new B();
b.func(); // virtual修饰为打印B，无virtual修饰则打印A
```

#### 通过


> 补充 虚函数实现的逻辑（多态+虚函数指向正确的函数）


### 纯虚函数
```
class A {
public:
	virtual void func() = 0;
}
```
> 当类中定义了纯虚函数时，该类无法进行实例化。
> 当类继承的函数中有纯虚函数，该类无法进行实例化，除非对该纯虚函数进行重写。
> 等价于java中的抽象函数。

### 类的本质

> 待补充 优先级二

### 类的成员

[成员和嵌套成员的介绍](https://www.learncpp.com/cpp-tutorial/nested-types-member-types/)

### 类和结构体的区别

1. 从技术上来说，class和struct在默认的可见性上有区别，class默认是private，struct默认是public
2. 从抽象层面上说，class和struct的使用取决于编程风格和习惯：
  - 一般简单的数据集合，常使用结构体维护。
  - 较为复杂的情况，例如需要继承（结构体也可以继承）的情况，常使用类进行维护。

### 问题一：类和结构体存储在哪个区域？

> 优先级一

### 问题二：这段代码为什么会链接错误？
```
class A {
public:
	static int a;
};

int main() {
	A::a = 1;
	return 0;
}
//main.obj : error LNK2001: unresolved external symbol "public: static int A::a" (?a@A@@2HA)
```

### 问题三：为什么类中非常量的静态成员不允许初始化？
```
class A {
public:
	static int a = 1; // 编译报错
};
// E1592, 必须声明常量才能初始化
```

> 回答：静态成员在类定义中只能作声明，而(不能定义)[https://en.cppreference.com/w/cpp/language/static]。
> 但是为什么呢？(待补充)[https://stackoverflow.com/questions/45277976/why-can-static-member-function-definitions-not-have-the-keyword-static]

### 代码中的数据存在哪个区？

> 优先级二

### 结构体存在的意义
1. 兼容C语言










