# Lyra学习

### Experience

1. 体验是什么？是lyra自定义的吗？
> 是的

层级关系：

#### Experience包含
1. GameFeature
2. DefaultPawnData
3. 动作集合
4. 动作

#### LyraPawnData包含
1. PawnClass
2. 能力集合
3. Tag关系，打断，阻塞等类似于优先级的定义
4. InputAction(IA)与InputTag的映射关系
5. 相机模式

### GameplayFeature里的Actions - AddAbilities，与Experience中有什么区别？

> 加载方式没有区别，区别在于模块化处理，Experience中的Actions与Experience绑定，GameplayFeature中的Actions与GameplayFeature关联。

### GameplayFeature里的PrimaryAssetScan，与Experience中有什么区别？

> 同上

### Lyra动画

1. Linked Animation Layer

2. ``ABP_Mannequin_Base``定义状态转换规则(状态机)，通过接口:``ALI Item Anim Layers``预留``Linked Anim Layer``交由各个子类去实现
3. ``ABP_ItemAnimLayerBase``实现接口具体逻辑，但不设置具体的``Animation``(这些``Animation``在该蓝图中的变量定义，可以继承给子类)
4. 其余动画蓝图类继承``ABP_ItemAnimLayerBase``，设置不同的``Animation``，例如手枪,空手的动画蓝图

> 手枪/空手动画蓝图的子类，应该不能覆盖接口重写具体逻辑！

[从网格拷贝动画](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/working-with-modular-characters-in-unreal-engine#%E4%BB%8E%E7%BD%91%E6%A0%BC%E4%BD%93%E5%A4%8D%E5%88%B6%E5%A7%BF%E5%8A%BF)

### 动画重定向

1. IK也需要添加解算器的根骨骼
2. 整个骨骼也需要添加一个根骨骼
3. 2个根骨骼不同

[lyra动画讲解地址](https://www.bilibili.com/video/BV1yG4y187y6/?spm_id_from=333.337.search-card.all.click&vd_source=222dac9e1f77e83867423a3b22e2f313)

### 修改lyra动画

[动画博客](https://www.unrealengine.com/zh-CN/tech-blog/adapting-lyra-animation-to-your-ue5-game)


### 修改父类默认子组件类型的方法

例如：修改``MovementComponent``为``ULyraCharacterMovementComponent``

```
ALyraCharacter::ALyraCharacter(const FObjectInitializer& ObjectInitializer)
	: Super(ObjectInitializer.SetDefaultSubobjectClass<ULyraCharacterMovementComponent>(ACharacter::CharacterMovementComponentName))
{
}
```

> 通过ObjectInitializer实现




