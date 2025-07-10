# MVC与MVVM架构

### MVC

核心思想：Controller 改变 Model，Model 驱动 View 刷新

1. Model层职责为数据存储与底层数据交互
2. View层职责为界面展示
3. Controller层职责是响应用户操作

UE中MVC的例子：
1. Model层：Pawn/Character
2. View层: StaticMeshComponent/SkeletalMeshComponent
3. Controller层：PlayerController

过程：
1. 通过InputComponent绑定映射
2. 按键回调中控制pawn

### MVVM

MVVM是MVC的改良版，解决MVC中Contorller在项目后期过于臃肿的问题

1. Model和View的职责基本相同
2. View-Model的职责是负责桥接 Model 与 View，一般通过双向绑定的方式实现这种桥接

优点：
1. UI 与业务逻辑完全分离，(解耦合),解决MVC模式中各个模块通信混乱的问题
2. 双向绑定减少样板代码

### MVC和MVVM的区别

1. MVVM是MVC的改良版，解决MVC中Contorller在项目后期过于臃肿的问题
2. MVVC中Model和View不直接通信


