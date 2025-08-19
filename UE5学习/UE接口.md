# UE接口

### UInterface

用于对反射系统可见的空壳类，继承自UObject

### IInterface

用于实际继承和声明接口的类

### 接口的使用场景

对于没有共同父类(除了UObject等)，但需要实现相同的功能的情况。例如：高亮效果
1. 高亮敌人
2. 高亮物品
3. 高亮场景

都可以通过实现该接口，在将需要高亮的Object转成接口类，调用高亮接口。

### 查看类是否实现接口

```
bool bIsImplemented;

/* bIsImplemented is true if OriginalObject implements UReactToTriggerInterface */
bIsImplemented = OriginalObject->GetClass()->ImplementsInterface(UReactToTriggerInterface::StaticClass());

/* bIsImplemented is true if OriginalObject implements UReactToTriggerInterface */
bIsImplemented = OriginalObject->Implements<UReactToTriggerInterface>();

/* ReactingObject is non-null if OriginalObject implements UReactToTriggerInterface in C++ */
IReactToTriggerInterface* ReactingObject = Cast<IReactToTriggerInterface>(OriginalObject);
```

区别：
1. ``Cast``只支持C++类，无法检测蓝图类，另外两种支持蓝图类




