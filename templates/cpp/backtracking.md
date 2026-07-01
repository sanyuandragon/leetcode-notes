# 回溯

## 全排列

适用：数组元素互不相同，返回所有排列。

```cpp
vector<vector<int>> permute(vector<int>& nums) {
    vector<vector<int>> ans;
    vector<int> path;
    vector<bool> used(nums.size(), false);

    function<void()> dfs = [&]() {
        if (path.size() == nums.size()) {
            ans.push_back(path);
            return;
        }

        for (int i = 0; i < (int)nums.size(); ++i) {
            if (used[i]) {
                continue;
            }

            path.push_back(nums[i]);
            used[i] = true;

            dfs();

            used[i] = false;
            path.pop_back();
        }
    };

    dfs();
    return ans;
}
```

## 组合/子集骨架

适用：从左到右选择元素，每个元素通常只考虑一次。

```cpp
void backtrack(vector<int>& nums, int startIndex, vector<int>& path) {
    // collect path when needed

    for (int i = startIndex; i < (int)nums.size(); ++i) {
        path.push_back(nums[i]);
        backtrack(nums, i + 1, path);
        path.pop_back();
    }
}
```

## 易错点

- 排列问题每层从 `0` 开始枚举，靠 `used` 判断是否可选
- 组合/子集问题通常从 `startIndex` 开始枚举，避免重复组合
- 递归前做选择，递归后撤销选择
- 如果输入有重复元素，先排序，再判断同层重复

