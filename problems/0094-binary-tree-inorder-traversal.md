# 94. 二叉树的中序遍历

## 题目信息

- 难度：简单
- 链接：https://leetcode.cn/problems/binary-tree-inorder-traversal/
- 主知识点：二叉树
- 相关知识点：DFS、栈、递归、迭代遍历
- 复习状态：`new`

## 一眼识别

看到这些信号时，应该想到本题方法：

- 输入是 `TreeNode* root`
- 要返回二叉树的中序遍历结果
- 中序遍历顺序是：左子树 -> 当前节点 -> 右子树
- 递归写法最直观，迭代写法用栈模拟递归过程

## 解题思路

### 解法 1：递归

中序遍历的定义本身就是递归结构：

1. 先遍历左子树
2. 访问当前节点
3. 再遍历右子树

空节点直接返回。递归写法代码短，也最适合先理解遍历顺序。

### 解法 2：迭代栈

递归的本质是调用栈。手动写栈时，需要模拟“不断走到最左边，再回退访问节点，再进入右子树”的过程。

维护：

- `cur`：当前正在尝试向左深入的节点
- `st`：保存还没访问、但左子树已经或正在处理的节点

过程：

- 只要 `cur` 不为空，就不断入栈并向左走
- 当 `cur` 为空时，说明左边走到底了，从栈顶弹出节点并访问
- 访问后转向它的右子树

## C++ 代码讲解

### 解法 1：递归

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    vector<int> ans;

    void inorder(TreeNode* root, vector<int>& ans) {
        if (root == nullptr) {
            return;
        }

        // 左
        inorder(root->left, ans);
        // 根
        ans.push_back(root->val);
        // 右
        inorder(root->right, ans);
    }

    vector<int> inorderTraversal(TreeNode* root) {
        inorder(root, ans);
        return ans;
    }
};
```

关键点：

- `inorder(root->left, ans)`：先处理左子树
- `ans.push_back(root->val)`：再访问当前节点
- `inorder(root->right, ans)`：最后处理右子树
- `root == nullptr`：递归出口

小提醒：

- 你的写法用成员变量 `ans` 保存结果，LeetCode 每个测试用例创建新对象时没问题
- 更通用的写法是把 `ans` 放在 `inorderTraversal` 里作为局部变量，避免同一个对象多次调用时残留旧结果

### 解法 2：迭代栈

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> result;
        stack<TreeNode*> st;
        TreeNode* cur = root;

        while (cur != nullptr || !st.empty()) {
            if (cur != nullptr) {
                // 一路向左，把沿途节点压栈，等待左子树处理完后再访问
                st.push(cur);
                cur = cur->left;
            }else {
                // 左边走到底后，弹出最近的节点访问
                cur = st.top();
                st.pop();
                result.push_back(cur->val);

                // 当前节点访问完后，转向右子树
                cur = cur->right;
            }
        }

        return result;
    }
};
```

关键点：

- `cur`：当前准备处理的节点
- `st`：模拟递归调用栈
- `cur != nullptr` 时持续向左走
- `cur == nullptr` 时弹栈访问节点
- 访问节点后转向右子树

循环不变量 / 状态含义：

- 栈中保存的是已经经过但还没访问的节点
- 当前节点的左子树处理完后，才能访问当前节点
- 每个节点都会经历一次入栈、一次出栈、一次访问

边界处理：

- 空树：`cur == nullptr` 且栈为空，循环不进入，返回空数组
- 单节点：入栈后左空，弹出访问，再转向右空，结束
- 迭代条件必须是 `cur != nullptr || !st.empty()`，只要还有当前节点或栈里还有待访问节点，就不能结束

## 复杂度分析

- 时间复杂度：O(n)，每个节点访问一次
- 空间复杂度：O(h)，其中 `h` 是树高；递归调用栈或显式栈最坏为 O(n)

## 面试讲法

可以这样讲：

中序遍历的顺序是左子树、当前节点、右子树。递归写法直接按照这个顺序写：空节点返回，先递归左子树，再记录当前节点值，再递归右子树。迭代写法用栈模拟递归：先沿着左孩子一路入栈，走到空后弹出栈顶访问，再转向右子树。这样每个节点只入栈出栈一次，时间复杂度 O(n)。

可能追问：

- 前序、中序、后序区别：前序是根左右，中序是左根右，后序是左右根
- 为什么迭代要一路向左：中序要求先访问最左侧节点
- 空树返回什么：返回空数组
- Morris 遍历能不能 O(1) 空间：可以，但会临时修改树结构，面试中一般先讲递归和栈

## 模板沉淀

- 是否沉淀模板：是
- 对应模板：[二叉树中序遍历](../templates/cpp/binary-tree.md)
- 可复用点：二叉树 DFS 的递归顺序，以及用栈模拟“先一路向左，再弹栈访问，再转右”的中序过程

## 易错点

- 中序遍历顺序是左、根、右
- 迭代写法中，访问节点后要转向右子树
- 循环条件要同时看 `cur` 和栈是否为空
- 递归写法如果用成员变量保存结果，要注意多次调用时可能残留旧数据
- 空树应该返回空数组

## 复盘卡片

- 一句话记忆：中序遍历 = 左根右；迭代版就是一路向左入栈，弹出访问后转右
- 下次先想：当前节点什么时候访问，左子树处理完再访问
- 如果写错，优先检查：访问顺序、迭代循环条件、弹栈后是否转向右子树
