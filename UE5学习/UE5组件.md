# UE5组件

### RootComponent

根组件，可以替换通过：
```
SetRootComponent()
```

### USceneComponent

1. 提供Transform，Rotation，Scale
2. 提供嵌套关系
3. 继承自Actor的蓝图类默认提供DefaultSceneRoot，即USceneComponent

> 继承自UObject

### CapsuleComponent

胶囊体组件，用于实现简单高效的碰撞

因为static mesh本身三角形较多，计算碰撞会有较高的代价，所以可以使用胶囊体实现

> 继承自USceneComponent

### StaticMeshComponent

用于显示网格的组件，适合静态Actor，性能更高

> 继承自USceneComponent

### SkeletalMeshComponent

骨骼网格组件

支持骨骼动画，适合动态任务
> 继承自USceneComponent

### UCameraComponent

相机组件
继承自：USceneComponent

### USpringArmComponent

旋转臂组件，常用于Camera和Actor之间，设置固定距离

特点：
1. 遇到碰撞会自动收缩，防止穿模和视线遮挡

继承自：USceneComponent

### GroomComponent

毛发组件，用于头发或者眉毛等

可以绑定mesh，附加socket

> 待补充


