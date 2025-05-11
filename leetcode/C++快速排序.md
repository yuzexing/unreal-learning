# C++快速排序

算法思想：分治法
时间复杂度：O(nlogn)
最坏时间复杂度：O(n²)
空间复杂度：O(n)
稳定性：不稳定


### 代码实现

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

### 优化
1. 如果元素本身已经有序了，则可能退化到O(n²)，例如：元素原本降序，但是需要调整为升序，则时间复杂度O(n²).
优化办法：随机化 pivot、三数取中

2. 对重复率较高的
三路划分
