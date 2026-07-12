# LeetCode 笔记

这个仓库用来把每道 LeetCode 题整理成“面试可讲、复习可捡、模板可复用”的 Markdown 笔记。

## 使用方式

每次整理新题时，尽量提供这些信息：

```text
题号 / 标题：
难度：
题目链接：
题目描述或核心约束：
我的 C++ 代码：
我卡住的地方 / 提交错误 / 想重点理解的点：
```

整理时默认产出：

- `problems/` 中的一篇题目笔记
- `topics/` 中对应知识点索引的更新
- `templates/cpp/` 中可复用 C++ 模板的补充
- `review.md` 中的复盘提醒
- 一份保留原写法、补充中文注释的 C++ 代码
- 如果你明确要求“不要修改代码”，则保留原代码不加注释，讲解写在代码块外

## 目录

- `problems/`：每道题一篇笔记，文件名格式为 `题号-英文短标题.md`，例如 `0001-two-sum.md`
- `topics/`：按知识点聚合题目、套路和易错点
- `templates/cpp/`：可直接复用的 C++ 模板
- `review.md`：复盘清单、薄弱点和复习节奏

## 题目索引

| 题号 | 标题 | 难度 | 主知识点 | 复习状态 |
| --- | --- | --- | --- | --- |
| 1 | [两数之和](problems/0001-two-sum.md) | 简单 | 哈希表 | `new` |
| 3 | [无重复字符的最长子串](problems/0003-longest-substring-without-repeating-characters.md) | 中等 | 滑动窗口 | `new` |
| 4 | [寻找两个正序数组的中位数](problems/0004-median-of-two-sorted-arrays.md) | 困难 | 二分查找 | `new` |
| 5 | [最长回文子串](problems/0005-longest-palindromic-substring.md) | 中等 | 字符串 | `new` |
| 15 | [三数之和](problems/0015-three-sum.md) | 中等 | 双指针 | `new` |
| 19 | [删除链表的倒数第 N 个结点](problems/0019-remove-nth-node-from-end-of-list.md) | 中等 | 链表 | `new` |
| 20 | [有效的括号](problems/0020-valid-parentheses.md) | 简单 | 栈 | `new` |
| 21 | [合并两个有序链表](problems/0021-merge-two-sorted-lists.md) | 简单 | 链表 | `new` |
| 23 | [合并 K 个升序链表](problems/0023-merge-k-sorted-lists.md) | 困难 | 链表 | `new` |
| 25 | [K 个一组翻转链表](problems/0025-reverse-nodes-in-k-group.md) | 困难 | 链表 | `new` |
| 33 | [搜索旋转排序数组](problems/0033-search-in-rotated-sorted-array.md) | 中等 | 二分查找 | `new` |
| 42 | [接雨水](problems/0042-trapping-rain-water.md) | 困难 | 双指针 | `new` |
| 46 | [全排列](problems/0046-permutations.md) | 中等 | 回溯 | `new` |
| 53 | [最大子数组和](problems/0053-maximum-subarray.md) | 中等 | 动态规划 | `new` |
| 56 | [合并区间](problems/0056-merge-intervals.md) | 中等 | 区间 | `new` |
| 72 | [编辑距离](problems/0072-edit-distance.md) | 中等 | 动态规划 | `new` |
| 92 | [反转链表 II](problems/0092-reverse-linked-list-ii.md) | 中等 | 链表 | `new` |
| 94 | [二叉树的中序遍历](problems/0094-binary-tree-inorder-traversal.md) | 简单 | 二叉树 | `new` |
| 102 | [二叉树的层序遍历](problems/0102-binary-tree-level-order-traversal.md) | 中等 | 二叉树 | `new` |
| 121 | [买卖股票的最佳时机](problems/0121-best-time-to-buy-and-sell-stock.md) | 简单 | 贪心 | `new` |
| 124 | [二叉树中的最大路径和](problems/0124-binary-tree-maximum-path-sum.md) | 困难 | 二叉树 | `new` |
| 141 | [环形链表](problems/0141-linked-list-cycle.md) | 简单 | 链表 | `new` |
| 142 | [环形链表 II](problems/0142-linked-list-cycle-ii.md) | 中等 | 链表 | `new` |
| 146 | [LRU 缓存](problems/0146-lru-cache.md) | 中等 | 设计 | `new` |
| 160 | [相交链表](problems/0160-intersection-of-two-linked-lists.md) | 简单 | 链表 | `new` |
| 199 | [二叉树的右视图](problems/0199-binary-tree-right-side-view.md) | 中等 | 二叉树 | `new` |
| 200 | [岛屿数量](problems/0200-number-of-islands.md) | 中等 | 图论 | `new` |
| 206 | [反转链表](problems/0206-reverse-linked-list.md) | 简单 | 链表 | `new` |
| 236 | [二叉树的最近公共祖先](problems/0236-lowest-common-ancestor-of-a-binary-tree.md) | 中等 | 二叉树 | `new` |
| 300 | [最长递增子序列](problems/0300-longest-increasing-subsequence.md) | 中等 | 动态规划 | `new` |
| 1143 | [最长公共子序列](problems/1143-longest-common-subsequence.md) | 中等 | 动态规划 | `new` |
| 1041 | [困于环中的机器人](problems/1041-robot-bounded-in-circle.md) | 中等 | 模拟 | `new` |
| - | - | - | - | - |

