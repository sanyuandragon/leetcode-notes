# 链表

## 核心识别

什么时候想到链表套路：

- 题目操作的是 `ListNode*`
- 需要改变节点连接关系，而不是只读取值
- 结果链表的头节点可能变化
- 涉及合并、删除、反转、找中点、判断环等操作

## 常用套路

- 虚拟头节点：统一处理头节点可能变化的情况
- 尾指针：构建新链表或合并链表时，维护结果链表末尾
- 分治归并：多个有序链表两两合并，逐轮扩大合并范围
- 快慢指针：找中点、判断环、寻找倒数第 k 个节点
- 双指针切换链表：处理两个链表长度差，比如相交链表
- 三指针反转：`prev`、`cur`、`next` 依次改边
- 区间反转：先找到区间前驱，反转固定长度，再接回左右两侧
- K 组反转：每次确认剩余节点够 `k` 个，再做长度为 `k` 的区间反转

## 题目列表

| 题号 | 标题 | 难度 | 关键点 | 状态 |
| --- | --- | --- | --- | --- |
| 21 | [合并两个有序链表](../problems/0021-merge-two-sorted-lists.md) | 简单 | 虚拟头节点 + 尾指针，复用原节点合并两个有序链表 | `new` |
| 23 | [合并 K 个升序链表](../problems/0023-merge-k-sorted-lists.md) | 困难 | 复用合并两个链表模板，用 `step` 翻倍做分治归并 | `new` |
| 25 | [K 个一组翻转链表](../problems/0025-reverse-nodes-in-k-group.md) | 困难 | 每组确认够 `k` 个节点，再做固定长度局部反转并接回链表 | `new` |
| 92 | [反转链表 II](../problems/0092-reverse-linked-list-ii.md) | 中等 | 虚拟头节点定位区间前驱，局部反转后重新接回链表 | `new` |
| 141 | [环形链表](../problems/0141-linked-list-cycle.md) | 简单 | 快慢指针，快指针追上慢指针说明有环 | `new` |
| 160 | [相交链表](../problems/0160-intersection-of-two-linked-lists.md) | 简单 | 两个指针分别走 A+B 和 B+A，在交点或空指针处相遇 | `new` |
| 206 | [反转链表](../problems/0206-reverse-linked-list.md) | 简单 | 三指针原地反转，先保存后继再改 `next` | `new` |
| - | - | - | - | - |

## 模板

- 合并两个有序链表：见 [linked-list.md](../templates/cpp/linked-list.md)
- 合并 K 个升序链表：见 [linked-list.md](../templates/cpp/linked-list.md)
- 快慢指针判环：见 [linked-list.md](../templates/cpp/linked-list.md)
- 相交链表双指针：见 [linked-list.md](../templates/cpp/linked-list.md)
- 反转链表：见 [linked-list.md](../templates/cpp/linked-list.md)
- 区间反转链表：见 [linked-list.md](../templates/cpp/linked-list.md)
- K 个一组反转链表：见 [linked-list.md](../templates/cpp/linked-list.md)

## 易错点

- 虚拟头节点不是答案本身，返回时要返回 `dummy.next` 或 `prehead->next`
- 改 `next` 指针前，必要时先保存后继节点
- 尾插后尾指针必须前进
- 合并 K 个链表时，顺序合并会重复扫描长链表，优先考虑分治或小根堆
- 链表题要特别检查空链表、单节点链表和剩余链表
- 快慢指针访问 `fast->next->next` 前，必须先判断 `fast` 和 `fast->next` 非空
- 相交链表判断的是节点地址相同，不是节点值相同
- 反转链表时返回 `prev`，循环结束时 `curr` 已经为空
- 区间反转时，原区间头反转后会变成区间尾，要接回右侧链表
- K 组反转时，不足 `k` 个的尾部节点不能反转

## 面试表达

链表题的关键是明确每个指针的含义。只要头节点可能变化，就优先考虑虚拟头节点；只要在构建结果链表，就维护一个尾指针。
