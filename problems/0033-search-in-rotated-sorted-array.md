# 33. 搜索旋转排序数组

## 题目信息

- 难度：中等
- 链接：https://leetcode.cn/problems/search-in-rotated-sorted-array/
- 主知识点：二分查找
- 相关知识点：数组、旋转数组、有序区间判断
- 复习状态：`new`

## 一眼识别

看到这些信号时，应该想到本题方法：

- 原数组有序，但被旋转过
- 数组元素互不相同
- 要求 O(log n)，不能线性扫描
- 旋转后整体不再完全有序，但任意二分时至少有一半是有序的

## 解题思路

### 暴力思路

最简单是从左到右扫描，找到 `target` 就返回下标，否则返回 `-1`。但这样是 O(n)，不满足题目要求的 O(log n)。

### 优化关键

旋转数组虽然整体不是单调的，但在闭区间 `[left, right]` 中取 `mid` 后，一定至少有一边是有序的：

- 如果 `nums[left] <= nums[mid]`，说明左半边 `[left, mid]` 有序
- 否则右半边 `[mid, right]` 有序

知道哪一半有序后，再判断 `target` 是否落在这个有序区间里：

- 如果落在有序半边，就保留这半边
- 否则丢掉这半边，去另一半继续找

### 最终做法

1. 使用闭区间二分：`left = 0`，`right = n - 1`
2. 每轮计算 `mid`
3. 如果 `nums[mid] == target`，直接返回 `mid`
4. 判断左半边是否有序
5. 如果左半边有序，判断 `target` 是否在 `[nums[left], nums[mid]]` 中
6. 如果左半边无序，则右半边一定有序，判断 `target` 是否在 `(nums[mid], nums[right]]` 中
7. 根据判断结果缩小区间
8. 找不到返回 `-1`

## C++ 代码讲解

```cpp
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int left = 0;
        int right = nums.size() - 1;

        while (left <= right) {
            int mid = left + (right - left) / 2;

            if (nums[mid] == target) {
                return mid;
            }
            if (nums[left] <= nums[mid]) {
                if (nums[left] <= target && target <= nums[mid]) {
                    right = mid - 1;
                } else {
                    left = mid + 1;
                }
            } else {
                if (nums[mid] < target && target <= nums[right]) {
                    left = mid + 1;
                } else {
                    right = mid - 1;
                }
            }
        }

        return -1;
    }
};
```

关键点：

- `left`、`right`：当前搜索闭区间
- `mid`：当前检查位置，写成 `left + (right - left) / 2` 避免溢出
- `nums[left] <= nums[mid]`：判断左半边是否有序
- `nums[left] <= target && target <= nums[mid]`：判断目标是否在左侧有序区间
- `nums[mid] < target && target <= nums[right]`：判断目标是否在右侧有序区间
- 每次都通过 `left = mid + 1` 或 `right = mid - 1` 丢掉一半区间

循环不变量 / 状态含义：

- 如果 `target` 存在，它一定在当前闭区间 `[left, right]` 中
- 每次循环都会判断出至少一半区间是有序的
- 根据 `target` 是否落在有序区间里，可以安全丢弃另一半
- 每次更新后搜索区间都会严格缩小

边界处理：

- 数组长度至少为 `1`，所以 `right = nums.size() - 1` 是安全的
- 单元素数组时，`left == right == mid`，命中则返回，否则区间会缩小为空
- 因为元素互不相同，`nums[left] <= nums[mid]` 可以稳定判断左半边有序
- 在右侧有序区间判断中使用 `nums[mid] < target`，因为 `nums[mid] == target` 已经提前处理过

## 复杂度分析

- 时间复杂度：O(log n)，每次丢掉一半搜索区间
- 空间复杂度：O(1)，只使用常数个变量

## 面试讲法

可以这样讲：

旋转排序数组整体不再有序，但每次二分后，左右两半至少有一半是有序的。我先比较 `nums[left]` 和 `nums[mid]` 判断左半边是否有序。如果左半边有序，就看 `target` 是否落在 `nums[left]` 到 `nums[mid]` 之间；落在里面就搜索左半边，否则搜索右半边。如果左半边无序，则右半边一定有序，用同样方式判断 `target` 是否落在右半边。每次都能排除一半区间，所以是 O(log n)。

可能追问：

- 为什么至少一半有序：旋转数组只在一个位置断开，二分切开后不可能两边都跨过断点
- 为什么能用 `nums[left] <= nums[mid]`：题目保证元素互不相同，且闭区间中左半边若有序就满足这个关系
- 如果有重复元素怎么办：判断有序半边会变得不稳定，可能需要跳过重复边界，最坏会退化到 O(n)
- 能不能先找旋转点：可以，先找最小值位置，再在某一段普通二分，但实现分两步

## 模板沉淀

- 是否沉淀模板：是
- 对应模板：[旋转排序数组二分](../templates/cpp/binary-search.md)
- 可复用点：当数组被旋转但元素互不相同时，每轮判断哪半边有序，再决定保留哪半边

## 易错点

- 不要只用普通二分判断 `nums[mid] < target`，旋转后整体不单调
- 必须先判断 `nums[mid] == target`，再做区间归属判断
- 左半边有序条件是 `nums[left] <= nums[mid]`
- 判断目标是否在左半边时用闭区间 `[nums[left], nums[mid]]`
- 更新边界时要排除 `mid`，否则可能死循环
- 有重复元素时这套判断不完全适用，本题明确“值互不相同”

## 复盘卡片

- 一句话记忆：旋转数组二分 = 每轮先找有序半边，再看 target 是否在这半边
- 下次先想：`nums[left] <= nums[mid]` 表示左半边有序
- 如果写错，优先检查：左右有序判断、target 区间边界、`mid` 是否被正确排除

