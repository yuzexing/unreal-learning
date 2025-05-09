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
