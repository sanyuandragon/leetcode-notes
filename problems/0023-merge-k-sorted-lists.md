# 23. 合并 K 个升序链表

## 题目信息

- 难度：困难
- 链接：https://leetcode.cn/problems/merge-k-sorted-lists/
- 主知识点：链表
- 相关知识点：分治、归并、虚拟头节点、优先队列
- 复习状态：`new`

## 一眼识别

看到这些信号时，应该想到本题方法：

- 有 `k` 个已经升序排列的链表
- 要合并成一个整体升序链表
- 可以复用“合并两个有序链表”的模板
- `k` 很大时，不能简单地从左到右一个个累加合并

## 解题思路

### 暴力思路

最直接的做法是从左到右依次合并：

```text
(((list0 + list1) + list2) + list3) ...
```

这样会让前面已经合并好的长链表被反复扫描。假设总节点数是 `N`，链表个数是 `k`，最坏时间复杂度会接近 O(kN)。

也可以把所有节点值放进数组，排序后重新建链表。但这样没有充分利用每个链表本身已经有序的条件，而且会额外创建节点。

### 优化关键

你的写法使用的是分治合并，也就是归并排序的思想：

- 先把相邻两个链表两两合并
- 再把合并后的链表继续两两合并
- 每一轮合并的间隔 `step` 翻倍

这样每个节点在每一层合并中最多被处理一次，而合并层数是 `log k`，总时间复杂度是 O(N log k)。

### 最终做法

1. 如果 `lists` 为空，直接返回 `nullptr`
2. 写一个 `mergeTwoLists` 函数，负责合并两个升序链表
3. 设置 `step = 1`
4. 每一轮把下标差为 `step` 的链表两两合并
5. 合并结果放回左侧位置 `lists[i]`
6. 每轮结束后 `step *= 2`
7. 最终所有链表都合并到 `lists[0]`

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
public:
    ListNode* mergeTwoLists(ListNode* list1, ListNode* list2) {
        ListNode dummy(0);
        ListNode* cur = &dummy;

        // 复用合并两个升序链表的模板，每次接上较小的当前节点
        while (list1 && list2) {
            if (list1->val < list2->val) {
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

    ListNode* mergeKLists(vector<ListNode*>& lists) {
        int m = lists.size();
        if (m == 0) {
            return nullptr;
        }

        // step 表示本轮要合并的两个链表之间的下标间隔
        for (int step = 1; step < m; step *= 2) {
            for (int i = 0; i < m - step; i += step * 2) {
                // 把 lists[i] 和 lists[i + step] 合并，结果放回 lists[i]
                lists[i] = mergeTwoLists(lists[i], lists[i + step]);
            }
        }

        return lists[0];
    }
};
```

关键点：

- `mergeTwoLists`：合并两个升序链表，是整题的基础模板
- `dummy`：虚拟头节点，用来统一处理合并后链表的头节点
- `cur`：合并结果的尾指针
- `m`：链表个数
- `step`：当前轮合并的跨度，依次是 `1, 2, 4, 8...`
- `i += step * 2`：每次跳过已经被合并成一组的区间
- `lists[i] = mergeTwoLists(...)`：把本组的合并结果放回左侧位置

循环不变量 / 状态含义：

- 当 `step = 1` 时，每组 2 个链表被合并
- 当 `step = 2` 时，每组 4 个原始链表被合并
- 当 `step = 4` 时，每组 8 个原始链表被合并
- 每轮结束后，`lists[0]、lists[step * 2]、...` 这些位置保存了更大区间的合并结果
- 当 `step >= m` 时，所有链表都已经合并到 `lists[0]`

边界处理：

- `lists` 为空时要返回 `nullptr`
- `lists` 中某个链表本身可以是空指针，`mergeTwoLists` 能自然处理
- `m` 不是 2 的幂时，最后剩下没有配对的链表会留到下一轮再合并
- 合并过程复用原节点，没有新建数据节点，也不会改变节点值

## 复杂度分析

- 时间复杂度：O(N log k)，其中 `N` 是所有链表节点总数，`k` 是链表个数
- 空间复杂度：O(1)，迭代分治只使用常数额外指针，不计输入数组本身

## 面试讲法

可以这样讲：

我先写一个合并两个有序链表的函数，这和第 21 题一样，用虚拟头节点和尾指针，每次接入当前较小的节点。对于 `k` 个链表，如果一个个顺序合并，前面的长链表会被反复扫描，所以我用分治思想：第一轮两两合并，第二轮把已经合并好的结果再两两合并，每一轮跨度翻倍。这样每个节点在每一层最多被处理一次，一共有 `log k` 层，所以总时间复杂度是 O(N log k)。

可能追问：

- 为什么不用顺序合并：顺序合并会让已合并的长链表被重复扫描，最坏 O(kN)
- 能不能用小根堆：可以，把每个链表头放入优先队列，每次取最小节点，时间也是 O(N log k)
- 分治递归和迭代有什么区别：递归更直观，迭代避免递归调用栈
- 为什么剩余链表可以直接接上：两个输入链表各自本来有序，剩余部分仍然整体有序

## 模板沉淀

- 是否沉淀模板：是
- 对应模板：[合并 K 个升序链表](../templates/cpp/linked-list.md)
- 可复用点：多个有序链表或有序数组合并时，可以用“两两合并 + step 翻倍”的分治结构

## 易错点

- 忘记处理 `lists.size() == 0`
- `for (int i = 0; i < m - step; i += step * 2)` 的边界容易写错
- `step *= 2` 才是分治合并，不能每轮只 `step++`
- 合并两个链表后，要把结果写回 `lists[i]`
- 剩余链表不用逐个节点处理，直接接到 `cur->next`
- 如果用优先队列做法，比较器要按节点值建小根堆

## 复盘卡片

- 一句话记忆：合并 K 个链表 = 先会合并两个，再用 step 翻倍做两两归并
- 下次先想：顺序合并会重复扫描长链表，分治能把层数压到 `log k`
- 如果写错，优先检查：空数组、`i < m - step`、`i += step * 2`、结果是否写回 `lists[i]`
