# C++类和结构体

### 类的作用
1. 维护一部分数据
2. 提供对内部数据的操作的方法


#### 类的理解
> 类使我们的代码可读性更高，使代码更简洁。但如果一个功能不使用类无法实现，那么使用类也无法实现，类的本质是语法糖。

### 类的继承

> 待补充 优先级二

### 类的本质

> 待补充 优先级二


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










