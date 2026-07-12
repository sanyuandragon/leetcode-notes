# 4. 寻找两个正序数组的中位数

## 题目信息

- 难度：困难
- 链接：https://leetcode.cn/problems/median-of-two-sorted-arrays/
- 主知识点：二分查找
- 相关知识点：数组、划分、边界哨兵
- 复习状态：`new`

## 一眼识别

看到这些信号时，应该想到本题方法：

- 两个数组都已经正序排列
- 要求找合并后的中位数，但不能真的合并
- 题目要求 O(log(m+n))
- 中位数可以理解成把总数组切成左右两半
- 需要在较短数组上二分切分位置

## 解题思路

### 暴力思路

最直观的做法是把两个有序数组合并成一个有序数组，再根据总长度奇偶取中位数。这样时间复杂度是 O(m+n)，空间复杂度也是 O(m+n)，没有利用题目要求的 O(log(m+n))。

也可以用双指针只走到中位数位置，空间降到 O(1)，但时间仍然是 O(m+n)。

### 优化关键

中位数的本质是把合并后的有序数组分成左右两部分：

- 左半部分元素个数等于右半部分，或者比右半部分多一个
- 左半部分的最大值小于等于右半部分的最小值

假设在 `nums1` 中切出 `cut1` 个元素放到左边，那么 `nums2` 中应该切出：

```cpp
cut2 = (m + n + 1) / 2 - cut1;
```

这样左半部分总元素个数固定。接下来只需要检查切分是否合法：

```cpp
left1 <= right2 && left2 <= right1
```

其中：

- `left1`：`nums1` 左半部分最大值
- `right1`：`nums1` 右半部分最小值
- `left2`：`nums2` 左半部分最大值
- `right2`：`nums2` 右半部分最小值

如果 `left1 > right2`，说明 `nums1` 左边切太多了，要让 `cut1` 变小；否则说明 `nums1` 左边切太少了，要让 `cut1` 变大。

为了保证 `cut2` 不越界，始终在较短数组上二分。

### 最终做法

1. 如果 `nums1` 更长，就交换两个数组，保证 `nums1` 是较短数组
2. 在 `nums1` 的切分位置 `[0, m]` 上二分
3. 根据 `cut1` 计算 `cut2`
4. 用 `INT_MIN` 和 `INT_MAX` 处理切在数组边界的情况
5. 如果满足 `left1 <= right2 && left2 <= right1`，说明切分合法
6. 总长度为奇数时，返回左半部分最大值
7. 总长度为偶数时，返回左半部分最大值和右半部分最小值的平均数
8. 如果 `left1 > right2`，向左调整 `cut1`；否则向右调整 `cut1`

## C++ 代码讲解

```cpp
class Solution {
public:
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
        // 保证 nums1 是较短数组，这样 cut2 一定更容易保持在合法范围内
        if (nums1.size() > nums2.size()) {
            return findMedianSortedArrays(nums2, nums1);
        }

        int m = nums1.size();
        int n = nums2.size();

        int left = 0;
        int right = m;
        while(left <= right) {
            int cut1 = left + (right - left) / 2;
            int cut2 = (m + n + 1) / 2 - cut1;
            int left1, right1, left2, right2;

            // cut1 == 0 表示 nums1 左边没有元素，用负无穷当哨兵
            if (cut1 == 0) {
                left1 = INT_MIN;
            }else {
                left1 = nums1[cut1 - 1];
            }
            // cut1 == m 表示 nums1 右边没有元素，用正无穷当哨兵
            if (cut1 == m) {
                right1 = INT_MAX;
            }else {
                right1 = nums1[cut1];
            }

            if (cut2 == 0) {
                left2 = INT_MIN;
            }else {
                left2 = nums2[cut2 - 1];
            }

            if (cut2 == n) {
                right2 = INT_MAX;
            }else {
                right2 = nums2[cut2];
            }

            // 合法切分：两个左半部分的最大值都不超过另一边右半部分的最小值
            if (left1 <= right2 && left2 <= right1) {
                if ((m + n) % 2 == 1) {
                    return max(left1, left2);
                }
                int maxLeft = max(left1, left2);
                int minRight = min(right1, right2);
                return (static_cast<double>(maxLeft) + minRight) / 2.0;
            }

            // nums1 左边元素太多，cut1 需要左移
            if (left1 > right2) {
                right = cut1 - 1;
            }else {
                left = cut1 + 1;
            }
        }

        return 0.0;
    }
};
```

