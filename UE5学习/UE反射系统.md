# UE反射系统

### 保存元数据

UE采用UHT的方式搜集并生成反射所需代码，跟随源代码一起编译。
通过宏标记例如``UPROPERTY,UFUNCTION,UCLASS``等标记需要收集的元数据

#### 为什么是代码不是数据？

避免产生一致性问题

### 收集元数据

在main函数执行前，采用静态收集延迟注册元数据(将收集的数据存储到全局的TArray中)

#### 为什么延迟注册？

加快程序启动速度

### 消费元数据

通过``FindClass``,``FindPropertyByName``,``FindFunctionByName``等使用元数据

其中返回的UClass* 包含了元数据以及响应的构造函数指针。

``FindPropertyByName``内部通过记录成员的偏移量实现

``FindFunctionByName``内部通过记录对应的函数指针实现
