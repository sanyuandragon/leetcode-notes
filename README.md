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

## 目录

- `problems/`：每道题一篇笔记，文件名格式为 `题号-英文短标题.md`，例如 `0001-two-sum.md`
- `topics/`：按知识点聚合题目、套路和易错点
- `templates/cpp/`：可直接复用的 C++ 模板
- `review.md`：复盘清单、薄弱点和复习节奏

## 题目索引

| 题号 | 标题 | 难度 | 主知识点 | 复习状态 |
| --- | --- | --- | --- | --- |
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
| 双指针 | [two-pointers.md](topics/two-pointers.md) | 有序数组、区间收缩、快慢指针 |
| 二分查找 | [binary-search.md](topics/binary-search.md) | 有序查找、答案二分、边界定位 |
| 滑动窗口 | [sliding-window.md](topics/sliding-window.md) | 连续子数组、最长/最短窗口 |
| 单调栈 | [monotonic-stack.md](topics/monotonic-stack.md) | 下一个更大元素、贡献法 |
| 动态规划 | [dynamic-programming.md](topics/dynamic-programming.md) | 状态定义、转移、初始化 |
| 并查集 | [union-find.md](topics/union-find.md) | 连通性、分组、合并查询 |

## 单题笔记结构

每篇题目笔记按这个顺序写：

1. 题目信息
2. 一眼识别
3. 解题思路
4. C++ 代码讲解
5. 复杂度分析
6. 面试讲法
7. 模板沉淀
8. 易错点
9. 复盘卡片

新题可从 [problems/_template.md](problems/_template.md) 复制结构。