关键点：

- `nums1.size() > nums2.size()`：递归交换，保证只在短数组上二分
- `cut1`：`nums1` 中放进左半部分的元素个数
- `cut2`：`nums2` 中放进左半部分的元素个数
- `(m + n + 1) / 2`：让总长度为奇数时，左半部分比右半部分多一个
- `INT_MIN` / `INT_MAX`：处理切分在数组最左或最右时没有元素的情况
- `left1 <= right2 && left2 <= right1`：判断左右两半是否整体有序
- `left1 > right2`：说明 `cut1` 太大，需要减少 `nums1` 左半部分元素

循环不变量 / 状态含义：

- `left` 和 `right` 表示 `cut1` 的可搜索范围
- 每一轮都固定左半部分总元素个数为 `(m + n + 1) / 2`
- 如果当前切分不合法，可以根据交叉比较结果排除一半 `cut1`
- 合法切分出现时，中位数只和左半最大值、右半最小值有关

边界处理：

- 其中一个数组可以为空，所以切分边界必须用哨兵值处理
- 总长度至少为 `1`，不会出现两个数组都为空
- 必须先保证 `nums1` 更短，否则 `cut2` 可能越界
- 偶数长度取平均时要先转成 `double`，避免整数除法
- 题目数据范围在 `[-10^6, 10^6]`，所以用 `INT_MIN` / `INT_MAX` 作为哨兵不会和真实数据冲突

## 复杂度分析

- 时间复杂度：O(log(min(m, n)))，只在较短数组的切分位置上二分
- 空间复杂度：O(1)，只使用常数个变量；递归交换只发生一次，可以视为 O(1)

## 面试讲法

可以这样讲：

我不直接合并数组，而是把问题转成找一个合法切分。合并后的中位数要求左半部分和右半部分数量平衡，并且左边所有元素都小于等于右边所有元素。我在较短数组上二分 `cut1`，再用总左半长度反推出 `cut2`。每次取出 `left1、right1、left2、right2` 四个边界值，如果 `left1 <= right2` 且 `left2 <= right1`，说明切分合法。总长度为奇数时返回左半最大值，偶数时返回左半最大值和右半最小值的平均数。如果 `left1 > right2`，说明 `nums1` 左边取多了，二分左移；否则右移。

可能追问：

- 为什么在短数组上二分：保证 `cut2` 更容易保持合法，同时复杂度是 O(log(min(m,n)))
- 为什么用 `(m+n+1)/2`：奇数长度时让左半部分多一个，中位数就是左半最大值
- 为什么只检查两个交叉条件：两个数组内部已经有序，只要跨数组边界不乱，整体切分就合法
- 一个数组为空怎么办：哨兵值 `INT_MIN` 和 `INT_MAX` 可以统一处理边界

## 模板沉淀

- 是否沉淀模板：是
- 对应模板：[两个有序数组中位数](../templates/cpp/binary-search.md)
- 可复用点：在短数组上二分切分位置，用交叉边界判断左右两半是否合法

## 易错点

- 忘记先让 `nums1` 成为较短数组，导致 `cut2` 可能越界
- `cut2` 公式写错，左半部分元素个数没有固定住
- 奇数长度应该返回 `max(left1, left2)`，不是右半最小值
- 偶数长度取平均时要避免整数除法
- `cut1 == 0`、`cut1 == m`、`cut2 == 0`、`cut2 == n` 四个边界都要处理
- `left1 > right2` 时应该让 `cut1` 左移，方向别写反

## 复盘卡片

- 一句话记忆：两个有序数组中位数 = 在短数组上二分切分，让左半最大值 <= 右半最小值
- 下次先想：`cut1 + cut2 = (m+n+1)/2`
- 如果写错，优先检查：是否短数组二分、四个边界哨兵、二分调整方向、奇偶返回值
