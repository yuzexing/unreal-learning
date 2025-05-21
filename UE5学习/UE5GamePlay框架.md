# UE5GamePlay框架

参考自《InsideUE4》知乎大钊

### Actor（个体/演员）

是什么：
- 组成世界的个体
- 可互相嵌套
- 组件的容器
- 网络通信的单位
- 可派生出职责不同的Actor

Actor 是跨关卡的
不是所有Actor都是可以看见的


### Componet

- 组件是一种能力
- 组件为Actor提供能力
- 能力与业务逻辑无关
- 能力是跨游戏的

例如：碰撞，投射，移动等

不好的实践：包含爆率、经验等业务逻辑


### Actor和Component的关系

Actor分为可见或不可见的

Component 为Actor提供能力，避免Actor每个臃肿

理解：

ActorComponent 是干什么？

SceneComponent支持嵌套，因为UE更支持Transform的组件进行嵌套。
SceneComponent作为RootComponent
"一个Actor若想可以被放进Level里，就必须实例化USceneComponent* RootComponent"。意思是：Actor必须拥有SceneComponent才可以放到level中。

对于其他组件，更支持组合的方式。而不是嵌套：因为嵌套过深会有性能损耗。

我的理解：组件是独立的能力，所以几乎不需要嵌套，不需要关心其他组件的存在。

组件中适合

Actor之间的父子关系是通过**Component**确定的，即SceneComponent的嵌套。

Actor本身不关心父子关系，而是将父子关系委托与**Component**，

Actor更像是一个容器，只提供了基本的创建销毁，网络复制，事件触发等一些逻辑性的功能。

### 思考：

3C：Camera，Control，Character

Actor和Pawn和Character的区别

> 待补充


### Level和World

Level中包含多个Actor，第一个Actor是AWorldSetting，
多个Level组成一个World，存在一个主Level（PersistentLevel），

World中包含了所有level中的Controller和Pawn的弱引用，便于查找

WorldSetting的关系，World的Setting是以PersistentLevel为主的，我的理解：
1. PersistentLevel的WorldSetting负责通用的设置
2. 每个Level中可以进行定制化的配置，例如光照等。

### WorldContext，GameInstance，Engine

世界的类型：
```
namespace EWorldType
{
	enum Type
	{
		None,		// An untyped world, in most cases this will be the vestigial worlds of streamed in sub-levels
		Game,		// The game world
		Editor,		// A world being edited in the editor
		PIE,		// A Play In Editor world
		Preview,	// A preview world for an editor tool
		Inactive	// An editor world that was loaded but not currently being edited in the level editor
	};
}
```

UE可以通过WorldContext管理世界。

> 编辑器模式下，一个WorldContext给编辑器，一个给PIE，基本不需要我们关心，也不需要我们操作。

WorldContext还保存着World里的Level切换的上下文。

综上：WorldContext集负责World切换，也管理Level之间切换的信息。

问题：为什么Level切换的信息不放在World中？


```
struct FWorldContext
{
    [...]
	TEnumAsByte<EWorldType::Type>	WorldType;

	FSeamlessTravelHandler SeamlessTravelHandler;

	FName ContextHandle;

	/** URL to travel to for pending client connect */
	FString TravelURL;

	/** TravelType for pending client connects */
	uint8 TravelType;

	/** URL the last time we traveled */
	UPROPERTY()
	struct FURL LastURL;

	/** last server we connected to (for "reconnect" command) */
	UPROPERTY()
	struct FURL LastRemoteURL;

}
```

> 回答：因为存在跨World的level切换



WorldContext保存在GameInstance中

GameInstance中可以存放独立于Level的逻辑和数据

C++和BP通常继承与GameInstance


### Engine

UEngine分为UGameEngine和UEditorEngine

UEngine的WorldList保存了所有的World


###　GamePlayStatics


Engine层次暴露的蓝图函数库：

```
UCLASS ()
class UGameplayStatics : public UBlueprintFunctionLibrary 
```

> 待补充 优先级五





