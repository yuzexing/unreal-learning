# UE5多人射击项目总结


### ServerTravel失败

现象：在蓝图或者C++中通过字符串传递目标关卡路径
结果：目标关卡加载失败，重新加载当前关卡
原因：目标关卡没有被打包
解决方法：
1. 在蓝图中使用引用资源的方式打开关卡
2. 如果使用路径，则需要主动将地图路径设置为打包的地图列表

### CreateSession失败(Steam)
在steam中，``bUsesPresence`` 和 ``bUseLobbiesIfAvailable`` 必须保持一致，否则直接创建会话失败
源码：
```
// In Steam, bUsesPresence and bUseLobbiesIfAvailable have equivalent meaning and should have the same value
if (NewSessionSettings.bUsesPresence != NewSessionSettings.bUseLobbiesIfAvailable)
{
	UE_LOG_ONLINE_SESSION(Warning, TEXT("[%hs] The values of FOnlineSessionSettings::bUsesPresence and FOnlineSessionSettings::bUseLobbiesIfAvailable are treated as equal and have to match"), __FUNCTION__);
	TriggerOnCreateSessionCompleteDelegates(SessionName, false);
	return false;
}
```

### FindSession失败(Steam)

```
[OnlineSubsystemSteam]
bEnabled=true
SteamDevAppId=480
bInitServerOnClient=true // 必须加上这行才能找到创建的会话
```

### UE5.6 JoinSession失败/Connection Loss

1. 开启``Steam Sockets``插件
2. 增加``DefaultEngine.ini``配置
3. 参考``Create``/``FindSesion``的配置

```
// DefaultEngine.ini
[/Script/Engine.GameEngine]
!NetDriverDefinitions=ClearArray
+NetDriverDefinitions=(DefName="GameNetDriver",DriverClassName="/Script/SteamSockets.SteamSocketsNetDriver",DriverClassNameFallback="OnlineSubsystemUtils.IpNetDriver")
[OnlineSubsystem]
DefaultPlatformService=Steam

[OnlineSubsystemSteam]
bEnabled=true
SteamDevAppId=480
bInitServerOnClient=true

[/Script/OnlineSubsystemSteam.SteamNetDriver]
NetConnectionClassName="OnlineSubsystemSteam.SteamNetConnection"
```

### 鼠标控制Character旋转的完整流程

1. AxisMapping绑定鼠标左右移动事件
2. 通过回调执行``AddControllerYawInput``修改``PlayerController``中的``Rotation``
3. 设置弹簧臂旋转。``SpringBoom``设置``usePawnControlRotation=true;``
4. 禁用角色自身旋转。``Character``设置``bUseControllerRotationYaw = false;``
5. 角色旋转至移动方向。``Character``设置``orientRotationToMovent = true;``

第一个项目一致，但是思考并总结了流程
```
Pawn: AddControllerYawInput(float); 修改PlayerController Yaw角度
Pawn: bUseControllerRotationYaw = True; Pawn的旋转绑定PlayerController
SpringBoom: usePawnControlRotation = True; 弹簧臂的旋转绑定PlayerController
Camera:  usePawnControlRotation = True; 相机的旋转绑定PlayerController
```

### 人物运动转向时的侧倾功能

1. 记录人物每一帧的旋转角，主要是Yaw
2. 当前帧-上一帧的角度(标准化)
3. 将偏航角差值δyaw除以DeltaTime，求得当前帧的旋转状态下，每秒的旋转角度t
4. 对lean进行插值，结果从lean至t

#### 问题一：为什么要除以deltTime?

> 用于消除帧率依赖，当帧率很高时，偏航角δyaw会很小，当帧率很低时，δyaw很大

#### 问题二：为什么要对lean插值?

> 用于对侧倾速度的平滑过渡
> 如果不插值，直接使用t作为lean，那么会导致人物动画的过渡不平滑
> 也可以不对lean插值，在混合空间中做平滑过渡（也是插值）

### BUG：直接对欧拉角插值

当在动画蓝图中(Smooth)直接对欧拉角插值时，从-180到180的过渡时，会经过0到180，从而导致动画的异动

解决办法一：在C++中使用FMath::RInterp(R1, R2, dt, float);

原理： 内部对角度做了标准化，求得最小旋转角度，允许从-180,180之间的角度过渡

解决办法二：在动画蓝图中勾选``wrapInput``，允许超过最大值最小值时进行循环

### GetBaseAimRotation和GetActorRotation的区别

GetBaseAimRotation 返回PlayerController的旋转角度，对于玩家，则返回镜头的旋转角度；对于Ai，返回眼睛的旋转角度；主要用于瞄准，视线方向等需求

GetActorRotation 获取Actor在世界空间中的旋转角度

### Rotator中的浮点数在网络传输中的压缩

32位浮点数压缩至16位的**无符号**short，在网络传输后，角度从[-180,180)变为[0,360)

解决办法：
1. 重新做归一化
2. 对于character的情况，可以将[270,360)的值映射为[-90, 0)

```
FVector2D InRange(270.f, 360.f);
FVector2D OutRange(-90.f, 0.f);
AO_PitchOffset = FMath::GetMappedRangeValueClamped(InRange, OutRange, AimRotation.Pitch);
```

``GetMappedRangeValueClamped``表示将输入映射到``OutRange``空间，大于或小于``InRange``边界时，按out边界输出

### 为什么将生命值放到PlayerState里？而是放到Character或者HealthComponent里？

> 1. PlayerState默认使用的低频的网络同步频率，大约是1s一次，生命值变动会比较频繁，不适合放到这里
> 2. PlayerState如果设置了高频更新的频率，会复制不必要的信息从而占用带宽，例如ping值信息
> 所以生命值信息不放在PlayerState里


> 待补充
