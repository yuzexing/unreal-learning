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




> 待补充
