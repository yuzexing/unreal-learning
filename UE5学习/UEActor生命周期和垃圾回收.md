# UEActor生命周期和垃圾回收

[生命周期文档](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/unreal-engine-actor-lifecycle#actor%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E7%BB%88%E7%82%B9)
[垃圾回收文档](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/garbage-collection-settings-in-the-unreal-engine-project-settings)
[UObject处理文档](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/unreal-object-handling-in-unreal-engine)
[垃圾回收四部流程](https://dev.epicgames.com/community/learning/knowledge-base/ePKR/unreal-engine-garbage-collector-internals)

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
### UObjectPtr和原始指针的区别？

问题一：为什么要使用UObjectPtr替换原始指针?

UObjectPtr的64位中，额外的存放了对象的信息，支持编辑器级别(非Release)的：
1. 动态解析（懒加载）
2. 信息封装

> 待补充(https://zhuanlan.zhihu.com/p/504115127)

### 增量式垃圾回收

什么是增量式垃圾回收？
将垃圾回收划分为多个阶段进行，避免在一帧里回收过多的内存，导致卡顿，阻塞主线程。

问题一：为什么以前不能增量式垃圾回收，使用UObjectPtr却可以？

UObjectPtr是对裸指针的包装，在对指针赋值、构造、移动构造、拷贝时，会将拷贝的对象立即标记为可达，避免增量式垃圾回收的误删除。

同时将该对象加入到初始节点列表(不是根节点列表)，会在下一帧对其引用对象进行可达性分析，避免GC的误删除。

补充：如果说该对象本身是不可达到的，则下一次会被正常回收。

[blog参考](https://santa.wang/ue5-incremental-garbage-collection/)

[官网参考](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/incremental-garbage-collection-in-unreal-engine?application_version=5.5)
