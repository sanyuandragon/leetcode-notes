# 二分查找

## 左边界

找第一个 `>= target` 的位置。

```cpp
int lowerBound(vector<int>& nums, int target) {
    int left = 0;
    int right = (int)nums.size();

    while (left < right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] >= target) {
            right = mid;
        } else {
            left = mid + 1;
        }
    }

    return left;
}
```

## 答案二分

找最小可行答案。

```cpp
int left = minAnswer;
int right = maxAnswer;

while (left < right) {
    int mid = left + (right - left) / 2;
    if (check(mid)) {
        right = mid;
    } else {
        left = mid + 1;
    }
}

return left;
```

## 易错点

- `check(mid)` 必须单调
- `right` 是否能取到答案要在初始化时确认
- 每次循环都要缩小区间

