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

> 待补充
