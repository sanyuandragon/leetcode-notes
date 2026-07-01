# 滑动窗口

## 最长合法窗口

```cpp
int left = 0;
int ans = 0;

for (int right = 0; right < (int)s.size(); ++right) {
    // add s[right]

    while (/* window is invalid */) {
        // remove s[left]
        ++left;
    }

ans = max(ans, right - left + 1);
}
```

## 无重复字符最长窗口

适用：最长无重复子串、窗口内元素不能重复。

```cpp
int lengthOfLongestSubstring(string s) {
    unordered_set<char> window;
    int right = -1;
    int ans = 0;

    for (int left = 0; left < (int)s.size(); ++left) {
        if (left != 0) {
            window.erase(s[left - 1]);
        }

        while (right + 1 < (int)s.size() && !window.count(s[right + 1])) {
            window.insert(s[right + 1]);
            ++right;
        }

        ans = max(ans, right - left + 1);
    }

    return ans;
}
```

## 最短合法窗口

```cpp
int left = 0;
int ans = INT_MAX;

for (int right = 0; right < (int)s.size(); ++right) {
    // add s[right]

    while (/* window is valid */) {
        ans = min(ans, right - left + 1);
        // remove s[left]
        ++left;
    }
}
```

## 易错点

- 最长窗口通常在收缩到合法后更新答案
- 最短窗口通常在合法时不断尝试收缩
- 左端移出窗口时，计数和状态必须同步更新
- 无重复窗口中，左端右移时要删除离开的字符
