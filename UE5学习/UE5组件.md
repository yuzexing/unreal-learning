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

用于显示网格的组件

> 继承自USceneComponent

### SkeletalMeshComponent

骨骼网格组件

用于绑定动画
> 继承自USceneComponent

###  

> 待补充


