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

