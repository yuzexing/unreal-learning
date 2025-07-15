# C++ algorithm

### std::sort

适用于任何有迭代器的容器，可以将[begin, end)内的数据进行排序

### 代码
```
#include <iostream>
#include <vector>
#include <algorithm>
#include <functional>

int main() {
	std::vector<int> v{1,3,9,7,4,1};
	// 默认升序
	std::sort(v.begin(), v.end());
	// greater<int>() 指定降序
	std::sort(v.begin(), v.end(), std::greater<int>());
	std::sort(v.begin(), v.end(), [](int a, int b) {
		// true 则a 在前，false b在前
		return a < b;
		});
	std::sort(v.begin(), v.end(), [](int a, int b) {
		// 将1排到最后
		if (a == 1) {
			return false;
		}
		if (b == 1) {
			return true;
		}
		return a < b;
		});
	for (int i : v) {
		std::cout << i << std::endl;
	}
}
```
### 分析
平均时间复杂度O(nlogn)，空间复杂度取决于具体的排序算法，不稳定，稳定的可以使用std:stable_sort

### std::lower_bound

二分查找：查找到第一个大于等于目标值的迭代器，如果所有元素都小于，则返回end

要求：有序








