# UE字符串转换

[参考文档](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/string-handling-in-unreal-engine)

### 窄字符

常用于保存单个的ASCII字符集，如果用于保存其他语言字符，则会出现乱码

字节数1，8位，256个

### 宽字符

用于表示Unicode字符集，在UE中使用TChar

字节数2

UTF-8是字符集的编码规则，对于ASCII采用单字节8位存储，其他采用多字节存储

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


