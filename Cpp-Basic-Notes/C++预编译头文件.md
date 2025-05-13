# C++预编译头文件

PCH，precompiled header file

用于处理固定的头文件，**加快编译速度**，例如标准模板库，WindowsAPi，C++库

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

未启预编译头文件后修改main.cpp的编译时间：0.9s
![image](https://github.com/user-attachments/assets/7bcc5aa4-e53c-4e7a-84eb-941dfccc5a9e)

开启预编译头文件的首次编译时间：1.4s

![image](https://github.com/user-attachments/assets/abf696e9-f708-4da4-a08b-0ea4a7298a86)

开启预编译头文件后修改main.cpp的编译时间：0.3s

![image](https://github.com/user-attachments/assets/145c30c6-eb02-48fb-a2e0-6e879c1dc2b1)




