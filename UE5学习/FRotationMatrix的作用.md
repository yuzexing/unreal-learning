# FRotationMatrix的作用

``RotationMatrix``表示3x3的旋转矩阵

### 通过欧拉角构造旋转矩阵的意义

将欧拉角的旋转，通过一个旋转矩阵来表示，原空间中的任何一个点、向量，通过这个旋转矩阵，都可以对其应用欧拉角的旋转。


### 场景1：根据控制器的旋转角度获取控制器的方向向量

```
FRotator ControlRotator = GetControlRotation();
FRotator YawRotator(0.f, ControlRotator.Yaw, 0.f);
FVector Direction = FRotationMatrix(YawRotator).GetUnitAxis(EAxis::X);
AddMovementInput(Direction, 1.0f);
```
解释：
1. 获取Controller的``Yaw``，使``roll``和``pitch``保持为0
2. 将``YawRotator``的角度转化为旋转矩阵
3. 将``GetUnitAxis``世界标准单位向量乘以旋转矩阵，获取``Yaw``角度的单位方向向量

### 问题一：如果直接用Controller的Rotation转化为旋转矩阵是什么情况?
即：
```
FVector Direction = FRotationMatrix(GetControlRotation()).GetUnitAxis(EAxis::X);
AddMovementInput(Direction, 1.0f);
```
> 将会向``Controller``实际面向的方向作用力
> 将``pitch``置为0是为了保持``Direction``与x,y平面共面
> 确保无论摄像机往上看还是往下看，角色始终只沿水平面前进

### 万向节死锁

成因：某个轴运动到另外两个**轴重合**后，会出现某一轴消失的情况。

解决：无法解决，除非避免轴的重合，或者不使用欧拉角作为旋转描述。

欧拉角是按规定顺序进行变换，以保证唯一性。如果顺序不固定，则结果不唯一

为什么轴会重合?
> 欧拉角是变换过程，每一次变换都是独立的，从(0,0,0)开始，按顺序变换到指定度数，所以**后面的轴转动无法带动前面的轴转动**。
