# 92. 反转链表 II

## 题目信息

- 题号：92
- 标题：反转链表 II
- 难度：中等
- 标签：链表、指针、局部反转、虚拟头节点
- 链接：https://leetcode.cn/problems/reverse-linked-list-ii/

## 一眼识别

看到“只反转链表中第 `left` 到第 `right` 个节点”，优先想到：

- 链表局部修改通常先用 `dummyHead` 统一处理头节点被修改的情况。
- 先找到反转区间前一个节点 `preLeft`。
- 对区间内固定 `right - left + 1` 个节点做普通链表反转。
- 反转后把区间新头、新尾分别接回左右两侧。

## 解题思路

暴力想法是把链表值取出来，在数组里反转区间，再写回链表。但这样没有真正练到链表指针操作，也需要额外空间。

更好的做法是“一次定位 + 局部反转”：

1. 建立虚拟头节点 `dummyHead`，让 `left = 1` 时也有统一的区间前驱。
2. 让 `preLeft` 从虚拟头走到第 `left` 个节点的前一个位置。
3. 从 `preLeft->next` 开始反转 `right - left + 1` 个节点。
4. 反转结束后：
   - `pre` 指向反转区间的新头。
   - `cur` 指向反转区间后面的第一个节点。
   - 原来的 `preLeft->next` 是反转区间旧头，反转后变成区间尾。
5. 先让旧头的 `next` 指向 `cur`，再让 `preLeft->next` 指向 `pre`。

关键图像：`preLeft -> [left ... right] -> cur`，中间反转后要重新接成 `preLeft -> right ... left -> cur`。

## C++ 代码讲解

带中文注释的代码：

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* reverseBetween(ListNode* head, int left, int right) {
        // 虚拟头节点：统一处理 left == 1 时头节点会变化的情况
        ListNode* dummyHead = new ListNode(0);
        dummyHead->next = head;

        // preLeft 指向反转区间的前一个节点
        ListNode* preLeft = dummyHead;
        for (int i = 1; i < left; ++i) {
            preLeft = preLeft->next;
        }

        // 从 left 位置开始，对固定长度的区间做普通链表反转
        ListNode* pre = nullptr;
        ListNode* cur = preLeft->next;
        ListNode* nxt = nullptr;

        // 反转 right - left + 1 个节点
        for (int i = 0; i < right - left + 1; ++i) {
            nxt = cur->next;   // 先保存后继，避免断链后找不到剩余链表
            cur->next = pre;   // 当前节点指向前一个节点，完成局部反转的一步
            pre = cur;         // pre 前进到当前节点
            cur = nxt;         // cur 前进到下一个待反转节点
        }

        // 此时 pre 是反转区间的新头，cur 是反转区间右侧的第一个节点
        // preLeft->next 仍然是原区间头，反转后它变成区间尾
        preLeft->next->next = cur; // 区间尾接回右侧链表
        preLeft->next = pre;       // 区间前驱接上反转后的新头

        return dummyHead->next;
    }
};
```

关键变量：

- `dummyHead`：虚拟头节点，避免单独处理 `left == 1`。
- `preLeft`：反转区间前驱，最后负责接上反转后的新头。
- `pre`：局部反转过程中，已经反转好的部分的头。
- `cur`：当前正在处理的节点；反转结束后指向区间右侧第一个节点。
- `nxt`：临时保存 `cur->next`，防止断链。

循环不变量：

- 每一轮开始时，`pre` 指向已经反转好的局部链表头。
- `cur` 指向下一个需要被反转的节点。
- 还没处理的剩余链表从 `cur` 开始。

边界处理：

- `left == right` 时，循环只反转 1 个节点，链表结构保持不变。
- `left == 1` 时，`dummyHead` 让 `preLeft` 可以稳定指向虚拟头。
- 题目保证 `1 <= left <= right <= n`，因此 `cur` 在反转循环中一定非空。

## 复杂度分析

- 时间复杂度：`O(n)`，最多遍历到区间前驱并反转一段链表。
- 空间复杂度：`O(1)`，只使用常数个指针。

## 面试讲法

这题我会用虚拟头节点统一处理头节点可能变化的情况。先让 `preLeft` 走到反转区间前一个节点，然后从 `preLeft->next` 开始做普通链表反转，反转的长度是 `right - left + 1`。反转结束后，`pre` 是区间新头，`cur` 是区间右侧第一个节点，而原来的区间头已经变成区间尾，所以先把这个旧头接到 `cur`，再把 `preLeft` 接到 `pre`。整个过程只改指针，时间 `O(n)`，额外空间 `O(1)`。

## 模板沉淀

可以沉淀为“链表区间反转”模板，核心四步：

1. 虚拟头节点。
2. 找到区间前驱 `preLeft`。
3. 反转固定长度 `right - left + 1`。
4. 旧区间头接右侧，新区间头接左侧。

已更新到：[链表模板](../templates/cpp/linked-list.md)。

## 易错点

- 不保存 `nxt = cur->next` 就改 `cur->next`，会导致后续链表丢失。
- `preLeft->next` 在重连前仍指向原区间头，原区间头反转后会变成区间尾。
- 重连顺序要清楚：先让区间尾接 `cur`，再让 `preLeft` 接 `pre`。
- 如果不用虚拟头节点，`left == 1` 时需要额外更新新头，容易漏。
- LeetCode 里 `new ListNode(0)` 可以通过；工程代码更推荐用栈对象 `ListNode dummy(0)`，避免手动释放问题。

## 复盘卡片

- 一句话记忆：区间反转 = 找前驱，反转固定长度，旧头变尾接右边，前驱接新头。
- 下次重点看：`preLeft->next` 在反转前后身份变化：原区间头 -> 反转后区间尾。
- 默写关键句：

```cpp
preLeft->next->next = cur;
preLeft->next = pre;
```
