# C++与UE编辑器的交互

### UPROPERTY 宏定义

将变量参与反射系统中，可以被编辑器和蓝图识别，参与垃圾回收
属性说明符：
1. VisibleAnywhere
2. VisibleDefaultsOnly
3. VisibleInstanceOnly
4. EditAnywhere
5. EditInstanceOnly
6. EditDefaultsOnly
7. BlueprintReadOnly
8. BlueprintReadWrite

- AnyWhere = 实例 + Detail
- Default = Detail，也就是CDO?
- Instance = 编辑器中的实例Detail
- Visible 不可编辑
- Edit 可编辑
- BlueprintReadOnly 表示事件图(EventGraph)可读
- BlueprintReadWrite 表示事件图(EventGraph)可读可写

### UFUNCTION宏属性

#### ``Server``表示只在服务端运行的函数

#### ``Reliable``表示可靠传输，即使发生网络错误，也会进行重传
