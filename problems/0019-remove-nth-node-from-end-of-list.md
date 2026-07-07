# 19. 删除链表的倒数第 N 个结点

## 题目信息

- 难度：中等
- 链接：https://leetcode.cn/problems/remove-nth-node-from-end-of-list/
- 主知识点：链表
- 相关知识点：双指针、虚拟头节点、长度统计
- 复习状态：`new`

## 一眼识别

看到这些信号时，应该想到本题方法：

- 要删除倒数第 `n` 个节点
- 删除节点时需要找到它的前一个节点
- 删除的可能是头节点
- 链表头节点可能变化，适合用虚拟头节点
- 可以先求长度，也可以用快慢指针一趟完成

## 解题思路

### 解法 1：先求链表长度

先遍历一遍链表，得到总长度 `length`。

倒数第 `n` 个节点，对应正数第：

```text
length - n + 1
```

如果要删除它，需要找到它的前一个节点，也就是从虚拟头节点出发走 `length - n` 步。

使用虚拟头节点 `dummy` 的好处是：当删除的是原头节点时，也可以统一通过 `dummy.next` 改指针。

### 解法 2：快慢指针

用两个指针制造 `n` 个节点的距离：

- `first` 先从 `head` 出发走 `n` 步
- `second` 从 `dummy` 出发
- 然后 `first` 和 `second` 一起走
- 当 `first` 走到空时，`second` 正好停在要删除节点的前一个节点

这样只需要一趟扫描就能找到删除位置。

## C++ 代码讲解

### 解法 1：统计长度

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
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        ListNode dummy(0, head);
        int length = 0;

        // 第一次遍历：统计链表长度
        for (ListNode* i = head; i != nullptr; i = i->next) {
            length++;
        }

        // cur 最终停在要删除节点的前一个节点
        ListNode* cur = &dummy;
        for (int i = 0; i < length - n; ++i) {
            cur = cur->next;
        }

        // 删除 cur->next
        ListNode* temp = cur->next;
        cur->next = cur->next->next;
        delete temp;

        return dummy.next;
    }
};
```

关键点：

- `dummy`：虚拟头节点，统一处理删除头节点的情况
- `length`：链表总长度
- `length - n`：从虚拟头节点走到待删除节点前驱的步数
- `cur`：待删除节点的前一个节点
- `temp`：保存待删除节点，改链后释放
- `return dummy.next`：返回新的头节点

循环不变量 / 状态含义：

- 统计长度后，`length` 是链表节点总数
- 第二个循环走完后，`cur` 指向倒数第 `n` 个节点的前驱
- `cur->next` 就是要删除的节点

### 解法 2：快慢指针

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
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        ListNode* dummy = new ListNode(0, head);
        ListNode* first = head;
        ListNode* second = dummy;

        // first 先走 n 步，制造 first 和 second 之间的距离
        for (int i = 0; i < n; ++i) {
            first = first->next;
        }

        // first 到空时，second 正好在待删除节点的前一个位置
        while (first) {
            first = first->next;
            second = second->next;
        }

        ListNode* temp = second->next;
        second->next = second->next->next;
        delete temp;

        return dummy->next;
    }
};
```

关键点：

- `dummy`：虚拟头节点，保证删除头节点时也有前驱
- `first`：快指针，先走 `n` 步
- `second`：慢指针，从虚拟头节点出发
- `while (first)`：两个指针同步前进，直到快指针走到空
- 循环结束后，`second->next` 就是要删除的节点

状态含义：

- `first` 和 `second` 之间始终相隔 `n` 个节点
- 当 `first == nullptr` 时，`second` 位于倒数第 `n` 个节点的前驱
- 虚拟头节点让 `n == length` 时也能删除原头节点

边界处理：

- 删除头节点：解法 1 中 `length - n == 0`，`cur` 留在 `dummy`；解法 2 中 `first` 先走到空，`second` 留在 `dummy`
- 单节点链表且 `n == 1`：删除后返回 `nullptr`
- 题目保证 `1 <= n <= length`，所以 `first` 先走 `n` 步是安全的
- 第二种写法用 `new` 创建 `dummy`，LeetCode 可以通过；工程里更推荐 `ListNode dummy(0, head)`，避免虚拟头节点本身未释放

## 复杂度分析

- 解法 1 时间复杂度：O(L)，其中 `L` 是链表长度，需要两次线性扫描
- 解法 1 空间复杂度：O(1)
- 解法 2 时间复杂度：O(L)，只需要一趟主扫描
- 解法 2 空间复杂度：O(1)

## 面试讲法

可以这样讲：

我会先说两遍扫描的做法：先统计链表长度 `length`，倒数第 `n` 个节点的前驱可以从虚拟头节点出发走 `length - n` 步找到。找到前驱后，直接跳过它的下一个节点即可。虚拟头节点用来统一处理删除头节点的情况。

如果要求一趟扫描，我用快慢指针。让快指针先走 `n` 步，然后慢指针从虚拟头节点出发，两个指针一起走。当快指针走到空时，慢指针正好在待删除节点的前驱位置。然后执行删除。

可能追问：

- 为什么 `second` 从 `dummy` 出发：需要让它停在待删除节点的前驱，尤其能处理删除头节点
- 为什么快指针先走 `n` 步：制造倒数第 `n` 个节点和尾部空指针之间的距离
- 能不能不用虚拟头节点：可以，但删除头节点时要单独判断，代码更容易分叉
- 是否必须 `delete temp`：刷题里不写通常也能过；写上能表达释放被删节点的习惯

## 模板沉淀

- 是否沉淀模板：是
- 对应模板：[删除链表倒数第 N 个节点](../templates/cpp/linked-list.md)
- 可复用点：删除位置可能是头节点时用虚拟头节点；倒数位置问题可以用快慢指针制造固定距离

## 易错点

- 找到的是待删除节点的前驱，不是待删除节点本身
- 从虚拟头节点走 `length - n` 步，而不是从 `head` 走
- 快慢指针法中 `second` 要从 `dummy` 出发
- 删除头节点时必须返回 `dummy.next`
- 用 `new` 创建虚拟头节点时，注意真实工程里要释放 dummy；刷题可用栈对象避免这个问题

## 复盘卡片

- 一句话记忆：删除倒数第 N 个 = 虚拟头节点找前驱；快指针先走 n 步，慢指针停在前驱
- 下次先想：删除的是节点本身，但操作必须落在前驱节点上
- 如果写错，优先检查：是否处理删除头节点、`length - n` 是否正确、`second` 是否从 dummy 出发
