# 栈

## 括号匹配

适用：有效括号、标签匹配、嵌套符号合法性判断。

```cpp
bool isValid(string s) {
    stack<char> st;

    for (char c : s) {
        if (c == '(') {
            st.push(')');
        } else if (c == '[') {
            st.push(']');
        } else if (c == '{') {
            st.push('}');
        } else {
            if (st.empty() || st.top() != c) {
                return false;
            }
            st.pop();
        }
    }

    return st.empty();
}
```

## 压左括号写法

```cpp
bool match(char left, char right) {
    return (left == '(' && right == ')') ||
           (left == '[' && right == ']') ||
           (left == '{' && right == '}');
}

bool isValid(string s) {
    stack<char> st;

    for (char c : s) {
        if (c == '(' || c == '[' || c == '{') {
            st.push(c);
        } else {
            if (st.empty() || !match(st.top(), c)) {
                return false;
            }
            st.pop();
        }
    }

    return st.empty();
}
```

## 易错点

- 遇到右括号时，先检查 `st.empty()`，再访问 `st.top()`
- 遍历结束后必须返回 `st.empty()`
- 压期待右括号的写法更省映射判断，但要确保映射正确
- 只比较括号数量不够，`"([)]"` 数量正确但顺序错误

