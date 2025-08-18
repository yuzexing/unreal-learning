# 动画蓝图动画蓝图AniBP

### 生命周期函数

1. Event Blueprint Initialize Animation（在动画蓝图编译后的初次执行时机）
2. Event Blueprint Begin Play（对应Actor的BeginPlay）


### 与Actor的构造函数不同

Actor构造函数，只会在构造CDO时执行一次。后续的Actor构造会先清空内存，然后再通过反射复制CDO中的初始化数据（不是简单的内存拷贝，还要考虑创建新的组件实例）
