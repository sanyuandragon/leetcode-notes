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
