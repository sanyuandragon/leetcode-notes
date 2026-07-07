# 300. 最长递增子序列

## 题目信息

- 难度：中等
- 链接：https://leetcode.cn/problems/longest-increasing-subsequence/
- 主知识点：动态规划
- 相关知识点：二分查找、贪心、子序列
- 复习状态：`new`

## 一眼识别

看到这些信号时，应该想到本题方法：

- 要求最长严格递增子序列
- 子序列不要求连续，但必须保持原数组相对顺序
- `nums[j] < nums[i]` 才能把 `nums[i]` 接到 `nums[j]` 后面
- 先用 O(n^2) DP 理解状态，再用 `tails + lower_bound` 优化到 O(n log n)

## 解题思路

### 解法 1：DP

定义：

```text
dp[i] = 以 nums[i] 结尾的最长严格递增子序列长度
```

如果 `j < i` 且 `nums[j] < nums[i]`，那么可以把 `nums[i]` 接到以 `nums[j]` 结尾的递增子序列后面：

```cpp
dp[i] = max(dp[i], dp[j] + 1);
```

因为最终的最长递增子序列不一定以最后一个元素结尾，所以需要用 `ans` 维护所有 `dp[i]` 的最大值。

### 解法 2：贪心 + 二分

维护一个数组 `tails`：

```text
tails[len - 1] = 长度为 len 的递增子序列中，最小可能结尾值
```

`tails` 不是实际的某一个子序列，但它的长度等于当前能找到的最长递增子序列长度。

遍历每个数 `x`：

- 如果 `x` 比所有尾巴都大，就接在最后，LIS 长度加一
- 否则，用 `x` 替换第一个 `>= x` 的尾巴，让同长度子序列的结尾更小

为什么替换是安全的：

- 同样长度的递增子序列，结尾越小，后面越容易接上更大的数
- 替换不会让已有长度变短，只会让未来更有机会延长

这里是严格递增，所以用 `lower_bound` 找第一个 `>= x` 的位置。遇到相等元素时替换，而不是追加。

## C++ 代码讲解

### 解法 1：O(n^2) DP

```cpp
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int n = nums.size();
        vector<int> dp(n, 1);
        int ans = 1;

        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < i; ++j) {
                if (nums[j] < nums[i]) {
                    // nums[i] 可以接在以 nums[j] 结尾的递增子序列后面
                    dp[i] = max(dp[i], dp[j] + 1);
                }
            }
            // 最长递增子序列不一定以最后一个元素结尾，所以每轮都更新答案
            ans = max(ans, dp[i]);
        }

        return ans;
    }
};
```

关键点：

- `dp[i]`：以 `nums[i]` 结尾的最长递增子序列长度
- `vector<int> dp(n, 1)`：每个元素自己都能形成长度为 1 的子序列
- `j < i`：保证子序列保持原数组顺序
- `nums[j] < nums[i]`：严格递增，不能写成 `<=`
- `ans`：所有 `dp[i]` 的最大值

循环不变量 / 状态含义：

- 进入外层第 `i` 轮时，`dp[0...i-1]` 已经计算完成
- 内层枚举所有能接在 `nums[i]` 前面的元素
- 外层第 `i` 轮结束后，`dp[i]` 就是以 `nums[i]` 结尾的最优长度

### 解法 2：O(n log n) 贪心 + 二分

```cpp
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        vector<int> tails;

        for (int x : nums) {
            auto it = lower_bound(tails.begin(), tails.end(), x);
            if (it == tails.end()) {
                // x 比所有尾巴都大，可以延长当前最长递增子序列
                tails.push_back(x);
            }else {
                // 用更小的 x 替换同长度子序列的结尾，给后续元素留下更多空间
                *it = x;
            }
        }

        return tails.size();
    }
};
```

关键点：

- `tails`：每个长度对应的最小结尾值
- `lower_bound(tails.begin(), tails.end(), x)`：找第一个 `>= x` 的位置
- `it == tails.end()`：`x` 可以接在所有已有尾巴后面，长度增加
- `*it = x`：长度不变，但把某个长度的结尾变小
- `tails.size()`：当前最长递增子序列长度

状态含义：

- `tails` 始终单调递增
- `tails[i]` 表示长度为 `i + 1` 的递增子序列的最小可能结尾值
- `tails` 不一定是原数组中的真实子序列，但长度一定是 LIS 长度

边界处理：

- 题目保证 `nums.length >= 1`，所以 DP 解法中 `ans = 1` 安全
- 严格递增用 `lower_bound`；如果是不下降子序列，通常要改成 `upper_bound`
- 重复数字不能延长严格递增子序列，比如 `[7,7,7]` 结果是 1
- `tails.size()` 类型是 `size_t`，返回 `int` 时 LeetCode 会自动转换；想严谨可以写 `(int)tails.size()`

## 复杂度分析

- 解法 1 时间复杂度：O(n^2)，两层循环枚举前驱
- 解法 1 空间复杂度：O(n)，保存 `dp`
- 解法 2 时间复杂度：O(n log n)，每个元素做一次二分
- 解法 2 空间复杂度：O(n)，最坏情况下 `tails` 长度为 `n`

## 面试讲法

可以这样讲：

我会先给出 DP 解法。定义 `dp[i]` 为以 `nums[i]` 结尾的最长递增子序列长度。对于每个 `i`，枚举前面的 `j`，如果 `nums[j] < nums[i]`，就可以把 `nums[i]` 接到 `j` 后面，用 `dp[j] + 1` 更新 `dp[i]`。最后答案是所有 `dp[i]` 的最大值。

如果面试官要求优化，我再讲 `tails`。`tails[len - 1]` 表示长度为 `len` 的递增子序列中最小可能结尾值。遍历数字 `x` 时，用二分找到第一个大于等于 `x` 的尾巴并替换；如果找不到，就把 `x` 追加到末尾。这样每次都让相同长度的结尾尽量小，后面更容易继续接数字，最终 `tails` 的长度就是答案。

可能追问：

- 为什么 `tails` 可以替换：替换只让同长度子序列结尾更小，不会损失已获得的长度
- `tails` 是不是一个真实子序列：不一定，它只是每个长度的最优尾巴表
- 为什么严格递增用 `lower_bound`：遇到相等元素要替换，不能追加
- 如果求最长不下降子序列怎么办：把二分位置改成第一个 `> x`，也就是 `upper_bound`

## 模板沉淀

- 是否沉淀模板：是
- 对应模板：[最长递增子序列 DP](../templates/cpp/dynamic-programming.md)、[LIS 的 tails 二分优化](../templates/cpp/binary-search.md)
- 可复用点：子序列最值可以先用“以 i 结尾”的 DP 建模；严格递增的长度优化可以用 `tails + lower_bound`

## 易错点

- 子序列不要求连续，子数组才要求连续
- 严格递增必须是 `nums[j] < nums[i]`
- DP 的答案不是固定 `dp[n - 1]`，而是所有 `dp[i]` 的最大值
- `tails` 不是实际 LIS，只能用它的长度作为答案
- 有重复元素时，`lower_bound` 会替换第一个相等元素，不能追加
- `lower_bound` 要包含头文件 `<algorithm>`

## 复盘卡片

- 一句话记忆：LIS 先想 `dp[i] = 以 i 结尾的最长长度`，再用 `tails` 维护每个长度的最小尾巴
- 下次先想：严格递增用 `<` 和 `lower_bound`
- 如果写错，优先检查：是否把子序列当子数组、答案是否取全局最大、`tails` 是否用第一个 `>= x` 替换
