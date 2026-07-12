# 二分查找

## 核心识别

什么时候想到二分：

- 数据本身有序
- 答案具有单调性，可以判断“当前答案是否可行”
- 需要找第一个满足条件的位置、最后一个满足条件的位置或最小可行答案

## 常用套路

- 闭区间：`while (left <= right)`，循环内决定丢弃哪一半
- 左边界：找第一个 `>= target` 或第一个满足条件的位置
- 答案二分：在答案范围上二分，用 `check(mid)` 判断可行性
- `lower_bound` 优化状态表：比如 LIS 中维护每个长度的最小结尾值

## 题目列表

| 题号 | 标题 | 难度 | 关键点 | 状态 |
| --- | --- | --- | --- | --- |
| 4 | [寻找两个正序数组的中位数](../problems/0004-median-of-two-sorted-arrays.md) | 困难 | 在短数组上二分切分位置，用四个边界判断左右两半是否合法 | `new` |
| 33 | [搜索旋转排序数组](../problems/0033-search-in-rotated-sorted-array.md) | 中等 | 每轮判断哪一半有序，再判断目标是否落在有序区间 | `new` |
| 300 | [最长递增子序列](../problems/0300-longest-increasing-subsequence.md) | 中等 | 用 `tails` 维护每个长度的最小结尾值，二分替换第一个 `>= x` 的位置 | `new` |
| - | - | - | - | - |

## 模板

- 二分边界：见 [binary-search.md](../templates/cpp/binary-search.md)
- 两个有序数组中位数：见 [binary-search.md](../templates/cpp/binary-search.md)
- 旋转排序数组：每轮至少一半有序，先判断有序半边再缩小区间
- LIS 的 tails 二分优化：见 [binary-search.md](../templates/cpp/binary-search.md)

## 易错点

- `mid` 用 `left + (right - left) / 2` 避免溢出
- 明确返回的是位置、布尔值，还是答案本身
- 每次循环都必须缩小区间，否则会死循环
- 两个有序数组中位数要在短数组上二分切分位置，避免另一个切分点越界
- 答案二分的 `check` 必须满足单调性
- 旋转数组不能直接按整体单调性比较 `nums[mid]` 和 `target`
- 严格递增 LIS 用 `lower_bound`，不下降子序列通常用 `upper_bound`

## 面试表达

二分的关键不是“数组有序”四个字，而是把问题转成一个单调判断：某个位置或答案左边不满足、右边满足，或者反过来。
