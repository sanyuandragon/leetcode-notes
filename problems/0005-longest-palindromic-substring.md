# 5. 最长回文子串

## 题目信息

- 难度：中等
- 链接：https://leetcode.cn/problems/longest-palindromic-substring/
- 主知识点：字符串
- 相关知识点：中心扩展、双指针、动态规划
- 复习状态：`new`

## 一眼识别

看到这些信号时，应该想到本题方法：

- 要找的是连续子串，不是子序列
- 子串需要满足回文性质，也就是左右对称
- 字符串长度最大 `1000`，O(n^2) 可以接受
- 回文串有两种中心：单个字符中心和两个字符之间的中心

## 解题思路

### 暴力思路

最直接的做法是枚举所有子串，再判断每个子串是否是回文。子串有 O(n^2) 个，每次判断最坏 O(n)，总复杂度是 O(n^3)，不够理想。

### 优化关键

回文串天然围绕中心对称。与其枚举左右边界，不如枚举回文中心，然后从中心向两边扩展。

每个位置都可能成为两类中心：

- 奇数长度回文：中心是一个字符，例如 `"aba"` 的中心是 `b`
- 偶数长度回文：中心是两个字符之间，例如 `"bb"` 的中心是两个 `b`

所以对每个 `center`，分别扩展：

```cpp
expand(s, center, center);     // 奇数长度
expand(s, center, center + 1); // 偶数长度
```

扩展停止时，`left` 和 `right` 已经越过了合法回文边界，所以真实长度是：

```cpp
right - left - 1
```

真实起点是：

```cpp
left + 1
```

### 最终做法

1. 初始化最长回文起点 `start = 0`
2. 初始化最长回文长度 `maxLen = 1`
3. 枚举每个位置作为中心
4. 分别尝试奇数中心和偶数中心扩展
5. 每次扩展结束后，如果长度更大，就更新 `start` 和 `maxLen`
6. 返回 `s.substr(start, maxLen)`

## C++ 代码讲解

```cpp
class Solution {
public:
    string longestPalindrome(string s) {
        int n = s.size();
        int start = 0;  // 最长回文串起点
        int maxLen = 1; // 最长回文串长度

        for (int center = 0; center < n; center++) {
            expend(s, center, center, start, maxLen);
            expend(s, center, center + 1, start, maxLen);
        }

        return s.substr(start, maxLen);
    }

private:
    void expend(string& s, int left, int right, int& start, int& maxLen) {
        while (left >= 0 && right < s.size() && s[left] == s[right]) {
            --left;
            ++right;
        }

        int len = right - left - 1;
        if (len > maxLen) {
            maxLen = len;
            start = left + 1;
        }
    }
};
```

关键点：

- `start`：当前最长回文子串的起始下标
- `maxLen`：当前最长回文子串长度
- `center`：枚举回文中心
- `expend(s, center, center, ...)`：处理奇数长度回文
- `expend(s, center, center + 1, ...)`：处理偶数长度回文
- `left`、`right`：从中心向两边扩展的双指针
- `len = right - left - 1`：扩展结束时左右指针已经多走一步，所以要减去越界的两端
- `start = left + 1`：真实回文子串从扩展失败后的下一位开始

循环不变量 / 状态含义：

- 外层遍历到某个 `center` 时，会检查所有以该位置相关的奇偶回文中心
- `start` 和 `maxLen` 始终记录目前见过的最长回文子串
- `expand` 结束后，`s[left + 1 ... right - 1]` 是本次中心能扩出的最长回文串

边界处理：

- `s.length >= 1`，所以 `maxLen = 1` 和 `s.substr(start, maxLen)` 是安全的
- 偶数中心 `center + 1` 可能等于 `n`，但 `while` 条件会先检查 `right < s.size()`
- 扩展时必须先判断边界，再访问 `s[left]` 和 `s[right]`
- 你的函数名写成了 `expend`，代码能通过；更常见的英文命名是 `expand`

## 复杂度分析

- 时间复杂度：O(n^2)。共有 O(n) 个中心，每次最坏向两边扩展 O(n)
- 空间复杂度：O(1)。只维护下标和长度，不计返回字符串

## 面试讲法

可以这样讲：

回文串的特点是从中心向两边对称，所以我不枚举所有左右边界，而是枚举回文中心。每个字符都可能作为奇数长度回文的中心，每两个相邻字符之间都可能作为偶数长度回文的中心。对每个中心向两边扩展，直到越界或左右字符不相等。扩展结束后，根据 `right - left - 1` 得到本次回文长度，如果超过当前答案，就更新起点和长度。最后用 `substr` 返回最长回文子串。

可能追问：

- 为什么要处理两种中心：因为回文长度可能是奇数，也可能是偶数
- 为什么长度是 `right - left - 1`：停止时 `left` 和 `right` 已经指向回文外侧
- 如果有多个答案怎么办：题目允许返回任意一个，代码保留第一次遇到的最长值
- 能不能用 DP：可以，`dp[i][j]` 表示 `s[i...j]` 是否回文，时间 O(n^2)，空间 O(n^2)
- 有没有更优算法：Manacher 算法可以做到 O(n)，但实现复杂，面试中中心扩展通常更稳

## 模板沉淀

- 是否沉淀模板：是
- 对应模板：[中心扩展求回文串](../templates/cpp/string.md)
- 可复用点：回文子串类问题常先枚举中心，再用左右指针向外扩展

## 易错点

- 只扩展 `center, center` 会漏掉偶数长度回文，例如 `"cbbd"` 的 `"bb"`
- 扩展结束后，`left` 和 `right` 已经越过合法边界，长度不是 `right - left + 1`
- `start` 应该更新为 `left + 1`，不是 `left`
- `maxLen` 初始化为 `1`，因为单个字符一定是回文串
- `right < s.size()` 要在访问 `s[right]` 之前判断

## 复盘卡片

- 一句话记忆：最长回文子串 = 枚举中心，奇偶都扩，扩完用 `right - left - 1` 算长度
- 下次先想：回文长度分奇偶，必须检查两个中心
- 如果写错，优先检查：偶数中心是否处理、长度公式、`start = left + 1`

