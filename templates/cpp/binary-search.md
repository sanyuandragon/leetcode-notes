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

## 旋转排序数组搜索

适用：数组原本升序、元素互不相同，但被旋转过，需要 O(log n) 搜索目标值。

```cpp
int search(vector<int>& nums, int target) {
    int left = 0;
    int right = (int)nums.size() - 1;

    while (left <= right) {
        int mid = left + (right - left) / 2;

        if (nums[mid] == target) {
            return mid;
        }

        if (nums[left] <= nums[mid]) {
            if (nums[left] <= target && target <= nums[mid]) {
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        } else {
            if (nums[mid] < target && target <= nums[right]) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
    }

    return -1;
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
- 旋转排序数组中，先判断哪半边有序，再判断 `target` 是否落在有序半边
