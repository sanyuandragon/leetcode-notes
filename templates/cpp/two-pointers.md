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

## 接雨水：最高柱分割扫描

适用：计算柱状图能接的雨水。先找全局最高柱，再从左右两边分别维护当前最高挡板。

```cpp
int trap(vector<int>& height) {
    int n = height.size();
    if (n == 0) {
        return 0;
    }

    int ans = 0;
    int maxHeight = height[0];
    int index = 0;
    for (int i = 0; i < n; ++i) {
        if (height[i] > maxHeight) {
            maxHeight = height[i];
            index = i;
        }
    }

    int leftHeight = 0;
    for (int i = 0; i < index; ++i) {
        if (height[i] > leftHeight) {
            leftHeight = height[i];
        } else {
            ans += leftHeight - height[i];
        }
    }

    int rightHeight = 0;
    for (int i = n - 1; i > index; --i) {
        if (height[i] > rightHeight) {
            rightHeight = height[i];
        } else {
            ans += rightHeight - height[i];
        }
    }

    return ans;
}
```

## 易错点

- 对撞指针常用 `left < right`
- 原地覆盖时，`slow` 通常表示下一个可写位置
- 三数之和命中答案后，先移动 `left/right`，再跳过和旧值重复的元素
- 接雨水中左右扫描不要越过最高柱；最高柱本身不接水
- 当前位置能接水的高度由左右最高挡板的较小值决定
