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

