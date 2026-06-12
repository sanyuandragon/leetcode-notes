# 并查集

## 标准模板

```cpp
class UnionFind {
private:
    vector<int> parent;
    vector<int> size;

public:
    UnionFind(int n) : parent(n), size(n, 1) {
        iota(parent.begin(), parent.end(), 0);
    }

    int find(int x) {
        if (parent[x] != x) {
            parent[x] = find(parent[x]);
        }
        return parent[x];
    }

    bool unite(int a, int b) {
        int rootA = find(a);
        int rootB = find(b);
        if (rootA == rootB) {
            return false;
        }

        if (size[rootA] < size[rootB]) {
            swap(rootA, rootB);
        }
        parent[rootB] = rootA;
        size[rootA] += size[rootB];
        return true;
    }

    bool connected(int a, int b) {
        return find(a) == find(b);
    }
};
```

## 易错点

- `unite` 前必须对两个点分别 `find`
- 按集合大小合并可以避免树过深
- 如果题目需要连通分量数量，成功合并时再 `--components`

