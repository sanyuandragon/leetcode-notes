# 146. LRU 缓存

## 题目信息

- 难度：中等
- 链接：https://leetcode.cn/problems/lru-cache/
- 主知识点：设计
- 相关知识点：哈希表、双向链表、LRU、哨兵节点
- 复习状态：`new`

## 一眼识别

看到这些信号时，应该想到本题方法：

- `get` 和 `put` 都要求 O(1) 平均时间
- 需要按“最近使用”顺序淘汰元素
- 既要快速通过 key 找到 value，又要快速移动节点顺序
- 哈希表负责查找，双向链表负责维护使用顺序

## 解题思路

### 暴力思路

可以用数组或普通链表保存缓存顺序，每次 `get/put` 时线性查找 key，并把节点移动到最近使用位置。但查找和删除都可能是 O(n)，不满足 O(1) 要求。

### 优化关键

LRU 需要两个能力：

- O(1) 查找某个 key 是否存在
- O(1) 把某个节点移动到最近使用位置，并删除最久未使用节点

所以组合两个结构：

- `unordered_map<int, DLinkedNode*> mp`：通过 key 直接找到链表节点
- 双向链表：头部表示最近使用，尾部表示最久未使用

使用两个哨兵节点 `head` 和 `tail` 可以统一插入、删除逻辑：

```text
head <-> 最近使用 ... 最久未使用 <-> tail
```

### 最终做法

1. 初始化容量、大小、哈希表、双向链表哨兵头尾
2. `get(key)`：
   - key 不存在，返回 `-1`
   - key 存在，通过哈希表找到节点
   - 把节点移动到头部，表示最近使用
   - 返回节点值
3. `put(key, value)`：
   - key 已存在，更新值并移动到头部
   - key 不存在，新建节点，加入哈希表并插入头部
   - 如果超过容量，删除尾部前一个节点，并从哈希表中移除

## C++ 代码讲解

```cpp
struct DLinkedNode {
    int key;
    int value;
    DLinkedNode *prev;
    DLinkedNode *next;

    DLinkedNode() : key(0), value(0), prev(nullptr), next(nullptr) {}
    DLinkedNode(int k, int v) : key(k), value(v), prev(nullptr), next(nullptr) {}
};

class LRUCache {
private:
    unordered_map<int, DLinkedNode*> mp;
    int size;
    int capacity;
    DLinkedNode* head;
    DLinkedNode* tail;

public:
    LRUCache(int _capacity) {
        capacity = _capacity;
        size = 0;
        head = new DLinkedNode();
        tail = new DLinkedNode();
        head->next = tail;
        tail->prev = head;
    }

    int get(int key) {
        if (!mp.count(key)) {
            return -1;
        }

        DLinkedNode* node = mp[key];
        moveToHead(node);
        return node->value;
    }

    void put(int key, int value) {
        if (mp.count(key)) {
            DLinkedNode* node = mp[key];
            node->value = value;
            moveToHead(node);
        } else {
            DLinkedNode* node = new DLinkedNode(key, value);
            mp[key] = node;
            addToHead(node);
            ++size;

            if (size > capacity) {
                DLinkedNode* node = removeTail();
                mp.erase(node->key);
                delete node;
                --size;
            }
        }
    }

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

    DLinkedNode* removeTail() {
        DLinkedNode* node = tail->prev;
        removeNode(node);
        return node;
    }

    void moveToHead(DLinkedNode* node) {
        removeNode(node);
        addToHead(node);
    }
};
```

关键点：

- `DLinkedNode`：双向链表节点，需要同时保存 `key` 和 `value`
- `mp`：从 key 映射到链表节点指针，保证 O(1) 查找
- `head`、`tail`：哨兵节点，不存真实数据，用来简化边界操作
- `head->next`：最近使用的节点
- `tail->prev`：最久未使用的节点
- `addToHead`：把节点插入到头部哨兵后面
- `removeNode`：把任意真实节点从链表中摘掉
- `removeTail`：删除最久未使用节点
- `moveToHead`：先删除节点，再插入头部

循环不变量 / 状态含义：

- 哈希表中的每个 key 都对应链表中的一个真实节点
- 链表从 `head` 到 `tail` 按最近使用到最久未使用排列
- 每次访问或更新一个 key 后，对应节点都要移动到头部
- 当 `size > capacity` 时，`tail->prev` 一定是应该淘汰的节点

边界处理：

- 容量最小为 `1`，不会出现合法容量为 `0` 的情况
- 删除尾部时删除的是 `tail->prev`，不是 `tail` 哨兵
- 节点中必须保存 `key`，否则淘汰时无法从哈希表中 `erase`
- 新节点加入后如果超容量，要同时从链表和哈希表中删除
- 你的代码在 LeetCode 中可通过；实际工程中还应补析构函数释放剩余节点，避免内存泄漏

## 复杂度分析

- `get` 时间复杂度：O(1) 平均
- `put` 时间复杂度：O(1) 平均
- 空间复杂度：O(capacity)，哈希表和双向链表最多保存 `capacity` 个真实节点

## 面试讲法

可以这样讲：

LRU 要求 `get` 和 `put` 都是 O(1)，所以单独使用数组、链表或哈希表都不够。哈希表可以 O(1) 定位 key 对应的节点，但不能维护最近使用顺序；双向链表可以 O(1) 删除和移动节点，并把头部作为最近使用、尾部作为最久未使用。所以我用哈希表保存 `key -> node`，用双向链表维护使用顺序。每次访问或更新节点，就把它移动到头部；插入新节点也放到头部；超过容量时删除尾部前一个节点。

可能追问：

- 为什么不用单向链表：删除任意节点需要知道前驱，单向链表无法 O(1) 删除
- 为什么节点里要存 key：淘汰尾部节点时，需要根据节点的 key 从哈希表删除
- 为什么需要哨兵节点：统一空链表、头插、尾删等边界逻辑
- C++ 内存如何处理：删除淘汰节点时 `delete`，工程代码还应在析构函数中释放剩余节点和哨兵

## 模板沉淀

- 是否沉淀模板：是
- 对应模板：[LRU 缓存](../templates/cpp/design.md)
- 可复用点：需要同时满足“快速查找 + 快速维护顺序”的设计题，常考虑哈希表 + 双向链表

## 易错点

- 忘记在 `get` 后 `moveToHead`
- key 已存在时只更新 value，但忘记移动到头部
- 淘汰尾部节点时，忘记从哈希表删除
- 节点不保存 key，导致淘汰时无法 `mp.erase(node->key)`
- `removeTail` 不能删除 `tail` 哨兵，要删除 `tail->prev`
- 双向链表改指针时四条边都要接对

## 复盘卡片

- 一句话记忆：LRU = 哈希表定位节点，双向链表维护最近使用顺序
- 下次先想：头部最近，尾部最久；访问就移头，超容就删尾
- 如果写错，优先检查：map 和链表是否同步、淘汰是否 erase、节点是否保存 key

