# UE5碰撞与重叠


### 碰撞/重叠的条件

1. 生成碰撞/重叠事件
2. A对B的类型的碰撞响应不为ignore
3. B对A的类型的碰撞响应不为ignore

### 自定义碰撞类型

1. 编辑器中添加的自定义碰撞类型
2. 在C++中为``ECollisionChannel::ECC_GameTraceChannel1``从1开始顺序编号
3. 一般通过宏定义提高语义
