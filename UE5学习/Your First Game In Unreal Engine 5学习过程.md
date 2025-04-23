# Your First Game In Unreal Engine 5学习过程

学习过程中的困惑：遇到很多类不知道为什么作者要使用他，也不知道他的作用，如果作者能够详细的说明每一个选择的理由就最好了。

学习思路：遇到问题解决问题，遇到使用的API则去文档查询API的作用，如果文档也不清晰，则查询AI。

#### 问题一：UVs的作用


#### 问题二：过程中遇到Axis and action mappings are now deprecated, please use Enhanced input Actions and input Mapping Context instead。

使用[Enhanced Input System](https://www.bilibili.com/video/BV1GEypYKERz/?spm_id_from=333.337.search-card.all.click&vd_source=16b822060153afb61a028379bf440fe8)

Enhanced input Actions and input Mapping Context和Axis and Action mapping的最大区别：前者是基于数据资产（data asset)的，而后者是将定义放在配置文件中（deafultInput.ini）

#### 优势：
1. Enhanced input actions 类似于旧的axis and action mapping，但是通过数据资产管理。
2. Input Mapping Context 支持动态的上下文切换，更加灵活的控制操作映射。


#### 问题三：emissive color是什么？与base color的区别是什么？

> missive Color 在 UE5 中表示材质的自发光属性，用于让表面在无外部光照下依然可见并发出光芒。它通过材质节点的 Emissive Color 输入引脚输出高动态范围（HDR）颜色值，可以驱动泛光（bloom）效果并在启用 Lumen 全局光照时为场景注入动态光源。与 Base Color 不同，Emissive Color 不受场景光源和阴影影响，但在使用动态或静态 Global Illumination 方法时，仍可以向周围物体投射间接光照。

#### 问题四：蓝图中的FlipBook节点是什么？

> FlipBook是一个用于管理和播放精灵图帧序列的节点。

#### 问题五：俯视角下为什么修改SpringArm的Transform为Absolute Rotation，就能避免镜头随着Character转动？

> 当打开 Absolute Rotation 时，所有的继承选项（Inherit Pitch/Yaw/Roll）都将失效，不再从父组件继承对应轴的旋转，所以不会随着Character的旋转而旋转。

#### 问题六：按照教程的要求设置Character Movement的Orient Rotation to Movement后，为什么没有旋转至移动方向？

> Use Controller Rotation：Yaw 需要关闭，如果是True，则使用playerController的左右旋转，与Orient Rotation to Movement冲突。



