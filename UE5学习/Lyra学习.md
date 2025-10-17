# Lyra学习

### Experience

1. 体验是什么？是lyra自定义的吗？
> 是的
2. 


### Lyra动画

1. Linked Animation Layer



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

