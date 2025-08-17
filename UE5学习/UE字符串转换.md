# UE字符串转换

[参考文档](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/string-handling-in-unreal-engine)

### 窄字符

常用于保存单个的ASCII字符集，如果用于保存其他语言字符，则会出现乱码

字节数1，8位，256个

### 宽字符

用于表示Unicode字符集，在UE中使用TChar

字节数2

UTF-8是字符集的编码规则，对于ASCII采用单字节8位存储，其他采用多字节存储

### TChar

是UE对本地支持的字符集做的处理，对于不同平台兼容不同的字符宽度，具有可移植性

> A switchable character. In-memory only. Either ANSICHAR or WIDECHAR, depending on a licensee's requirements.

### FString

UE的可变字符串，类似与C++``std::string``

1. 字符串比较时：逐个比较，因为每一个FString在内存中会创建一个TArray<TChar>

### TEXT("")

使用场景：在使用到字符串字面量时，都应该使用TEXT()宏，以保证跨平台的兼容性

根据编辑器的设置，确定目标平台，再根据目标平台进行TEXT宏的定义：
```
#define UTF16TEXT(x) UTF16TEXT_PASTE(x)
#define WIDETEXT(str) WIDETEXT_PASTE(str)
#define UTF8TEXT_PASTE(x)  u8 ## x
#define UTF16TEXT_PASTE(x) u ## x
#if PLATFORM_WIDECHAR_IS_CHAR16
	#define WIDETEXT_PASTE(x)  UTF16TEXT_PASTE(x)
#else
	#define WIDETEXT_PASTE(x)  L ## x
#endif

// If we don't have a platform-specific define for the TEXT macro, define it now.
#if !defined(TEXT) && !UE_BUILD_DOCS
	#if PLATFORM_TCHAR_IS_UTF8CHAR
		#define TEXT_PASTE(x) UTF8TEXT(x)
	#else
		#define TEXT_PASTE(x) WIDETEXT(x)
	#endif
	#define TEXT(x) TEXT_PASTE(x)
#endif
```

TEXT宏将字符串转换为平台兼容的字符编码，例如``const char8_t[] = u8"..."``。使用utf8编码保存和解释内容。

如果不使用TEXT()宏包裹字符串，那么会使用默认的ASCII编码，注意不要加入其他非法字符。

### FName

FName存储着一个实例编号(id)以及指向给定字符串的索引引用，以便快速查找和访问(实现FName到FString/FText的转换)。

此外，FName子系统使用**哈希表**提供快速的字符串到FName转换。

特点：
1. 对比高效
2. 不可改变
3. 全局唯一
4. 不区分大小写

场景：
1. 骨骼名称
2. UObject名称
3. 资源名称

### FText

本地化文本，专门用于UI展示

在做本地化时，编辑器会将所有FText汇总，用于翻译
