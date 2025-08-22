# UClass

### UClass是什么

UClass是运行时的类信息，通过编译时期收集元数据生成的反射系统可知的类型。可以包括蓝图类型

### 与泛型中设置的类有什么区别？

泛型是C++模板参数，编译时期的类信息，运行时期则没有这个概念。

用于编译时期的安全性检查

### 场景

```
AuraWidgetController = NewObject<UAuraWidgetController>(this, UAuraWidgetController::StaticClass());
// 其中UAuraWidgetController为泛型，参数模板，编译时期的类型
// StaticClass()是UClass*，运行时期的类型
```
