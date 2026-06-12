# 双指针

## 对撞指针

适用：有序数组、两端收缩、寻找一组满足条件的元素。

```cpp
int left = 0;
int right = (int)nums.size() - 1;

while (left < right) {
    int sum = nums[left] + nums[right];

    if (sum == target) {
        // handle answer
        ++left;
        --right;
    } else if (sum < target) {
        ++left;
    } else {
        --right;
    }
}
```

## 排序后固定一个数 + 双指针

适用：三数之和、固定前缀后用两端收缩寻找剩余元素。

```cpp
sort(nums.begin(), nums.end());
vector<vector<int>> ans;
int n = nums.size();

for (int i = 0; i < n; ++i) {
    if (i > 0 && nums[i] == nums[i - 1]) {
        continue;
    }

    int left = i + 1;
    int right = n - 1;
    while (left < right) {
        int sum = nums[i] + nums[left] + nums[right];

        if (sum == target) {
            ans.push_back({nums[i], nums[left], nums[right]});
            ++left;
            --right;

            while (left < right && nums[left] == nums[left - 1]) {
                ++left;
            }
            while (left < right && nums[right] == nums[right + 1]) {
                --right;
            }
        } else if (sum < target) {
            ++left;
        } else {
            --right;
        }
    }
}
```

## 快慢指针

适用：原地删除、链表环、去重。

```cpp
int slow = 0;
for (int fast = 0; fast < (int)nums.size(); ++fast) {
    if (/* nums[fast] should be kept */) {
        nums[slow++] = nums[fast];
    }
}
```

## 易错点

- 对撞指针常用 `left < right`
- 原地覆盖时，`slow` 通常表示下一个可写位置
- 三数之和命中答案后，先移动 `left/right`，再跳过和旧值重复的元素
