# 设计

## LRU 缓存

适用：`get` / `put` 要求 O(1)，并按最近最少使用淘汰。

```cpp
struct DLinkedNode {
    int key;
    int value;
    DLinkedNode* prev;
    DLinkedNode* next;

    DLinkedNode() : key(0), value(0), prev(nullptr), next(nullptr) {}
    DLinkedNode(int k, int v) : key(k), value(v), prev(nullptr), next(nullptr) {}
};

class LRUCache {
private:
    unordered_map<int, DLinkedNode*> cache;
    int size = 0;
    int capacity;
    DLinkedNode* head;
    DLinkedNode* tail;

public:
    LRUCache(int capacity) : capacity(capacity) {
        head = new DLinkedNode();
        tail = new DLinkedNode();
        head->next = tail;
        tail->prev = head;
    }

    int get(int key) {
        if (!cache.count(key)) {
            return -1;
        }

        DLinkedNode* node = cache[key];
        moveToHead(node);
        return node->value;
    }

    void put(int key, int value) {
        if (cache.count(key)) {
            DLinkedNode* node = cache[key];
            node->value = value;
            moveToHead(node);
            return;
        }

        DLinkedNode* node = new DLinkedNode(key, value);
        cache[key] = node;
        addToHead(node);
        ++size;

        if (size > capacity) {
            DLinkedNode* removed = removeTail();
            cache.erase(removed->key);
            delete removed;
            --size;
        }
    }

private:
    void addToHead(DLinkedNode* node) {
        node->prev = head;
        node->next = head->next;
        head->next->prev = node;
        head->next = node;
    }

    void removeNode(DLinkedNode* node) {
        node->prev->next = node->next;
        node->next->prev = node->prev;
    }

    void moveToHead(DLinkedNode* node) {
        removeNode(node);
        addToHead(node);
    }

    DLinkedNode* removeTail() {
        DLinkedNode* node = tail->prev;
        removeNode(node);
        return node;
    }
};
```

## 易错点

- `get` 命中后也算使用，要移动到头部
- `put` 更新旧 key 后也要移动到头部
- 淘汰节点时要同时从链表和哈希表删除
- 节点必须保存 `key`，否则删除尾部时无法清理哈希表
- 哨兵 `head/tail` 不是真实节点，不要被淘汰

