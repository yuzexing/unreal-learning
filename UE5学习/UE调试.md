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

