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

> 待补充
