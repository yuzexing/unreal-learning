# AI行为树

### AIController

只在服务端运行，PossesedBy函数也仅在服务端执行

### 行为树各个模块的功能

1. BehaviorTreeService依附于节点，在节点执行，修改BlackBoard中的数据

可以设定执行频率

2. Decorator 用于设定满足的条件

3. Task 用于执行任务


### Selector和Sequence的功能

1. Selector从左到右依次执行，直到有一个子节点执行**成功**
2. Sequence从左到右依次执行，直到有一个子节点执行**失败**


### 装饰器节点

1. Nofity Observer的作用：
结果改变时重新评估与值改变时重新评估，重新评估是指重新计算条件是否满足（结果改变时重新评估难道不是依赖于值改变时重新评估实现的吗）

2. Observer aborts：
2.1 self 指终止自身及其子节点。
2.2 low priority指终止右侧所有节点。

[文档](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/unreal-engine-behavior-tree-node-reference-decorators)

### 环境查询系统EQS

1. EQS在哪里使用？
2. EQS如何查询指定的Actor？
3. EQS如何利用AIPerception感知场景？
4. EQS的工作方式是什么？

 
> 1. EQS在BT中，执行BTT，Run EQS处执行
> 2. EQS可以使用Perceived Actor生成器或者Actor Of Class生成器来获取Context下的Actor，在使用测试器Test过滤Actor
> 3. EQS中有一个Perceived Actor生成器，可以指定AISense的类型，来过滤刺激源
> 4. EQS在BTT中使用，EQS下一般包含一个生成器，生成器中指定上下文(context场景)来提供备选的Actor，再通过Test打分(Tester也需要指定上下文)，返回给BTT中

### 如何在AIsense中设置只识别敌人？

在AIController中，继承IGenericTeamAgentInterface，设置自己的ID和对不同的队伍的敌对状态
```

ETeamAttitude::Type ALyraPlayerBotController::GetTeamAttitudeTowards(const AActor& Other) const
{
	if (const APawn* OtherPawn = Cast<APawn>(&Other)) {

		if (const ILyraTeamAgentInterface* TeamAgent = Cast<ILyraTeamAgentInterface>(OtherPawn->GetController()))
		{
			FGenericTeamId OtherTeamID = TeamAgent->GetGenericTeamId();

			//Checking Other pawn ID to define Attitude
			if (OtherTeamID.GetId() != GetGenericTeamId().GetId())
			{
				return ETeamAttitude::Hostile;
			}
			else
			{
				return ETeamAttitude::Friendly;
			}
		}
	}

	return ETeamAttitude::Neutral;
}

```

> 待补充
