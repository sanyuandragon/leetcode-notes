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
- 快慢指针里访问 `fast->next->next` 前，要先判断 `fast` 和 `fast->next`
- 反转链表结束时 `cur` 是空，新的头节点是 `prev`
