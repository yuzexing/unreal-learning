# C++快速排序

算法思想：分治法
时间复杂度：O(nlogn)
最坏时间复杂度：O(n²)
空间复杂度：O(n)
稳定性：不稳定

实现步骤：
1. 确定中枢点
2. 将大于中枢点的值置于右侧，小于中枢点的值置于左侧
3. 分别处理左右两个子数组


### 代码实现：左右指针法


```
void quickSelect(vector<int>& nums, int l, int r) {
    if (l == r) {
        return;
    }
    int partition = nums[l], i = l - 1, j = r + 1;
    while (i < j) {
        do i++; while (nums[i] < partition);
        do j--; while (nums[j] > partition);
        if (i < j) {
            std::swap(nums[i], nums[j]);
        }
    }
    quickSelect(nums, l, j);
    quickSelect(nums, j + 1, r);
}
```

优点：性能好，交换次数少
缺点：边界处理复杂



### 代码实现：前后指针法

```
int lomutoPartition(vector<int>& nums, int left, int right) {
    int pivot = nums[right];
    int i = left - 1; // i 指向下一个要交换的位置，i+1表示大于pivot的点
    for (int j = left; j < right; ++j) {
        if (nums[j] <= pivot) {
            ++i;
            swap(nums[i], nums[j]);
        }
    }
    swap(nums[i + 1], nums[right]);
    return i + 1;
}

void quickSortLomuto(vector<int>& nums, int left, int right) {
    if (left >= right) return; // 递归函数一定要确定终止条件
    int pivotIndex = lomutoPartition(nums, left, right);
    quickSortLomuto(nums, left, pivotIndex - 1);
    quickSortLomuto(nums, pivotIndex + 1, right);
}
```

优点：代码简洁易于理解
缺点：交换多，重复值多或者序列有序时的效果差


### 前后指针法优化
1. 如果元素本身已经有序了，pivot选择不合适则可能退化到O(n²)，例如：元素原本降序，但是需要调整为升序，则时间复杂度O(n²).
优化办法：随机化 pivot、三数取中

2. 对重复率较高的情况优化
三路划分

> 三路划分思想是将：等于基准值的再放到中间，避免再次排序

3. 小数组优化

当子数组大小小于某阈值时，改用简单排序算法（插入排序等）

优势：减少递归调用的开销，不利于流水线预测，不利于cache命中
