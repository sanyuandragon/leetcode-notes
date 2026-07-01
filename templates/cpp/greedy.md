# 贪心

## 一次交易股票最大利润

适用：只能买卖一次，必须先买后卖，求最大利润。

```cpp
int maxProfit(vector<int>& prices) {
    int minPrice = prices[0];
    int ans = 0;

    for (int price : prices) {
        ans = max(ans, price - minPrice);
        minPrice = min(minPrice, price);
    }

    return ans;
}
```

## 维护前缀最优

适用：遍历到当前位置时，需要用它之前的某个最优值更新答案。

```cpp
int bestPrefix = initialValue;
int ans = initialAnswer;

for (int x : nums) {
    ans = max(ans, x - bestPrefix);
    bestPrefix = min(bestPrefix, x);
}
```

## 易错点

- 有时间顺序的题不能排序
- 先用当前值更新答案，再更新历史最优，常用于强调“当前作为卖出/结束位置”
- 如果允许当前元素同时作为买入和卖出，利润为 `0`，通常不影响最大利润
- 如果题目允许多次交易，模板会变成累加上涨段或股票 DP，不能直接套一次交易模板

