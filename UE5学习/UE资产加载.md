# UE资产加载

### 主要资产和次要资产

1. 主要资产是通过AssetManager去控制加载
2. 次要资产是被主要资产引用后，进行依赖的加载

### 资产优先级

1. 当一个次要资产被多个主要资产引用时，会分配给优先级高的主资产进行管理。

### 资产路径

1. Content = /Game
2. Engine = /Engine
3. Plugins = /xxxxModule


### 资产引用

1. ObjectPtr(硬引用)
2. ObjectClass(类的硬引用)
3. SoftObjectPtr(软引用)
4. SoftObjectClass(类的软引用)

1. 举例：在蓝图资产中，如果spawnActor或者new Object，其中指定的Class类型，也会是硬引用！一定要注意


### Lyra资产

