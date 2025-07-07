# RPG项目总结

## 性能优化方面

### 禁用Tick

如果Component或者Actor不需要Tick函数，禁用的Tick函数的执行

### 前置申明
.h文件中前置声明，避免在.h文件中引用其他.h文件

### 清理无用头文件

加快预处理速度

### 动画蓝图启用多线程

在子线程中更新数据，避免阻塞主线程

### 动画蓝图拆分

拆分动画蓝图，动画蓝图之间可以传递变量

### 动画缓存

``Save Cached Pose`` 是用于缓存一次已经计算好的动画姿势，通过``Use Cached Pose``可以多次复用而不必重复计算。

### 对象池

Spawn 、Destroy Actor较为消耗性能，尤其是在频繁的Spawn和Destory时，考虑使用对象池

TODO:使用对象池实现Soul和Gold的生成销毁

### 树木导致的FPS降低
将抗锯齿调整为：Temporal Anti-Aliasing
阴影贴图改为：Shadow Map

### 函数内联
合理使用``FORCEINLINE``或者`INLINE``，增加代码执行速度

### const引用传递
引用/指针传递避免拷贝，如果不涉及修改，则申明为const参数和函数

> const提高语义，编译器可以更放心的做优化

### 减少开方计算

例如：
1. 在求两点间距离时，使用切比雪夫距离/曼哈顿距离而不是欧氏距离
2. 求``Vector.Size()``可以使用``Vector.SizeSquared()``

### 待补充

## 动画蓝图方面

### 动画蓝图模板

1. 动画蓝图内部使用SequencePlayer 、BlendSpacePlayer而不是具体的骨骼动画
2. 将公共资源公开引脚，通过变量传入
3. 通过配置变量使用通用动画蓝图模板


## 物理方面

### Query Only (No Physics Collision)

1. 仅做查询，例如射线检测，重叠检测，碰撞检测
2. 不会参与物理模拟，即不会用于刚体仿真，碰撞反弹

### Physics Only (No Query Collision)

1. 仅做物理模拟，组件会参与物理碰撞处理（如重力推移、刚体反弹、物体阻挡）
2. 不会被射线检测，重叠检测，碰撞检测等

## BUG方面

### Destructible Actor Spawn Actor Error

现象：可破坏物被击碎并击飞后，无法在原地产生宝藏

处理1：设置``AlwaysSpawn``
```
FActorSpawnParameters Params;
Params.SpawnCollisionHandlingOverride = ESpawnActorCollisionHandlingMethod::AlwaysSpawn;
```
结果：依然无法生成

处理2：忽略宝藏中Sphere组件与Destructible类型的对象的overlay事件
结果：成功

### 打包时FORCEINLINE方法链接不到

将方法改为非``FORCEINLINE``方法，或者将函数定义与声明放在一起

### 出了世界分区后武器消失

原因：``World partition``导致的所属区域即其中Actor被卸载
解决：添加属性不被卸载


###



将武器Actor的``Is Spatially Loaded``置为``False``

### 打包后AI导航失效

手动build Paths
