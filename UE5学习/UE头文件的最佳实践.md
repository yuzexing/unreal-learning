# UE头文件的最佳实践

### 最佳实践一：
只能在以下必要情况中在 .h 文件中 include 其他 .h 文件：
1. 继承时需引入父类的.h文件
2. 需要知道类具体大小时（创建类的实例）
3. 访问类成员、方法时

其他时候使用前向声明，例如：
```
...
UPROPERTY(VisibleAnywhere)
class UCapsuleComponent* Capsule;
```

所以：仅在.cpp文件中引入需要使用的.h文件

### 原因：

假设在.h文件引入其他非必要的.h文件，那么会引入额外的无用的代码，导致增加代码的体积，举例：
```
A.h
#include "base1.h"
#include "base2.h"
#include "base3.h"
class A {};
```
```
B.h
#include "base4.h"
#include "base5.h"
#include "base6.h"
#include "A.h"
class B {
    A* p;
};
```

```
C.h
#include "base7.h"
#include "base8.h"
#include "base9.h"
#include "B.h"
class C {
    B* p;
};
```

``C.h``的代码会变得非常多，如果``c.cpp``文件引入了``C.h``，那么会引入非必要的.h文件


还会导致：
1. 影响编译性能，如果头文件之间存在依赖关系，则会重新编译依赖于该头文件的所有头文件
2. 耦合度提升
3. 破坏封装性，使用者需要查看头文件内的具体内容，看看到底引入了什么
4. 循环依赖，如果在上述例子中，``A.h``中不小心的引入了``C.h``，因为A可能不知道C.h中引入了A.h

