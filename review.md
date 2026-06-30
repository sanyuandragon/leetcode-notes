# 复盘

这个文件记录需要回看的题、薄弱知识点和复习节奏。目标不是把题目越记越多，而是把容易忘的地方反复压实。

## 今日新增

| 日期 | 题号 | 标题 | 主知识点 | 下次复盘 |
| --- | --- | --- | --- | --- |
| 2026-06-30 | 200 | [岛屿数量](problems/0200-number-of-islands.md) | 图论 | 2026-07-01 |
| 2026-06-30 | 33 | [搜索旋转排序数组](problems/0033-search-in-rotated-sorted-array.md) | 二分查找 | 2026-07-01 |
| 2026-06-30 | 102 | [二叉树的层序遍历](problems/0102-binary-tree-level-order-traversal.md) | 二叉树 | 2026-07-01 |
| 2026-06-26 | 5 | [最长回文子串](problems/0005-longest-palindromic-substring.md) | 字符串 | 2026-06-27 |
| 2026-06-12 | 1 | [两数之和](problems/0001-two-sum.md) | 哈希表 | 2026-06-13 |
| 2026-06-12 | 15 | [三数之和](problems/0015-three-sum.md) | 双指针 | 2026-06-13 |
| 2026-06-12 | 21 | [合并两个有序链表](problems/0021-merge-two-sorted-lists.md) | 链表 | 2026-06-13 |
| 2026-06-12 | 53 | [最大子数组和](problems/0053-maximum-subarray.md) | 动态规划 | 2026-06-13 |
| - | - | - | - | - |

## 待复盘题目

| 优先级 | 题号 | 标题 | 原因 | 复盘动作 |
| --- | --- | --- | --- | --- |
| P1 | 200 | [岛屿数量](problems/0200-number-of-islands.md) | 网格 BFS 的起点标记、入队时标记和四方向边界容易漏 | 默写 `dx/dy`、新岛 `ans++`、BFS 中入队即沉岛 |
| P1 | 33 | [搜索旋转排序数组](problems/0033-search-in-rotated-sorted-array.md) | 有序半边判断和 target 区间边界容易混 | 默写“先判断哪半边有序，再判断 target 是否在有序半边” |
| P1 | 102 | [二叉树的层序遍历](problems/0102-binary-tree-level-order-traversal.md) | `size = q.size()` 分层含义容易被写成动态队列长度 | 默写 BFS 模板，解释为什么每层开始要先固定 `size` |
| P1 | 5 | [最长回文子串](problems/0005-longest-palindromic-substring.md) | 奇偶中心、扩展结束后的长度公式和起点容易混 | 默写 `expand(center, center)`、`expand(center, center + 1)` 和 `right - left - 1` |
| P2 | 1 | [两数之和](problems/0001-two-sum.md) | 哈希表先查后插、`insert` 不覆盖旧下标需要记清楚 | 只看题名，复述 `need = target - nums[i]` 和先查后插 |
| P1 | 15 | [三数之和](problems/0015-three-sum.md) | 分层去重和指针移动顺序容易混 | 只看题名，复述排序、固定一个数、左右指针去重三步 |
| P1 | 21 | [合并两个有序链表](problems/0021-merge-two-sorted-lists.md) | 虚拟头节点、尾指针和剩余链表连接容易漏一步 | 默写 `dummy/tail` 合并模板，重点检查 `tail = tail->next` |
| P1 | 53 | [最大子数组和](problems/0053-maximum-subarray.md) | `sum` 的状态含义和全负数初始化容易混 | 复述 `sum` 是以当前位置结尾的最大和，并检查 `maxAns = nums[0]` |
| - | - | - | - | - |

优先级建议：

- `P0`：完全忘记思路，或独立写不出来
- `P1`：大方向会，但边界、初始化、循环条件容易错
- `P2`：能写出来，但面试口述不顺

## 薄弱点

| 知识点 | 具体问题 | 对应题目 | 修复方式 |
| --- | --- | --- | --- |
| 图论 | 网格连通块计数要把格子看成点，四方向相邻看成边；遇到新陆地后 BFS 清理整块 | [200. 岛屿数量](problems/0200-number-of-islands.md) | 默写 `ans++ -> push -> 标记 -> BFS 四方向扩展` |
| 二分查找 | 旋转数组整体不单调，但每轮至少一半有序；先定位有序半边再缩小区间 | [33. 搜索旋转排序数组](problems/0033-search-in-rotated-sorted-array.md) | 默写 `nums[left] <= nums[mid]` 和左右区间判断条件 |
| 二叉树 | 层序遍历按层输出时，要用队列 BFS，并在每层开始固定当前层节点数 | [102. 二叉树的层序遍历](problems/0102-binary-tree-level-order-traversal.md) | 默写 `while (!q.empty()) -> size -> for size 次 -> 入队孩子` |
| 字符串 | 回文子串要同时处理奇数中心和偶数中心，扩展停止后边界已经多走一步 | [5. 最长回文子串](problems/0005-longest-palindromic-substring.md) | 默写中心扩展模板，并解释 `start = left + 1` |
| 哈希表 | 用值到下标的映射把查找补数降到均摊 O(1)，并保持先查后插 | [1. 两数之和](problems/0001-two-sum.md) | 默写 `need`、`find`、`return {mp[need], i}`、插入当前数 |
| 双指针 | 排序后固定前缀，再在剩余区间对撞；去重需要分层处理 | [15. 三数之和](problems/0015-three-sum.md) | 默写命中答案后的 `l++ / r--` 与两段去重循环 |
| 链表 | 结果头节点可能变化时，用虚拟头节点统一处理；尾插后尾指针必须前进 | [21. 合并两个有序链表](problems/0021-merge-two-sorted-lists.md) | 默写 `dummy -> tail -> 接节点 -> 移动原链表 -> tail 前进 -> 接剩余` |
| 动态规划 | 最大子数组和要定义“以当前位置结尾”的状态，全局答案单独维护 | [53. 最大子数组和](problems/0053-maximum-subarray.md) | 默写 `sum = max(sum + x, x)`，并用全负数例子检查初始化 |
| - | - | - | - |

## 复习节奏

建议按 `1 天 -> 3 天 -> 7 天 -> 14 天` 复盘：

- 第 1 次：只看题名和标签，尝试复述思路
- 第 2 次：手写关键代码框架
- 第 3 次：完整独立写一遍
- 第 4 次：用面试讲法讲清楚，并总结模板

## 已掌握

| 题号 | 标题 | 掌握证据 |
| --- | --- | --- |
| - | - | - |
