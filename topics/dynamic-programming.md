# 动态规划

## 核心识别

什么时候想到动态规划：

- 问题可以拆成重叠子问题
- 当前答案依赖更小规模或更早状态
- 需要求最值、方案数、可行性或序列匹配

## 常用套路

- 明确 `dp[i]` 或 `dp[i][j]` 的含义
- 写出状态转移
- 确定初始化和遍历顺序
- 必要时做空间优化
- 子序列最值：常定义 `dp[i]` 为以 `i` 结尾或以 `i` 开头的最优值
- 双字符串匹配：常定义 `dp[i][j]` 表示两个前缀之间的最优值

## 题目列表

| 题号 | 标题 | 难度 | 关键点 | 状态 |
| --- | --- | --- | --- | --- |
| 53 | [最大子数组和](../problems/0053-maximum-subarray.md) | 中等 | 定义以当前位置结尾的最大和，选择接上前面或重新开始 | `new` |
| 72 | [编辑距离](../problems/0072-edit-distance.md) | 中等 | 定义 `dp[i][j]` 为两个前缀的最小编辑距离，考虑插入、删除、替换 | `new` |
| 300 | [最长递增子序列](../problems/0300-longest-increasing-subsequence.md) | 中等 | 定义 `dp[i]` 为以 `nums[i]` 结尾的 LIS 长度，也可用 `tails` 二分优化 | `new` |
| 1143 | [最长公共子序列](../problems/1143-longest-common-subsequence.md) | 中等 | 定义 `dp[i][j]` 为两个前缀的 LCS 长度，相等左上加一，不等取上/左最大 | `new` |
| - | - | - | - | - |

## 模板

- 线性 DP：见 [dynamic-programming.md](../templates/cpp/dynamic-programming.md)
- Kadane 算法：最大子数组和的 O(1) 空间线性 DP
- 编辑距离二维 DP：见 [dynamic-programming.md](../templates/cpp/dynamic-programming.md)
- 最长递增子序列 DP：见 [dynamic-programming.md](../templates/cpp/dynamic-programming.md)
- 最长公共子序列二维 DP：见 [dynamic-programming.md](../templates/cpp/dynamic-programming.md)

## 易错点

- 状态定义不清会导致转移式混乱
- 初始化要覆盖最小规模输入
- 遍历顺序必须保证依赖状态已经计算过
- 空间优化时注意覆盖顺序
- 最大子数组和这类题不能把答案初始化为 `0`，全负数时会出错
- 子序列 DP 通常不要求连续，不要和子数组混淆
- 双字符串 DP 里，`i/j` 常表示前缀长度，访问字符时要用 `i - 1/j - 1`
- 最长公共子序列不是最长公共子串，匹配字符不要求连续

## 面试表达

动态规划先定义状态，再解释状态之间怎么转移。面试里最重要的是讲清楚“这个状态代表什么”，而不是直接背公式。子序列问题尤其要说明状态如何保持原数组顺序。
