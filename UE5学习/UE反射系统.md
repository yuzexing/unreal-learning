# UE反射系统

### 保存元数据

UE采用UHT的方式搜集并生成反射所需代码，跟随源代码一起编译。
通过宏标记例如``UPROPERTY,UFUNCTION,UCLASS``等标记需要收集的元数据

#### "xx.generate.h"文件是什么？

是UHT生成的头文件，包含：
1. 通过友元类定义反射数据结构体声明
2. 序列化接口声明
3. 注册反射系统的函数声明
4. UE的宏定义（UPROPERTY，UFUNCTION等）

> 时机：在编译时，UBT会调用UHT收集并生成该文件，然后进入预处理，编译，汇编，链接阶段

#### GENERATED_BODY()是什么？

在预处理阶段时，进行宏展开，将xx.generate.h中定义的数据注入到类中
1. 插入反射系统所需的元数据的定义
2. 反射系统的注册函数

#### 为什么是将反射数据写入代码？

避免产生一致性问题，保证运行时获取最新的反射数据。

### 收集元数据

在main函数执行前，采用静态收集延迟注册元数据(将收集的数据存储到全局的TArray中)

#### 为什么延迟注册？

加快程序启动速度

### 消费元数据

通过``FindClass``,``FindPropertyByName``,``FindFunctionByName``等使用元数据

其中返回的UClass* 包含了元数据以及响应的构造函数指针。

``FindPropertyByName``内部通过记录成员的偏移量实现访问内存中地址，通过``FProperty*``的``FName``比较，找到对应的``FProperty*``

``FindFunctionByName``内部通过记录对应的函数指针实现
