# 25. K 个一组翻转链表

## 题目信息

- 题号：25
- 标题：K 个一组翻转链表
- 难度：困难
- 标签：链表、指针、K 组反转、虚拟头节点
- 链接：https://leetcode.cn/problems/reverse-nodes-in-k-group/

## 一眼识别

看到“每 `k` 个节点一组翻转，剩余不足 `k` 个保持原顺序”，优先想到：

- 这是第 206 题“反转链表”和第 92 题“区间反转”的升级版。
- 每一组都可以看成一个长度固定为 `k` 的区间反转。
- 头节点可能变化，用虚拟头节点 `dummyNode`。
- 每组反转后都要把“上一段尾部、当前组新头、当前组新尾、下一组开头”重新接起来。

## 解题思路

暴力上可以把节点放进数组，每 `k` 个一组交换指针，但这样会用额外空间，也不符合链表题练指针的目的。

你的写法先统计链表长度 `n`，这样后面只要 `n >= k` 就确定还有完整的一组可以反转，不需要每次提前探测第 `k` 个节点是否存在。

整体流程：

1. 建立虚拟头节点 `dummyNode`，指向原链表头。
2. 先遍历链表统计总长度 `n`。
3. `preLeft` 指向当前要反转这一组的前一个节点。
4. `cur` 指向当前组的第一个节点。
5. 当剩余长度 `n >= k` 时，反转接下来的 `k` 个节点。
6. 反转后：
   - `pre` 指向当前组反转后的新头。
   - `cur` 指向下一组的第一个节点。
   - `preLeft->next` 在重连前仍是当前组旧头，也就是反转后的组尾。
7. 用 `tmp` 记录当前组旧头，完成两端重连，再让 `preLeft = tmp`，准备处理下一组。

## C++ 代码讲解

按你的要求，下面保留原代码，不改写、不加注释：

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
    ListNode* reverseKGroup(ListNode* head, int k) {
        ListNode* dummyNode = new ListNode(0);
        dummyNode->next = head;
        int n = 0;
        for (ListNode* i = head; i; i = i->next) {
            n++;
        }

        ListNode* preLeft = dummyNode;
        ListNode* pre = nullptr;
        ListNode* cur = head;
        ListNode* nxt = nullptr;

        for (; n >= k; n = n - k) {
            for (int i = 0; i < k; ++i) {
                nxt = cur->next;
                cur->next = pre;
                pre = cur;
                cur = nxt;
            }
            ListNode* tmp = preLeft->next;
            preLeft->next->next = cur;
            preLeft->next = pre;
            preLeft = tmp;
        }
        return dummyNode->next;
    }
};
```

关键变量：

- `dummyNode`：虚拟头节点，统一处理第一组反转后头节点变化的情况。
- `n`：剩余还没按组处理的节点数量；每处理一组减去 `k`。
- `preLeft`：当前反转组的前一个节点，也是上一组反转后的尾节点。
- `pre`：反转过程中指向已经反转好的部分的头；每组结束后是当前组新头。
- `cur`：当前正在处理的节点；每组结束后指向下一组第一个节点。
- `nxt`：临时保存后继节点，避免改 `cur->next` 后断链。
- `tmp`：当前组反转前的头节点；反转后它会变成当前组尾，并成为下一轮的 `preLeft`。

这份代码里最值得注意的点是：`pre` 没有在每一组开始前重置为 `nullptr`。常见模板会在每组开始时重置 `pre`，但你的写法也能成立，因为当前组旧头的 `next` 会在这句里被重新覆盖：

```cpp
preLeft->next->next = cur;
```

也就是说，反转过程中第一步临时连到上一段的指针，最后会被“当前组尾接回下一段”覆盖掉。

重连逻辑：

- `ListNode* tmp = preLeft->next;`：保存当前组旧头，它反转后会变成组尾。
- `preLeft->next->next = cur;`：当前组尾接到下一组开头。
- `preLeft->next = pre;`：上一段尾部接到当前组新头。
- `preLeft = tmp;`：移动到当前组尾，准备处理下一组。

## 可选优化写法

如果想让代码更接近通用模板，可以在每组开始时把 `pre` 重置为 `nullptr`，并提前保存当前组尾。这样每一组都是独立的“反转长度为 `k` 的区间”，指针含义更直观。

```cpp
class Solution {
public:
    ListNode* reverseKGroup(ListNode* head, int k) {
        ListNode dummy(0);
        dummy.next = head;

        int n = 0;
        for (ListNode* p = head; p != nullptr; p = p->next) {
            ++n;
        }

        ListNode* preLeft = &dummy;
        ListNode* cur = head;

        while (n >= k) {
            ListNode* groupTail = cur;
            ListNode* prev = nullptr;

            for (int i = 0; i < k; ++i) {
                ListNode* next = cur->next;
                cur->next = prev;
                prev = cur;
                cur = next;
            }

            groupTail->next = cur;
            preLeft->next = prev;
            preLeft = groupTail;
            n -= k;
        }

        return dummy.next;
    }
};
```

这个版本和你的思路相同，只是把“每组反转”写得更独立：`prev` 只服务当前组，`groupTail` 明确表示当前组反转后的尾节点。

## 复杂度分析

- 时间复杂度：`O(n)`，统计长度遍历一次，后续每个节点最多再被反转一次。
- 空间复杂度：`O(1)`，只使用常数个指针。

## 面试讲法

这题可以看成“每次反转长度为 `k` 的区间”。我先用虚拟头节点处理第一组反转后头节点变化的问题，再统计链表总长度。只要剩余节点数不少于 `k`，就对当前组做一次局部反转。反转结束后，`pre` 是当前组新头，`cur` 是下一组开头，而当前组原来的头会变成组尾，所以要把组尾接到 `cur`，再把上一段接到 `pre`。最后把 `preLeft` 移到当前组尾，继续下一组。不足 `k` 个的节点不进入循环，自然保持原顺序。

## 模板沉淀

可以沉淀为“K 个一组反转链表”模板，核心是：

1. 先确认当前剩余节点至少有 `k` 个。
2. 保存当前组旧头作为 `groupTail`。
3. 反转 `k` 个节点。
4. `groupTail` 接下一段，`preLeft` 接当前组新头。
5. `preLeft` 移到 `groupTail`。

已更新到：[链表模板](../templates/cpp/linked-list.md)。

## 易错点

- 不足 `k` 个节点不能反转，要保持原顺序。
- 每组反转前要知道这一组是完整的，否则会把尾部不足 `k` 的节点也反转。
- 改 `cur->next` 前必须保存 `nxt`。
- 反转后当前组旧头会变成组尾，后续连接要靠它完成。
- 你的写法中 `pre` 不重置也能成立，但理解成本较高；复盘时重点确认 `preLeft->next->next = cur` 覆盖了临时连接。
- 刷题里 `new ListNode(0)` 可以通过；工程代码更推荐 `ListNode dummy(0)`，避免手动释放问题。

## 复盘卡片

- 一句话记忆：K 组反转 = 每次确认够 `k` 个，再做长度为 `k` 的区间反转。
- 下次重点看：反转后四个位置的关系：上一段尾 `preLeft`、当前组新头 `pre`、当前组新尾 `tmp`、下一段头 `cur`。
- 默写关键句：

```cpp
ListNode* tmp = preLeft->next;
preLeft->next->next = cur;
preLeft->next = pre;
preLeft = tmp;
```
