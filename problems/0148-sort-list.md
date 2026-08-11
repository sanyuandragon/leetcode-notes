# 148. 排序链表

## 题目信息

- 难度：中等
- 链接：https://leetcode.cn/problems/sort-list/
- 主知识点：链表
- 相关知识点：归并排序、快慢指针、分治、合并有序链表
- 复习状态：`new`

## 一眼识别

看到这些信号时，应该想到本题方法：

- 输入是单链表 `ListNode* head`
- 要按升序排序整条链表
- 链表不能像数组一样随机访问，按下标排序不方便
- 归并排序天然适合链表：找中点拆成两半，再合并两个有序链表
- 需要复用“合并两个有序链表”的模板

## 解题思路

### 暴力思路

可以把链表节点值全部取出来放进数组，排序后再写回链表。这样实现简单，但需要 O(n) 额外空间，也没有体现链表节点重连的思路。

也可以每次扫描链表找最小节点，类似选择排序，但时间复杂度是 O(n^2)，节点数最多 `5 * 10^4`，会超时。

### 优化关键

链表排序最常用的是归并排序：

1. 用快慢指针找到链表中点
2. 从中点前断开，把链表拆成左右两半
3. 分别递归排序左右两半
4. 用合并两个有序链表的模板把两半合并

归并排序的优势是：

- 拆分链表只需要快慢指针
- 合并两个有序链表只需要改 `next` 指针
- 每层递归合并会访问所有节点一次，总层数是 O(log n)

### 最终做法

1. 如果链表为空或只有一个节点，直接返回
2. `middleNode` 用快慢指针找到后半段头节点，并把前半段尾部断开
3. 递归排序前半段 `head`
4. 递归排序后半段 `head2`
5. 使用 `mergeTwoLists` 合并两个有序链表
6. 返回合并后的头节点

## C++ 代码讲解

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
    ListNode* middleNode(ListNode* head) {
        ListNode* pre = head;
        ListNode* slow = head;
        ListNode* fast = head;

        // slow 每次走一步，fast 每次走两步；pre 记录 slow 的前一个节点
        while (fast && fast->next) {
            pre = slow;
            slow = slow->next;
            fast = fast->next->next;
        }

        // 从中点前断开，head 仍是前半段头，slow 是后半段头
        pre->next = nullptr;
        return slow;
    }

    ListNode* mergeTwoLists(ListNode* list1, ListNode* list2) {
        ListNode dummy;
        ListNode* cur = &dummy;

        // 合并两个已排序链表，每次接上较小的当前节点
        while (list1 && list2) {
            if (list1->val <list2->val) {
                cur->next = list1;
                list1 = list1->next;
            }else {
                cur->next = list2;
                list2 = list2->next;
            }
            cur = cur->next;
        }

        // 剩余链表本身已经有序，可以整段接上
        cur->next = list1 ? list1 : list2;
        return dummy.next;
    }

public:
    ListNode* sortList(ListNode* head) {
        if (head == nullptr || head->next == nullptr) {
            return head;
        }

        ListNode* head2 = middleNode(head);

        head = sortList(head);
        head2 = sortList(head2);

        return mergeTwoLists(head, head2);
    }
};
```

关键点：

- `sortList`：归并排序主函数，负责递归拆分和合并
- `middleNode`：找到后半段头节点，并把原链表断成两段
- `pre`：始终记录 `slow` 的前一个节点，用来执行 `pre->next = nullptr`
- `slow`：最终指向后半段的头节点
- `fast`：每次走两步，用来让 `slow` 走到中点
- `mergeTwoLists`：复用合并两个升序链表模板
- `dummy` / `cur`：虚拟头节点和结果链表尾指针

递归不变量 / 状态含义：

- `sortList(head)` 返回以 `head` 为头的这段链表排序后的头节点
- 每次递归前，链表都会被断成两个独立子链表
- 递归到底时，空链表或单节点链表天然有序
- 合并阶段的两个输入链表都已经各自有序

边界处理：

- 空链表直接返回 `nullptr`
- 单节点链表直接返回自己，避免继续拆分
- `middleNode` 只会在链表至少两个节点时调用，所以 `pre->next = nullptr` 是安全的
- 两节点链表会被拆成一个节点和一个节点，递归能正常收敛
- 合并时如果一条链表先走完，剩余链表可以直接接到结果末尾

## 复杂度分析

- 时间复杂度：O(n log n)，每层合并访问所有节点一次，递归层数是 O(log n)
- 空间复杂度：O(log n)，递归调用栈深度；如果使用自底向上的迭代归并，可以做到 O(1) 额外空间

## 面试讲法

可以这样讲：

链表排序我会优先用归并排序。因为链表不适合按下标随机访问，但很适合用快慢指针找中点，也很适合通过改 `next` 指针合并两个有序链表。我先用快慢指针把链表拆成两半，注意用 `pre` 记录中点前一个节点并断开链表。然后递归排序左右两半，最后复用合并两个有序链表的模板，用虚拟头节点和尾指针把两个有序子链表合并起来。递归拆分有 `log n` 层，每层合并总共处理 `n` 个节点，所以时间复杂度是 O(n log n)。

可能追问：

- 为什么不用快速排序：链表不能 O(1) 随机访问下标，快排分区和选枢轴不如数组方便
- 为什么要断链：不断开的话，前半段递归仍然连着后半段，递归无法正确缩小范围
- 如何做到 O(1) 空间：可以用自底向上的迭代归并，按长度 `1, 2, 4...` 分段合并
- 合并时是否新建节点：不需要，直接复用原链表节点，只改变 `next` 指针

## 模板沉淀

- 是否沉淀模板：是
- 对应模板：[链表归并排序](../templates/cpp/linked-list.md)
- 可复用点：快慢指针拆分链表，递归排序两半，再复用合并两个有序链表

## 易错点

- 找到中点后必须断链：`pre->next = nullptr`
- 递归出口必须处理空链表和单节点链表
- 两节点链表要能拆开，否则会无限递归
- 合并两个链表时，尾指针 `cur` 接节点后必须前进
- 剩余链表可以直接接上，不需要继续逐个比较
- 递归版空间复杂度是 O(log n)，题目进阶如果严格要求 O(1)，要写自底向上的迭代归并

## 复盘卡片

- 一句话记忆：排序链表 = 快慢指针断成两半，递归排序，再归并两个有序链表
- 下次先想：链表排序优先归并，因为拆分和合并都只靠指针
- 如果写错，优先检查：是否断链、递归出口、两节点能否拆开、合并尾指针是否前进
