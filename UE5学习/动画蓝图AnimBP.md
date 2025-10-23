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


### 动画混合的方式

#### 骨骼混合

> 指定某个骨骼进行混合，常用于上半身和下半身混合的场景

#### 混合遮罩

> 对选定的部分骨骼设置混合权重，mask0不混合，mask1混合

#### 混合描述

> 定义混合骨骼速度

[官方文档](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/blend-masks-and-blend-profiles-in-unreal-engine)


