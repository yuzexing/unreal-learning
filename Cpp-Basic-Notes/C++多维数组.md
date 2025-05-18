# C++多维数组

### 本质

除了最里层的数组存储业务数据，外围的都是指针数组，保存数组的地址。

```
#include <iostream>
#include <vector>

int main() {
	// 一维数组1x3
	int* a = new int[3];

	delete[] a;
	// 二维数组3x3
	int** b = new int*[3];
	for (int i = 0; i < 3; i++) {
		b[i] = new int[3];
	}
	for (int i = 0; i < 3; i++) {
		delete[] b[i];
	}
	delete[] b;

	// 三维数组3x3x3
	int*** c = new int** [3];
	for (int i = 0; i < 3; i++) {
		c[i] = new int*[3];
		for (int j = 0; j < 3; j++) {
			c[i][j] = new int[3];
		}
	}
	c[0][0][0]; // 访问

	// 释放

	for (int i = 0; i < 3; i++) {
		for (int j = 0; j < 3; j++) {
			delete[] c[i][j];
		}
		delete[] c[i];
	}
	delete[] c;

}
```

### 性能优化

代码：
主要是演示多维数组的原理，实际项目应当使用智能指针或者容器类(std::vector、std::array)管理动态数组，因为这样：
1. 容易产生内存碎片
2. 容易内存泄漏（管理不当、疏忽的情况下）
3. 容易未命中cache，没有连续的存储数组（也是导致1的原因）


优化方案：
1. 将所有数组通过一次new生成
2. 不使用中间指针记录数据地址

> 尽量不使用二维数组，而是用一维数组代替，性能会好很多。

```

int main() {
	int* arr = new int[27];

	for (int x = 0; x < 3; x++) {
		for (int y = 0; y < 3; y++) {
			for (int z = 0; z < 3; z++) {
				arr[x + 3 * y + 3 * 3 * z] = 2;
			}
		}
	}
	int x = 1;
	int y = 1;
	int z = 2;
	// 访问 (x,y,z) 的数据
	std::cout << arr[x + 3 * y + 3 * 3 *z] << std::endl;

	delete[] arr;
	// 删除也更简单
	// 占用空间也更少

}
```

### 二维动态数组的初始化方式

```
vector<vector<int>> test(size1, vector<int>(size2, defaultValue));
```


