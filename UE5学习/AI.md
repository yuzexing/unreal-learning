# AI

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
