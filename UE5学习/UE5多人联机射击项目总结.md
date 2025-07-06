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




> 待补充
