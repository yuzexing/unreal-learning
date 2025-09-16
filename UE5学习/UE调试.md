# UE调试

### UE宏日志

```
参数：日志类型，日志等级，日志内容
UE_LOG(LogTemp, Warning, TEXT("Begine Tick Called"));
```
输出在output栏中

### 屏幕中输出

```
参数：Key，显示时间，颜色，日志内容
GEngine->AddOnScreenDebugMessage(32, 1, FColor::Blue, FString(TEXT("my test 11")));
```

### 绘制调试形状

蓝图中，DrawDebugSphere函数

C++中:
```
#include "DrawDebugHelpers.h"

...

UWorld* world = GetWorld();
FVector location = GetActorLocation();
float radius = 10;
int32 fragment = 12;
DrawDebugSphere(world, location, radius, fragment, FColor::Green, true);
```

> C++类不会自动添加场景组件，但是继承自Actor的蓝图类会有

> 问题：对于include VS2022中创建的头文件 "header.h"，会一直提示找不到文件。很怪的bug
> 已解决，在vs2022中创建的.h文件默认是在Intermediate文件夹中，所以找不到


### GAS调试

按'撇键'进行调试，镜头中心靠近谁，则debug哪个actor



