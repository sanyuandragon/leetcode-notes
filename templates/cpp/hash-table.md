# 哈希表

## 查找补数

适用：两数之和、需要快速找另一个值的问题。

```cpp
vector<int> twoSum(vector<int>& nums, int target) {
    unordered_map<int, int> pos;

    for (int i = 0; i < (int)nums.size(); ++i) {
        int need = target - nums[i];
        if (pos.find(need) != pos.end()) {
            return {pos[need], i};
        }
        pos[nums[i]] = i;
    }

    return {};
}
```

## 频次统计

```cpp
unordered_map<int, int> cnt;
for (int x : nums) {
    ++cnt[x];
}
```

## 易错点

- 判断存在性用 `find`，不要用 `mp[key]`，后者会插入默认值
- 两数之和这类题通常先查后插，避免当前元素和自己配对