复习状态建议：

- `new`：刚整理，还没复盘
- `reviewing`：已经复盘过，但还不稳定
- `solid`：能独立讲清思路并写出代码
- `needs-work`：容易忘、容易错，需要重点复盘

## 知识点索引

| 知识点 | 文件 | 典型题 |
| --- | --- | --- |
| 哈希表 | [hash-table.md](topics/hash-table.md) | 两数之和、分组、计数 |
| 字符串 | [string.md](topics/string.md) | 回文、子串、前后缀、模式匹配 |
| 模拟 | [simulation.md](topics/simulation.md) | 方向移动、状态维护、周期判断 |
| 双指针 | [two-pointers.md](topics/two-pointers.md) | 有序数组、区间收缩、三数之和、接雨水、快慢指针 |
| 二分查找 | [binary-search.md](topics/binary-search.md) | 有序查找、两个有序数组中位数、旋转数组、答案二分、边界定位 |
| 滑动窗口 | [sliding-window.md](topics/sliding-window.md) | 连续子数组/子串、最长/最短窗口、去重 |
| 栈 | [stack.md](topics/stack.md) | 括号匹配、表达式、嵌套结构 |
| 单调栈 | [monotonic-stack.md](topics/monotonic-stack.md) | 下一个更大元素、贡献法 |
| 区间 | [intervals.md](topics/intervals.md) | 合并、插入、覆盖、扫描线 |
| 回溯 | [backtracking.md](topics/backtracking.md) | 排列、组合、子集、棋盘搜索 |
| 动态规划 | [dynamic-programming.md](topics/dynamic-programming.md) | 状态定义、转移、初始化、Kadane、编辑距离、最长公共子序列 |
| 贪心 | [greedy.md](topics/greedy.md) | 股票、区间、跳跃、前缀最优 |
| 设计 | [design.md](topics/design.md) | LRU、哈希表+链表、数据结构组合 |
| 并查集 | [union-find.md](topics/union-find.md) | 连通性、分组、合并查询 |
| 链表 | [linked-list.md](topics/linked-list.md) | 虚拟头节点、尾插、删除节点、合并链表、分治归并、反转、区间反转、K 组反转、快慢指针、判环、入环点、相交链表 |
| 二叉树 | [binary-tree.md](topics/binary-tree.md) | BFS、DFS、前中后序遍历、层序遍历、右视图、递归、树形 DP、最大路径和、最近公共祖先 |
| 图论 | [graph.md](topics/graph.md) | BFS、DFS、网格图、连通块 |

## 单题笔记结构

每篇题目笔记按这个顺序写：

1. 题目信息
2. 一眼识别
3. 解题思路
4. C++ 代码讲解：默认保留你的写法并补中文注释；若你要求不改代码，则原样保留并在代码块外讲解
5. 复杂度分析
6. 面试讲法
7. 模板沉淀
8. 易错点
9. 复盘卡片

新题可从 [problems/_template.md](problems/_template.md) 复制结构。
