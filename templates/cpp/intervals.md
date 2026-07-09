# 区间

## 合并区间

适用：给定一组闭区间，合并所有重叠区间。

```cpp
vector<vector<int>> merge(vector<vector<int>>& intervals) {
    vector<vector<int>> ans;
    sort(intervals.begin(), intervals.end());

    for (auto& interval : intervals) {
        int start = interval[0];
        int end = interval[1];

        if (ans.empty() || start > ans.back()[1]) {
            ans.push_back({start, end});
        } else {
            ans.back()[1] = max(ans.back()[1], end);
        }
    }

    return ans;
}
```

## 易错点

- 闭区间中 `[1,4]` 和 `[4,5]` 算重叠，应该合并
- 不重叠条件是 `start > ans.back()[1]`
- 重叠时右端点要取最大值：`max(ans.back()[1], end)`
- 排序后只需要和答案最后一个区间比较
