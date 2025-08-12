# UEActor生命周期和垃圾回收

[生命周期文档](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/unreal-engine-actor-lifecycle#actor%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E7%BB%88%E7%82%B9)
[垃圾回收文档](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/garbage-collection-settings-in-the-unreal-engine-project-settings)
[UObject处理文档](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/unreal-object-handling-in-unreal-engine)

### Actor生命周期

创建：从PIE创建，复制Actor；其他：从读取Actor数据，创建Actor；

结束：``EndPlay``函数被执行。包括``Destroy``，关卡卸载，应用程序关闭，Actor生命周期到期等。

当Actor生命周期结束时：
1. 从关卡Actor列表删除
2. 禁用Tick函数
3. 标记为待垃圾回收
4. 递归卸载子Actor和子组件

当垃圾回收Actor时：
1. 执行垃圾回收的生命周期函数
2. 判断是否被主线程占用(可以设置强制删除的判断次数，但可能出现问题)
3. 将指向其的反射系统可见的指针[清空](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/unreal-object-handling-in-unreal-engine)
4. 回收堆内存

### 潜在的问题

当一个Actor被``Destroy``了，那么指向他的指针依然不为nullptr，除非进行垃圾回收，那么怎么判断一个Actor是否是有效的呢？

在使用一个指针前，先使用``IsValide``函数进行判断，而不是判断指针是否为空



```
if (IsValid(TestActor) {
  TestActor->DoSomeThing();
}

/**
* Test validity of object
*
* @param	Test			The object to test
* @return	Return true if the object is usable: non-null and not pending kill or garbage
*/
FORCEINLINE bool IsValid(const UObject *Test)
{
	return Test && FInternalUObjectBaseUtilityIsValidFlagsChecker::CheckObjectValidBasedOnItsFlags(Test);
}
```


