# AI行为树

### AIController

只在服务端运行

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

> 待补充
