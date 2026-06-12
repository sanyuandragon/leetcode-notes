# 单调栈

## 下一个更大元素

```cpp
vector<int> nextGreater(vector<int>& nums) {
    int n = nums.size();
    vector<int> ans(n, -1);
    stack<int> st;

    for (int i = 0; i < n; ++i) {
        while (!st.empty() && nums[i] > nums[st.top()]) {
            ans[st.top()] = nums[i];
            st.pop();
        }
        st.push(i);
    }

    return ans;
}
```

## 维护递增栈

栈中下标对应的值保持递增，当前元素更小时弹栈。

```cpp
while (!st.empty() && nums[i] < nums[st.top()]) {
    st.pop();
}
st.push(i);
```

## 易错点

- 栈里优先存下标，方便计算距离和回填答案
- 相等元素是否弹出，要根据题目是否允许重复贡献决定

