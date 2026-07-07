# 142. 环形链表 II

## 题目信息

- 难度：中等
- 链接：https://leetcode.cn/problems/linked-list-cycle-ii/
- 主知识点：链表
- 相关知识点：哈希表、快慢指针、Floyd 判环
- 复习状态：`new`

## 一眼识别

看到这些信号时，应该想到本题方法：

- 题目要求返回链表开始入环的第一个节点
- 如果无环，要返回 `nullptr`
- 不能修改链表结构
- 判断的是节点地址是否重复访问，不是节点值是否重复
- 进阶要求 O(1) 空间时，可以用 Floyd 快慢指针

## 解题思路

### 解法 1：哈希表记录访问过的节点

最直观的做法是从 `head` 开始遍历链表，用 `unordered_set<ListNode*>` 保存已经访问过的节点地址。

遍历到某个节点时：

- 如果它已经在集合里，说明这是第一次重复遇到的节点，也就是入环点
- 如果它没出现过，就加入集合，然后继续走
- 如果走到 `nullptr`，说明链表无环

为什么第一次重复的节点就是入环点：

链表在进入环之前，每个节点只会经过一次。进入环之后继续沿 `next` 走，第一次遇到已经访问过的节点，必然是环的入口。

### 解法 2：快慢指针找入环点

进阶 O(1) 空间可以用 Floyd 算法：

1. `slow` 每次走一步，`fast` 每次走两步
2. 如果无环，`fast` 会走到 `nullptr`
3. 如果有环，`slow` 和 `fast` 会在环中某个节点相遇
4. 相遇后，让一个指针回到 `head`
5. 两个指针每次都走一步，再次相遇的位置就是入环点

这部分面试里经常会追问，但你的哈希表写法已经能正确解决本题。

## C++ 代码讲解

### 解法 1：哈希表

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        unordered_set<ListNode*> visited;

        while (head != nullptr) {
            // 如果当前节点已经访问过，它就是第一次重复出现的节点，也就是入环点
            if (visited.count(head)) {
                return head;
            }

            // 记录当前节点地址，再继续向后走
            visited.insert(head);
            head = head->next;
        }

        // 能走到 nullptr，说明链表没有环
        return nullptr;
    }
};
```

关键点：

- `unordered_set<ListNode*> visited`：保存访问过的节点地址
- `visited.count(head)`：判断当前节点是否已经出现过
- `visited.insert(head)`：第一次遇到当前节点时，把它记录下来
- `head = head->next`：沿链表继续向后走
- `return nullptr`：走到空指针说明无环

循环不变量 / 状态含义：

- 每次进入循环时，`visited` 中保存的是从原始头节点到当前节点之前所有访问过的节点
- 如果当前 `head` 已经存在于 `visited`，说明沿 `next` 指针走回了旧节点
- 第一次重复出现的节点就是环的入口

### 解法 2：O(1) 空间快慢指针

```cpp
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        ListNode* slow = head;
        ListNode* fast = head;

        while (fast != nullptr && fast->next != nullptr) {
            slow = slow->next;
            fast = fast->next->next;

            if (slow == fast) {
                ListNode* p = head;
                while (p != slow) {
                    p = p->next;
                    slow = slow->next;
                }
                return p;
            }
        }

        return nullptr;
    }
};
```

关键点：

- 第一次相遇只说明有环，不一定是入环点
- 相遇后，一个指针从 `head` 出发，一个指针从相遇点出发
- 两个指针每次走一步，再次相遇处就是入环点
- 整个过程只用常数个指针，空间复杂度 O(1)

边界处理：

- 空链表：循环不进入，返回 `nullptr`
- 单节点无环：`fast->next == nullptr`，返回 `nullptr`
- 单节点自环：哈希表解法会第二次遇到该节点并返回它；快慢指针解法会相遇后返回头节点
- `pos` 只是 LeetCode 用来构造链表的参数，不会传入函数

## 复杂度分析

- 哈希表解法时间复杂度：O(n)，每个节点最多访问一次再遇到入环点
- 哈希表解法空间复杂度：O(n)，最坏情况下保存所有节点
- 快慢指针解法时间复杂度：O(n)
- 快慢指针解法空间复杂度：O(1)

## 面试讲法

可以这样讲：

我先给出哈希表写法：遍历链表时，把每个访问过的节点地址放进 `unordered_set`。如果当前节点已经在集合里，说明它是第一次重复访问的节点，也就是入环点；如果遍历到空指针，说明无环。这个方法直观，时间 O(n)，空间 O(n)。

如果面试官要求 O(1) 空间，我会改用快慢指针。先用 Floyd 判环，快慢指针在环内相遇后，让一个指针回到头节点，另一个留在相遇点。两个指针同步每次走一步，再次相遇的位置就是入环点。

可能追问：

- 为什么哈希表要存节点指针：不同节点的值可能相同，判断环必须看是否回到同一个节点
- 为什么第一次重复节点是入口：入环前不会重复，进入环后第一次回到旧节点就是环开始的位置
- 快慢指针为什么能找到入口：相遇点到入口的距离和头节点到入口的距离在环长意义下相等
- 能不能修改链表做标记：题目要求不允许修改链表

## 模板沉淀

- 是否沉淀模板：是
- 对应模板：[环形链表 II](../templates/cpp/linked-list.md)
- 可复用点：哈希集合记录节点地址能直接找到第一个重复节点；Floyd 相遇后双指针同步走能 O(1) 找入环点

## 易错点

- `unordered_set` 里存的是 `ListNode*`，不是 `node->val`
- 判断重复要在插入当前节点之前
- 不要把 `pos` 当作函数参数使用
- 快慢指针解法中，相遇点不一定是入环点
- 返回的是节点指针，不是节点下标或节点值

## 复盘卡片

- 一句话记忆：入环点 = 第一次重复访问的节点；O(1) 写法是快慢相遇后从头和相遇点同步走
- 下次先想：题目要返回节点，不是判断有没有环
- 如果写错，优先检查：是否比较节点地址、哈希表查询插入顺序、快慢指针判空条件
