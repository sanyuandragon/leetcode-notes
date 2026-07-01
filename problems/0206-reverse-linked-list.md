# 206. 反转链表

## 题目信息

- 难度：简单
- 链接：https://leetcode.cn/problems/reverse-linked-list/
- 主知识点：链表
- 相关知识点：双指针、三指针、递归
- 复习状态：`new`

## 一眼识别

看到这些信号时，应该想到本题方法：

- 题目要求改变链表节点的 `next` 指向
- 返回反转后的新头节点
- 不需要创建新节点，可以原地反转
- 链表指针改变前必须先保存后继节点

## 解题思路

### 暴力思路

可以把链表节点值放进数组，再倒序重建一个新链表。但这样没有原地利用链表节点，也需要 O(n) 额外空间。

### 优化关键

原地反转链表时，需要维护三个指针：

- `prev`：已经反转好的链表头部
- `curr`：当前正在处理的节点
- `nex`：当前节点原本的下一个节点，防止改指针后丢失后续链表

每次把 `curr->next` 指向 `prev`，就完成了当前节点的反转。然后整体向后移动：

```cpp
prev = curr;
curr = nex;
```

当 `curr == nullptr` 时，说明原链表处理完了，此时 `prev` 就是反转后的新头节点。

### 最终做法

1. 初始化 `prev = nullptr`
2. 初始化 `curr = head`
3. 当 `curr` 不为空时循环
4. 保存 `nex = curr->next`
5. 反转当前指针：`curr->next = prev`
6. 移动 `prev = curr`
7. 移动 `curr = nex`
8. 返回 `prev`

## C++ 代码讲解

```cpp
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        ListNode* prev = nullptr;
        ListNode* curr = head;

        while (curr) {
            ListNode* nex = curr->next;
            curr->next = prev;
            prev = curr;
            curr = nex;
        }

        return prev;
    }
};
```

关键点：

- `prev`：当前已经反转好的部分的头节点
- `curr`：当前要反转的节点
- `nex`：保存原链表中 `curr` 后面的节点
- `curr->next = prev`：真正执行反转的一步
- `prev = curr`：把当前节点纳入已反转部分
- `curr = nex`：继续处理原链表中的下一个节点
- `return prev`：循环结束后，`prev` 指向反转后的新头

循环不变量 / 状态含义：

- 每轮循环开始时，`prev` 指向已经反转完成的链表
- `curr` 指向还未反转部分的第一个节点
- `nex` 用来临时保存未处理部分，避免断链
- 循环结束时，未处理部分为空，`prev` 就是答案

边界处理：

- 空链表：`head == nullptr`，循环不进入，返回 `nullptr`
- 单节点链表：反转后仍是自己，循环一次后返回该节点
- 必须先保存 `nex`，再修改 `curr->next`
- 返回的是 `prev`，不是 `curr`；结束时 `curr` 已经是 `nullptr`

## 复杂度分析

- 时间复杂度：O(n)，每个节点处理一次
- 空间复杂度：O(1)，只使用常数个指针

## 面试讲法

可以这样讲：

我用迭代的三指针做原地反转。`prev` 表示已经反转好的部分，`curr` 表示当前正在处理的节点。因为马上要修改 `curr->next`，所以先用 `nex` 保存原来的下一个节点。然后把 `curr->next` 指向 `prev`，完成当前节点反转，再让 `prev` 和 `curr` 向后移动。直到 `curr` 为空，说明所有节点都处理完，此时 `prev` 就是新头节点。

可能追问：

- 为什么要保存 `nex`：否则改掉 `curr->next` 后，会丢失原链表后续节点
- 为什么返回 `prev`：循环结束时 `curr` 是空，`prev` 才是新链表头
- 能不能递归：可以，递归到尾节点作为新头，回溯时反转指针
- 是否创建新节点：不需要，直接原地改变指针

## 模板沉淀

- 是否沉淀模板：是
- 对应模板：[反转链表](../templates/cpp/linked-list.md)
- 可复用点：链表反转、区间反转、K 个一组反转都建立在保存后继并改 `next` 的基本操作上

## 易错点

- 忘记先保存 `nex`，导致链表后半段丢失
- 返回 `curr` 而不是 `prev`
- 指针移动顺序写错，造成死循环或断链
- 空链表和单节点链表也要自然处理
- 变量名 `nex` 能用，但模板里更常见写法是 `next`

## 复盘卡片

- 一句话记忆：反转链表 = 保存 next，当前指向 prev，prev/curr 一起后移
- 下次先想：改 `next` 前有没有保存后继
- 如果写错，优先检查：`nex` 保存位置、返回值、三步移动顺序

