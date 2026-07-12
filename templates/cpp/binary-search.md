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

## 两个有序数组中位数

适用：两个升序数组，要求 O(log(m+n)) 找合并后的中位数。

```cpp
double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
    if (nums1.size() > nums2.size()) {
        return findMedianSortedArrays(nums2, nums1);
    }

    int m = nums1.size();
    int n = nums2.size();
    int left = 0;
    int right = m;

    while (left <= right) {
        int cut1 = left + (right - left) / 2;
        int cut2 = (m + n + 1) / 2 - cut1;

        int left1 = cut1 == 0 ? INT_MIN : nums1[cut1 - 1];
        int right1 = cut1 == m ? INT_MAX : nums1[cut1];
        int left2 = cut2 == 0 ? INT_MIN : nums2[cut2 - 1];
        int right2 = cut2 == n ? INT_MAX : nums2[cut2];

        if (left1 <= right2 && left2 <= right1) {
            if ((m + n) % 2 == 1) {
                return max(left1, left2);
            }

            return (static_cast<double>(max(left1, left2)) + min(right1, right2)) / 2.0;
        }

        if (left1 > right2) {
            right = cut1 - 1;
        } else {
            left = cut1 + 1;
        }
    }

    return 0.0;
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

## LIS 的 tails 二分优化

适用：最长严格递增子序列长度。`tails[i]` 表示长度为 `i + 1` 的递增子序列的最小可能结尾值。

```cpp
int lengthOfLIS(vector<int>& nums) {
    vector<int> tails;

    for (int x : nums) {
        auto it = lower_bound(tails.begin(), tails.end(), x);
        if (it == tails.end()) {
            tails.push_back(x);
        } else {
            *it = x;
        }
    }

    return tails.size();
}
```

## 易错点

- `check(mid)` 必须单调
- `right` 是否能取到答案要在初始化时确认
- 每次循环都要缩小区间
- 两个有序数组中位数要先保证第一个数组更短，并用哨兵处理切分到边界的情况
- 旋转排序数组中，先判断哪半边有序，再判断 `target` 是否落在有序半边
- 严格递增 LIS 用 `lower_bound` 找第一个 `>= x` 的位置，相等时替换而不是追加
