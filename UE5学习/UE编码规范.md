# UE编码规范


[参考官方指南](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/epic-cplusplus-coding-standard-for-unreal-engine)



### 字符串周围固定使用``TEXT()``宏

> ``TEXT()``可以在编译期间直接生成正确编码的字面量，避免了在运行时构造``FString``的额外转换，也防止出现乱码等问题。
> 因为``FString``是基于UE的宽字符``TCHAR``，``TCHAR``在不同平台下有不同的宽度，可以在编译时期通过``TEXT()``转换为对应的宽度形式，例如``L""``。
> 总之是为了跨平台所做出的优化方法

   
