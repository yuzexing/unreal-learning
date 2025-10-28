# UE5 GAS

### UAbilitySystemComponent和AttributeSet是怎么绑定的？

通过``Owner``关联，``UAbilitySystemComponent``会从``Owner``的属性中获取``AttributeClass``的指针
```
const UAttributeSet* UAbilitySystemComponent::GetOrCreateAttributeSubobject(TSubclassOf<UAttributeSet> AttributeClass)
{
	AActor* OwningActor = GetOwner();
	const UAttributeSet* MyAttributes = nullptr;
	if (OwningActor && AttributeClass)
	{
		MyAttributes = GetAttributeSubobject(AttributeClass);
		if (!MyAttributes)
		{
			UAttributeSet* Attributes = NewObject<UAttributeSet>(OwningActor, AttributeClass);
			AddSpawnedAttribute(Attributes);
			MyAttributes = Attributes;
		}
	}

	return MyAttributes;
}

const UAttributeSet* UAbilitySystemComponent::GetAttributeSubobject(const TSubclassOf<UAttributeSet> AttributeClass) const
{
	for (const UAttributeSet* Set : GetSpawnedAttributes())
	{
		if (Set && Set->IsA(AttributeClass))
		{
			return Set;
		}
	}
	return nullptr;
}
```

### GAS中的GameplayEffect Components的作用

[博客](https://www.quodsoler.com/blog/how-to-use-gameplay-effect-components-in-unreal-engine-5)


### AttributeSet中属性Clamp的时机

1. PreAttributeChange
2. PostGameplayEffectExecute

1. 在``PreAttributeChange``中只能修改查询``modifier``之后的值，如果再次进行计算，则需要重新clamp
2. 在``PostGameplayEffectExecute``中，进行Clamp并SetValue，则可以修改``modifier``结果；值被修改但是没有被同步到客户端，所以此次clamp只会被网络同步一次

[知乎](https://zhuanlan.zhihu.com/p/464303056)

### 次级属性依赖关系

<img width="1177" height="564" alt="image" src="https://github.com/user-attachments/assets/aa1d17e2-e375-413a-b993-b852185c4473" />

### ModifierMagnitudeCalculation为什么需要传入Tags?

```
float UMMC_MaxHealth::CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec& Spec) const {
	// return Super::CalculateBaseMagnitude_Implementation(Spec);
	// 为什么要SourceTags和TargetTags？
	FAggregatorEvaluateParameters EvaluationParameters;
	EvaluationParameters.SourceTags = Spec.CapturedSourceTags.GetAggregatedTags();
	EvaluationParameters.TargetTags = Spec.CapturedTargetTags.GetAggregatedTags();
	float Vigor;
	GetCapturedAttributeMagnitude(CaptureDef, Spec, EvaluationParameters, Vigor);
	return Vigor;
}
```

因为tags需要参与判断Must have tags、Must Not Have Tags和query Must match等设置，决定该MMC是否生效

### Gameplay Ability中无需修改的设置

1. 网络复制模式：因为GA只在服务端运行，从服务端复制到客户端
2. 服务端允许客户端结束GA：应当以服务器为权威
3. 直接复制输入：输入事件很频繁导致大量RPC汇聚到服务端，应当使用``Generic Replicated Events``替换

### 创建GameplayEffect的流程和作用

1.  MakeEffectContext
2.  MakeOutgoingSpec
3.  ApplyGameplayEffectSpec

1. FGameplayEffectContext包含发起源，发起情况
2. FGameplayEffectSpec中包含一个GameplayAttributeSet的快照，GESpec创造好后，即准备好施加到任何目标上
3. 将GA应用到目标上


### AbilitySystemGlobals 的配置方式

1. 配置文件DefaultGame.ini
2. [/Script/GameplayAbilities.AbilitySystemGlobals]
+AbilitySystemGlobalsClassName="/Script/ProjectName.CustomAbilitySystemGlobalsFileName"

### GAS中的网络复制与客户端预测

只在服务端运行的：
1. AttributeSet(客户端被复制)
2. 授予GA

同时在客户端和服务端运行的：
1. GameplayEffect
2. GameplayAbility（客户端和服务端，客户端预测）

### GA_FireBolt和GA_HitReact的区别

GA_FireBolt的GE只能在服务端执行，从而导致``Enemy``的``GA_HitReact``在服务端执行，所以``GA_HitReact``不会在客户端执行，只会在服务端执行，通过``GameplayAbility``中的``PlayMontageAndWait``来实现``NetMulticast``，在所有客户端中播放受击蒙太奇动画。

由于是在``UAttributeSet``进行判定并且执行GE，所以很难使用RPC使所有客户端执行GE。

### Gameplay Cue的类型以及区别

> Gameplay Cue用于视觉效果/音频效果等反馈效果

大类分为：
1. GCN Staic 不会创建实例，对应 instant 和 periodic的GE，不需要关心生命周期
2. GCN Actor 拥有实例，需要绑定一个Actor，对应infinity和has duration的GE

### Gameplay Cue Path

指定文件夹，用于加快GCN搜索的速度，避免全局文件搜索

### Gameplay Cue Notify Actor

> 是Actor的子类，会创建Actor，需要清楚Actor或者自动销毁，可以执行延迟等效果

#### Gameplay Cue Notify BurstLatent

> 可以做latent thing 例如延迟/时间线等的Burst

#### Gameplay Cue Notify Looping

> 用于处理连续循环的效果，通过添加/删除来控制GCN

### Gameplay Cue Notify Static

GCN的静态函数，不会创造对象

#### Gameplay Cue Notify Burst

> GCN staic的子类，无法执行循环，延迟等效果，必须立即执行
> 拥有GCN Effect，可以执行爆炸粒子效果，声音，镜头，设备效果/Burst decal

> Burst decal 是材质的修改，例如击碎效果，弹孔

### local Gameplay Cue Notify

什么时候用非复制的本地GCN?

> 当一个技能已经被复制了，例如火球术的火球已经是被复制的了 hit后，会播放声音，那么这个不需要再复制了

> 在GameplayCueManager中，有可以调用非复制的 GCN 函数

```
/** 
	 *  Convenience methods for invoking non-replicated gameplay cue events. 
	 * 
	 *	We want to avoid exposing designers the choice of "is this gameplay cue replicated or non-replicated?".
	 *	We want to make the decision for them in most cases:
	 *	- Abilities will always use replicated GameplayCue events because they are not executed on simulated proxies.
	 *	- Animations always use non-replicated GameplayCue events because they are always executed on simulated proxies.
	 *	
	 *  Sometimes it will be useful to give designers both options: in actor classes where there are many possible use cases.
	 *  Still, we should keep the choice confined to the actor class, and not globally.  E.g., Don't add both choices to the function library
	 *  since they would appear everywhere. Add the choices to the actor class so they only appear there.
	 */
	static UE_API void AddGameplayCue_NonReplicated(AActor* Target, const FGameplayTag GameplayCueTag, const FGameplayCueParameters& Parameters);
	static UE_API void RemoveGameplayCue_NonReplicated(AActor* Target, const FGameplayTag GameplayCueTag, const FGameplayCueParameters& Parameters);
	static UE_API void ExecuteGameplayCue_NonReplicated(AActor* Target, const FGameplayTag GameplayCueTag, const FGameplayCueParameters& Parameters);

```

> 待补充
