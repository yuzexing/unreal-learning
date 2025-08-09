# UE5网络同步

### 网络概述

UE5采用C/S架构，对于**单人游戏**或**本地多人游戏**，玩家输入到游戏不存在隐患

对于联网多人游戏，所有客户端连接至一个权威服务器，服务器将游戏状态与每一个客户端共享，客户端之间通过也服务器进行通信。

当出现网络不稳定，导致的输入丢失时，各个客户端会状态可能不同，但一切以权威服务器上的状态为准。

所以说，**服务器**是游戏真正运行的地方，**客户端**各种控制他们在**服务器**上拥有的远程Pawn。

具体说：**客户端**通过**本地Pawn**发送**远程程序调用**(RPC)到**服务端**以在游戏中进行操作，**服务端**复制游戏状态信息到每一个客户端(包括RPC发送端)，客户端使用复制的信息，**近似的模拟**服务端实际上正在发生的情况。

总结：客户端将操作发送到服务端，服务端执行，将状态复制到各个客户端。

> 网络带宽: 单位时间内可以传输的数据

[官方参考](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/networking-overview-for-unreal-engine)


### 问题一：如果说通过RPC像服务端发送操作，例如移动，那么当下一次RPC丢失了，角色是会进行移动预测吗？也就是说连续的状态在离散的RPC下是怎么做的？

### 问题二：如果说本地的操作

### 网络模式

1. 单机，被视为服务器，但不接受客户端连接，可以运行客户端和服务端逻辑
2. 专用服务器，没有本地玩家的服务器，只接收客户端连接，放弃图形、声音和输入等面向玩家的功能
3. 监听服务器，有一个本地玩家的服务器，可以接收客户端连接，可以运行客户端和服务端逻辑
4. 客户端，连接到远程服务器的客户端，客户端不运行服务端逻辑

### Actor网络复制规则

1. 属性变化时复制
2. 按照``AActor::NetUpdateFrequency``设置的频率复制
3. 只复制到相关的连接(Actor相关性)

对于不连贯的移动，会做移动推算
1. 对于``ROLE_AutonomousProxy``来自玩家的模拟代理，会有更多的信息做推算，相比于``ROLE_SimulatedProxy``
2. 复制永远是单向的，只存在与服务端向客户端的**单向复制**

### Remote Role & Local Role

1. ``Local Role``，用于区分客户端上的控制身份
2. ``Remote Role``，用于区分服务端上的控制身份

通过这两个属性，能够知道：
1. 谁拥有Actor的主控权(Authority)
2. Actor是否被复制
3. 复制模式

1. 主控权判断： LocalRole == Authority
2. Actor复制判断：LocalRole == Authority && RemoteRole == Simulate or Autonomous
3. 复制模式判断：LocalRole中的 Autonomous表示本地玩家，Simulated表示从服务端的模拟代理

> 补充：主控权是说某个Actor的数据是否权威，其他客户端上的数据是否来源于该Actor

[参考](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/actor-role-and-remote-role-in-unreal-engine)

### Actor所有者和所属连接

概念：
1. 每一个客户端产生一个连接（连接可以简单的翻译成客户端）
2. 客户端玩家控制器P，连接到服务器时，会产生一个服务端玩家控制器SP
3. 玩家控制器P和SP关联同一个连接
4. Actor所属连接，是Actor所属玩家控制器关联的连接
5. 玩家控制器是Pawn的所有者，同理也是Actor的所有者
6. Actor所有者和所属连接，可以**确定哪个客户端拥有修改和RPC的权力**
7. 当Actor不属于某个playerController后，Actor也不属于该链接

> Actor的所有者是玩家控制器
> 
> 玩家控制器是玩家在游戏中的化身

#### 所属连接的功能

连接的所有权可以下列行为有关
1. Actor的复制，考虑Actor相关性，只复制到相关的连接
2. 属性的复制，属性的条件复制可能会涉及到所属连接（暂时没有使用过）
3. RPC，服务端执行到客户端的RPC时，需要由Actor的所属连接确定RPC的连接/发送的目标

#### Actor所有者查询

查询Actor的所有者，需要查询最外层Actor的所有者，是否是玩家控制器，是则Actor属于该玩家控制器

组件查询所有者，先查询其从属链，直到找到所属Actor，再按照查询Actor的方式进行所有者查询

[官方参考](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/actor-owner-and-owning-connection-in-unreal-engine)

### RPC类型

1. Client RPC
2. Server RPC
3. Remote RPC
4. MultiCast RPC

[调用规则](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/remote-procedure-calls-in-unreal-engine#%E8%BF%9C%E7%A8%8Brpc)

总结：
1. 允许从客户端所有权的Actor上调用Server RPC，在服务端执行
2. 服务端运行调用并在本地执行一切Server RPC
3. 从客户端调用Client RPC只会在本地执行
4. 从服务端调用Client RPC，在Actor所属的客户端/服务端执行
5. 从客户端所属Actor上调用Remote RPC，会在服务端执行
6. 从服务端上调用Remote RPC，在属于客户端的Actor时，会在其所属客户端执行
7. 服务端执行的多播RPC会在所有客户端和服务端执行
8. 客户端调用的多播RPC，只会在本地执行

### RPC可靠性

1. 可靠，确认应答，保证有序，持续发送，暂停后续可靠RPC的发送（注意：只保证Actor内的可靠RPC有序，Actor之间的可靠RPC不一定有序）
2. 不可靠，无序

> 对于高频的操作，最好不使用可靠RPC，可能会导致队列溢出
>
> 不可靠RPC的执行顺序是无法保证的，可能提前执行或者不执行（丢失或者晚到不执行）

