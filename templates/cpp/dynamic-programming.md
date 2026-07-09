# 动态规划

## 线性 DP

```cpp
vector<int> dp(n, 0);
dp[0] = baseValue;

for (int i = 1; i < n; ++i) {
    dp[i] = /* transition from previous states */;
}

return dp[n - 1];
```

## 以当前位置结尾的线性 DP

适用：连续子数组或连续子串的最值问题。核心是定义“以 `i` 结尾”的状态。

```cpp
int bestEndingHere = nums[0];
int answer = nums[0];

for (int i = 1; i < (int)nums.size(); ++i) {
    bestEndingHere = max(bestEndingHere + nums[i], nums[i]);
    answer = max(answer, bestEndingHere);
}

return answer;
```

如果像第 53 题那样从 `i = 0` 开始，也可以写成：

```cpp
int sum = 0;
int answer = nums[0];

for (int x : nums) {
    sum = max(sum + x, x);
    answer = max(answer, sum);
}

return answer;
```

## 二维 DP

```cpp
vector<vector<int>> dp(n, vector<int>(m, 0));

for (int i = 0; i < n; ++i) {
    for (int j = 0; j < m; ++j) {
        dp[i][j] = /* transition */;
    }
}
```

## 编辑距离二维 DP

适用：两个字符串之间允许插入、删除、替换，求最少操作次数。

```cpp
int minDistance(string word1, string word2) {
    int m = word1.size();
    int n = word2.size();
    vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));

    for (int i = 0; i <= m; ++i) {
        dp[i][0] = i;
    }
    for (int j = 0; j <= n; ++j) {
        dp[0][j] = j;
    }

    for (int i = 1; i <= m; ++i) {
        for (int j = 1; j <= n; ++j) {
            if (word1[i - 1] == word2[j - 1]) {
                dp[i][j] = dp[i - 1][j - 1];
            } else {
                dp[i][j] = min({dp[i - 1][j], dp[i][j - 1], dp[i - 1][j - 1]}) + 1;
            }
        }
    }

    return dp[m][n];
}
```

## 最长公共子序列二维 DP

适用：两个字符串求最长公共子序列长度。

```cpp
int longestCommonSubsequence(string text1, string text2) {
    int m = text1.size();
    int n = text2.size();
    vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));

    for (int i = 1; i <= m; ++i) {
        for (int j = 1; j <= n; ++j) {
            if (text1[i - 1] == text2[j - 1]) {
                dp[i][j] = dp[i - 1][j - 1] + 1;
            } else {
                dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
            }
        }
    }

    return dp[m][n];
}
```

## 最长递增子序列 DP

适用：求最长严格递增子序列长度，O(n^2) 写法清晰，适合先讲状态定义。

```cpp
int lengthOfLIS(vector<int>& nums) {
    int n = nums.size();
    vector<int> dp(n, 1);
    int ans = 1;

    for (int i = 0; i < n; ++i) {
        for (int j = 0; j < i; ++j) {
            if (nums[j] < nums[i]) {
                dp[i] = max(dp[i], dp[j] + 1);
            }
        }
        ans = max(ans, dp[i]);
    }

    return ans;
}
```

## 面试检查顺序

1. `dp` 的含义是什么
2. 初始状态是什么
3. 状态从哪里转移过来
4. 遍历顺序为什么正确
5. 答案在哪里

## 易错点

- 不要一上来写转移式，先定义状态
- 空间优化时，倒序还是正序由依赖关系决定
- 最大子数组和要把答案初始化为数组元素，不能初始化为 `0`
- 编辑距离要初始化空前缀，`dp[i][0] = i`，`dp[0][j] = j`
- LCS 中字符相同取左上加一，字符不同取上方和左方最大值
- LIS 的 DP 答案是所有 `dp[i]` 的最大值，不一定是 `dp[n - 1]`
