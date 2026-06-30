# 字符串

## 中心扩展求最长回文子串

适用：最长回文子串、统计回文子串等回文子串问题。

```cpp
string longestPalindrome(string s) {
    int n = s.size();
    int start = 0;
    int maxLen = 1;

    auto expand = [&](int left, int right) {
        while (left >= 0 && right < n && s[left] == s[right]) {
            --left;
            ++right;
        }

        int len = right - left - 1;
        if (len > maxLen) {
            maxLen = len;
            start = left + 1;
        }
    };

    for (int center = 0; center < n; ++center) {
        expand(center, center);
        expand(center, center + 1);
    }

    return s.substr(start, maxLen);
}
```

## 判断回文

适用：检查整个字符串或某个区间是否为回文。

```cpp
bool isPalindrome(const string& s, int left, int right) {
    while (left < right) {
        if (s[left] != s[right]) {
            return false;
        }
        ++left;
        --right;
    }

    return true;
}
```

## 统计回文子串

```cpp
int countSubstrings(string s) {
    int n = s.size();
    int ans = 0;

    auto expand = [&](int left, int right) {
        while (left >= 0 && right < n && s[left] == s[right]) {
            ++ans;
            --left;
            ++right;
        }
    };

    for (int center = 0; center < n; ++center) {
        expand(center, center);
        expand(center, center + 1);
    }

    return ans;
}
```

## 易错点

- 回文中心要分奇数中心 `center, center` 和偶数中心 `center, center + 1`
- 扩展结束后，真实回文区间是 `[left + 1, right - 1]`
- 最长回文长度是 `right - left - 1`
- 返回子串时使用 `s.substr(start, maxLen)`，第二个参数是长度，不是结束下标

