# 动画蓝图动画蓝图AnimBP

### 生命周期函数

1. Event Blueprint Initialize Animation（在动画蓝图编译后的初次执行时机）
2. Event Blueprint Begin Play（对应Actor的BeginPlay）


### 与Actor的构造函数不同

Actor构造函数，只会在构造CDO时执行一次。后续的Actor构造会先清空内存，然后再通过反射复制CDO中的初始化数据（不是简单的内存拷贝，还要考虑创建新的组件实例）



### 动画蓝图之间的通信方式

例如1：
1. 人物动画蓝图与武器动画蓝图之间通信
2. 前提：人物骨骼的蒙太奇动画通知，可以通知到人物骨骼动画蓝图
3. 通过Pawn获取武器骨骼，在通过武器骨骼获取动画实例
4. 通过动画实例修改动画蓝图中的变量


### 如何调用父类的函数？

1. 右击当前需要调用父类方法的函数
2. add call to parent function


