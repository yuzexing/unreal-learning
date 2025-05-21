# C++预编译头文件

PCH，precompiled header file

主要用于处理固定的头文件，**加快编译速度**，例如标准模板库，WindowsAPi，C++库

也可以把自己的写的文件放进去，但是如果修改，则会重新编译所有的预编译头文件。


### 样例

当不使用预处理头文件时，

```
// pch.h 文件
#pragma once

#include <algorithm>
#include <iostream>
#include <functional>
#include <memory>
#include <thread>
#include <utility>


// data structures
#include <string>
#include <stack>
#include <deque>
#include <array>
#include <vector>
#include <map>
#include <set>
#include <unordered_set>
#include <unordered_map>


// Windows API
#include <Windows.h>
```
```
// pch.cpp
#include "pch.h"
```
```
// main.cpp
#include "pch.h"

int main() {
    int i = 3;                 // i is not declared const
    const int& rci = i;
    const_cast<int&>(rci) = 4; // OK: modifies i
}
```

输出预处理文件的大小：main.i和pch.i 各40万行：

![image](https://github.com/user-attachments/assets/3c721f65-c35d-4fcf-bc1e-67bc8a3c181a)

### 设置预处理头文件

1. 创建一个包含"预处理头文件"的cpp文件
2. 1中cpp文件右击属性-> C/C++ -> Precompiled Headers -> Precompiled Header -> create(/Yc)
3. 项目右击属性-> C/C++ -> Precompiled Headers -> Precompiled Header -> Use(/Yu)
3. 项目右击属性-> C/C++ -> Precompiled Headers -> Precompiled Header File -> pch.h

### 编译速度对比

1. 关闭预处理文件输出
2. 打开编译时间显示

未开启预编译头文件的首次编译时间：1.9s

![image](https://github.com/user-attachments/assets/0f3bdb39-e1d6-456e-ad28-fd86515d5f72)

未启预编译头文件后修改main.cpp的编译时间：0.8s

![image](https://github.com/user-attachments/assets/44707724-a041-44da-8cce-a774e0e3e06b)

开启预编译头文件的首次编译时间：1.4s

![image](https://github.com/user-attachments/assets/abf696e9-f708-4da4-a08b-0ea4a7298a86)

开启预编译头文件后修改main.cpp的编译时间：0.3s

![image](https://github.com/user-attachments/assets/145c30c6-eb02-48fb-a2e0-6e879c1dc2b1)

### 问题一：为什么开启预编译头文件和未开启预编译头文件的首次编译时间也会有差异？

本质是缓存，空间换时间。对于配置了预编译的头文件，只需编译一次（一次预处理，词法分析，语法分析，语义分析）然后作为缓存，后续复用，以加快速度。

当整个项目只有一处使用时，几乎不会减少编译时间，可能还会增加编译时间（预编译的头文件作为缓存的IO时间）


### 问题二：预编译头文件为什么能提高编译速度？

将头文件中的内容预处理+编译后作为二进制缓存，使得相同的预处理头文件不需要再次进行预处理+部分编译阶段。

[wikipedia](https://en.wikipedia.org/wiki/Precompiled_header)|
[stackoverflow](https://stackoverflow.com/questions/903228/why-use-precompiled-headers-c-c)


### 问题三：如果修改部分头文件后，是全部编译还是部分编译

> 如果预编译头文件之间相互独立，没有依赖关系，则部分编译
> 这种部分编译是有编译器保证的，叫做增量编译功能

