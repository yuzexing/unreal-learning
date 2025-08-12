# UE字符串转换

[参考文档](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/string-handling-in-unreal-engine)

> 待补充

### FString

UE的可变字符串，类似与C++``std::string``

1. 字符串比较时：逐个比较，因为每一个FString在内存中会创建一个

### TEXT("")

使用场景：在使用到字符串字面量时，都应该使用TEXT()宏，以保证跨平台的兼容性
用于跨平台的

### FName

### FText

本地化文本，专门用于UI展示

### "Text"


