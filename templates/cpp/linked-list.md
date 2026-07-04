# 链表

## 虚拟头节点合并两个有序链表

适用：合并两个有序链表，或任何需要从头开始构建结果链表的场景。

```cpp
ListNode* mergeTwoLists(ListNode* list1, ListNode* list2) {
    ListNode dummy(-1);
    ListNode* tail = &dummy;

    while (list1 != nullptr && list2 != nullptr) {
        if (list1->val < list2->val) {
            tail->next = list1;
            list1 = list1->next;
        } else {
            tail->next = list2;
            list2 = list2->next;
        }
        tail = tail->next;
    }

    tail->next = list1 == nullptr ? list2 : list1;
    return dummy.next;
}
```

## 虚拟头节点删除节点

适用：删除链表节点，尤其是头节点也可能被删除的情况。

```cpp
ListNode dummy(0, head);
ListNode* prev = &dummy;
ListNode* cur = head;

while (cur != nullptr) {
    if (/* cur should be removed */) {
        prev->next = cur->next;
    } else {
        prev = cur;
    }
    cur = cur->next;
}

return dummy.next;
```

## 反转链表

适用：整条链表反转、区间反转、K 个一组反转的基础操作。

```cpp
ListNode* prev = nullptr;
ListNode* cur = head;

while (cur != nullptr) {
    ListNode* next = cur->next;
    cur->next = prev;
    prev = cur;
    cur = next;
}

return prev;
```

## 区间反转链表

适用：只反转链表中的一段，例如第 `left` 到第 `right` 个节点。

```cpp
ListNode* reverseBetween(ListNode* head, int left, int right) {
    // 虚拟头节点统一处理 left == 1 时头节点变化的情况
    ListNode dummy(0);
    dummy.next = head;

    // preLeft 指向反转区间的前一个节点
    ListNode* preLeft = &dummy;
    for (int i = 1; i < left; ++i) {
        preLeft = preLeft->next;
    }

    // segmentTail 是原区间头，反转后会变成区间尾
    ListNode* segmentTail = preLeft->next;
    ListNode* prev = nullptr;
    ListNode* cur = segmentTail;

    // 只反转 left 到 right 这一段，共 right - left + 1 个节点
    for (int i = 0; i < right - left + 1; ++i) {
        ListNode* next = cur->next;
        cur->next = prev;
        prev = cur;
        cur = next;
    }

    // prev 是区间新头，cur 是区间右侧第一个节点
    segmentTail->next = cur;
    preLeft->next = prev;

    return dummy.next;
}
```

## K 个一组反转链表

适用：每 `k` 个节点一组反转，不足 `k` 个的尾部节点保持原顺序。

```cpp
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
        // 当前组旧头，反转后会变成当前组尾
        ListNode* groupTail = cur;
        ListNode* prev = nullptr;

        // 只反转当前组的 k 个节点
        for (int i = 0; i < k; ++i) {
            ListNode* next = cur->next;
            cur->next = prev;
            prev = cur;
            cur = next;
        }

        // prev 是当前组新头，cur 是下一组开头
        groupTail->next = cur;
        preLeft->next = prev;
        preLeft = groupTail;

        n -= k;
    }

    return dummy.next;
}
```

## 快慢指针判环

适用：判断链表是否有环。

```cpp
bool hasCycle(ListNode* head) {
    ListNode* slow = head;
    ListNode* fast = head;

    while (fast != nullptr && fast->next != nullptr) {
        slow = slow->next;
        fast = fast->next->next;

        if (slow == fast) {
            return true;
        }
    }

    return false;
}
```

## 易错点

- 结果链表头节点不确定时，用 `dummy` 统一处理
- 尾插一个节点后，`tail` 必须移动到新尾部
- 改变 `cur->next` 前，如果还要继续遍历，先保存 `next`
- 刷题里可以用 `new` 创建虚拟头节点；更推荐栈对象 `ListNode dummy(-1)`，不用手动释放
- 区间反转时先保存原区间头作为反转后的尾节点，方便接回右侧链表
- K 组反转前必须先确认剩余节点不少于 `k` 个，不足一组时保持原顺序
- 快慢指针里访问 `fast->next->next` 前，要先判断 `fast` 和 `fast->next`
- 反转链表结束时 `cur` 是空，新的头节点是 `prev`
