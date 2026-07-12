# 二叉树

## 层序遍历 BFS

适用：二叉树按层输出、求每层信息、最短层数等问题。

```cpp
vector<vector<int>> levelOrder(TreeNode* root) {
    vector<vector<int>> ans;
    if (root == nullptr) {
        return ans;
    }

    queue<TreeNode*> q;
    q.push(root);

    while (!q.empty()) {
        int size = q.size();
        vector<int> level;

        for (int i = 0; i < size; ++i) {
            TreeNode* node = q.front();
            q.pop();

            level.push_back(node->val);

            if (node->left != nullptr) {
                q.push(node->left);
            }
            if (node->right != nullptr) {
                q.push(node->right);
            }
        }

        ans.push_back(level);
    }

    return ans;
}
```

## 右视图 BFS

适用：二叉树右视图、每层取最右侧节点等问题。

```cpp
vector<int> rightSideView(TreeNode* root) {
    if (root == nullptr) {
        return {};
    }

    queue<TreeNode*> q;
    q.push(root);
    vector<int> ans;

    while (!q.empty()) {
        int size = q.size();

        for (int i = 0; i < size; ++i) {
            TreeNode* node = q.front();
            q.pop();

            if (i == size - 1) {
                ans.push_back(node->val);
            }

            if (node->left != nullptr) {
                q.push(node->left);
            }
            if (node->right != nullptr) {
                q.push(node->right);
            }
        }
    }

    return ans;
}
```

## DFS 递归遍历

```cpp
void preorder(TreeNode* root, vector<int>& ans) {
    if (root == nullptr) {
        return;
    }

    ans.push_back(root->val);
    preorder(root->left, ans);
    preorder(root->right, ans);
}
```

## 中序遍历

适用：二叉树左根右访问；二叉搜索树中序遍历会得到升序序列。

递归写法：

```cpp
void inorder(TreeNode* root, vector<int>& ans) {
    if (root == nullptr) {
        return;
    }

    inorder(root->left, ans);
    ans.push_back(root->val);
    inorder(root->right, ans);
}
```

迭代写法：

```cpp
vector<int> inorderTraversal(TreeNode* root) {
    vector<int> ans;
    stack<TreeNode*> st;
    TreeNode* cur = root;

    while (cur != nullptr || !st.empty()) {
        if (cur != nullptr) {
            st.push(cur);
            cur = cur->left;
        } else {
            cur = st.top();
            st.pop();
            ans.push_back(cur->val);
            cur = cur->right;
        }
    }

    return ans;
}
```

## 递归求高度

```cpp
int height(TreeNode* root) {
    if (root == nullptr) {
        return 0;
    }

    return max(height(root->left), height(root->right)) + 1;
}
```

## 二叉树最大路径和

适用：树上路径可以从任意节点开始和结束，要求最大路径和。

```cpp
class Solution {
public:
    int ans = INT_MIN;

    int dfs(TreeNode* node) {
        if (node == nullptr) {
            return 0;
        }

        int left = dfs(node->left);
        int right = dfs(node->right);

        ans = max(ans, left + right + node->val);
        return max(max(left, right) + node->val, 0);
    }

    int maxPathSum(TreeNode* root) {
        dfs(root);
        return ans;
    }
};
```

## 最近公共祖先

适用：普通二叉树中查找两个节点的最近公共祖先，且题目保证两个节点都存在。

```cpp
TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
    if (root == nullptr || root == p || root == q) {
        return root;
    }

    TreeNode* left = lowestCommonAncestor(root->left, p, q);
    TreeNode* right = lowestCommonAncestor(root->right, p, q);

    if (left != nullptr && right != nullptr) {
        return root;
    }

    return left != nullptr ? left : right;
}
```

## 易错点

- 层序遍历每层开始先保存 `size = q.size()`，不要让新入队的孩子影响当前层循环
- 空树要先返回，不要把 `nullptr` 推进队列
- 需要从右到左时，调整孩子入队顺序或输出顺序
- 右视图取的是每层最右侧存在的节点，不是只沿着右孩子一路向下
- DFS 递归先写空节点出口，再写当前节点和左右子树逻辑
- 中序遍历迭代版要先一路向左入栈，弹栈访问后再转向右子树
- 最大路径和里，返回给父节点的只能是单边贡献，更新全局答案时才能左右都用
- 最大路径和的 `ans` 要初始化为 `INT_MIN`，避免全负数树返回 0
- 最近公共祖先里，`root == p || root == q` 是递归出口，因为节点可以是自己的祖先
- 普通二叉树 LCA 不能用节点值大小判断方向，那是二叉搜索树的做法
