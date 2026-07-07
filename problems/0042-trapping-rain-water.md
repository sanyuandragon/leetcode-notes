# 42. 接雨水

## 题目信息

- 难度：困难
- 链接：https://leetcode.cn/problems/trapping-rain-water/
- 主知识点：双指针
- 相关知识点：数组、前缀最大值、单调栈
- 复习状态：`new`

## 一眼识别

看到这些信号时，应该想到本题方法：

- 每个位置能接多少水，取决于它左右两边最高柱子的较小值
- 某个位置的雨水量是 `min(leftMax, rightMax) - height[i]`
- 最高柱子可以作为全局分界点
- 最高柱左边只需要看左侧最高柱，右边只需要看右侧最高柱

## 解题思路

### 暴力思路

对每个位置 `i`，分别向左、向右扫描，找出左边最高柱 `leftMax` 和右边最高柱 `rightMax`，当前位置能接的水就是：

```cpp
min(leftMax, rightMax) - height[i]
```

如果结果大于 0，就累加到答案。这个做法容易理解，但每个位置都要向两边扫描，时间复杂度是 O(n^2)。

### 优化关键

你的写法先找到全局最高柱的位置 `index`。

这个最高柱有一个重要作用：它一定可以作为左右两边的可靠挡板。

- 在最高柱左边，右侧至少有全局最高柱兜底，所以当前位置能接多少水，只取决于它左边见过的最高柱 `leftHeight`
- 在最高柱右边，左侧至少有全局最高柱兜底，所以当前位置能接多少水，只取决于它右边见过的最高柱 `rightHeight`

因此可以：

- 从左往右扫到最高柱，维护 `leftHeight`
- 从右往左扫到最高柱，维护 `rightHeight`

遇到更高的柱子就更新挡板；遇到更矮的柱子，就可以接水。

### 最终做法

1. 如果数组为空，直接返回 `0`
2. 遍历一遍数组，找到最高柱子的下标 `index`
3. 从左往右扫描 `[0, index)`，维护左侧最高柱 `leftHeight`
4. 当前柱子低于 `leftHeight` 时，累加 `leftHeight - height[i]`
5. 从右往左扫描 `(index, n - 1]`，维护右侧最高柱 `rightHeight`
6. 当前柱子低于 `rightHeight` 时，累加 `rightHeight - height[j]`
7. 返回总水量 `ans`

## C++ 代码讲解

```cpp
class Solution {
public:
    int trap(vector<int>& height) {
        int ans = 0;
        int n = height.size();
        if (n == 0) {
            return 0;
        }

        // 先找到全局最高柱，它可以作为左右两边共同的最终挡板
        int maxHeight = height[0];
        int index = 0;
        for (int i = 0; i < n; ++i) {
            if (height[i] > maxHeight) {
                maxHeight = height[i];
                index = i;
            }
        }

        // 扫描最高柱左边：右侧有最高柱兜底，只需要维护左侧最高柱
        int leftHeight = 0;
        for (int i = 0; i < index; ++i) {
            if (height[i] > leftHeight) {
                leftHeight = height[i];
            }else {
                ans += leftHeight - height[i];
            }
        }

        // 扫描最高柱右边：左侧有最高柱兜底，只需要维护右侧最高柱
        int rightHeight = 0;
        for (int j = n - 1; j > index; --j) {
            if (height[j] > rightHeight) {
                rightHeight = height[j];
            }else {
                ans += rightHeight - height[j];
            }
        }

        return ans;
    }
};
```

关键点：

- `index`：全局最高柱的位置，用来把数组分成左右两段
- `leftHeight`：从左往右扫描时，当前见过的最高柱
- `rightHeight`：从右往左扫描时，当前见过的最高柱
- `ans += leftHeight - height[i]`：左半边当前位置能接的水
- `ans += rightHeight - height[j]`：右半边当前位置能接的水

循环不变量 / 状态含义：

- 左半边扫描时，`leftHeight` 始终是 `height[0...i]` 中的最大值
- 左半边任意位置的右侧一定存在全局最高柱，所以只要 `height[i] < leftHeight` 就能接水
- 右半边扫描时，`rightHeight` 始终是 `height[j...n-1]` 中的最大值
- 右半边任意位置的左侧一定存在全局最高柱，所以只要 `height[j] < rightHeight` 就能接水

边界处理：

- 空数组直接返回 `0`
- 如果数组长度小于 3，也自然接不到水，代码会返回 `0`
- 最高柱本身不用计算，它不会接水
- 如果有多个同样高度的最高柱，取第一个最高柱也可以；右侧扫描会处理另一根最高柱之前的区域

## 复杂度分析

- 时间复杂度：O(n)，找最高柱一遍，左右扫描总共一遍
- 空间复杂度：O(1)，只使用常数个变量

## 面试讲法

可以这样讲：

接雨水的核心是每个位置能接的水由左右两边最高柱子的较小值决定。我的做法先找到全局最高柱，把它作为分界点。最高柱左边的每个位置，右侧一定有这个最高柱作为挡板，所以只需要从左往右维护左侧最高柱；如果当前柱子比左侧最高柱低，就能接差值的水。最高柱右边同理，从右往左维护右侧最高柱。这样只需要线性扫描，空间是 O(1)。

可能追问：

- 为什么最高柱能当分界点：它是全局最高，左右两边都可以把它当作另一侧的足够高挡板
- 多个最高柱怎么办：任意选择一个最高柱作为分界点都不影响答案
- 和前后缀数组做法的区别：前后缀数组会显式保存每个位置左右最大值，空间 O(n)；这个做法借助最高柱把空间压到 O(1)
- 还有什么做法：经典左右双指针、单调栈也都可以

## 模板沉淀

- 是否沉淀模板：是
- 对应模板：[接雨水：最高柱分割扫描](../templates/cpp/two-pointers.md)
- 可复用点：先找到全局最大值作为分界点，再分别从两侧维护当前最高挡板

## 易错点

- 忘记处理空数组
- 左半边只扫到 `i < index`，右半边只扫到 `j > index`
- `leftHeight/rightHeight` 要先更新挡板，当前柱子更矮时才累加水量
- 不要把接水高度写成全局最高柱和当前柱子的差
- 严格理解：每个位置取左右最高柱较小值，不是取较大值

## 复盘卡片

- 一句话记忆：接雨水 = 最高柱切两边，左边看左挡板，右边看右挡板
- 下次先想：当前位置水量由 `min(leftMax, rightMax)` 决定
- 如果写错，优先检查：最高柱下标、左右扫描边界、挡板更新顺序
