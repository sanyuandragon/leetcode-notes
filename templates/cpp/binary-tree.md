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

## 递归求高度

```cpp
int height(TreeNode* root) {
    if (root == nullptr) {
        return 0;
    }

    return max(height(root->left), height(root->right)) + 1;
}
```

## 易错点

- 层序遍历每层开始先保存 `size = q.size()`，不要让新入队的孩子影响当前层循环
- 空树要先返回，不要把 `nullptr` 推进队列
- 需要从右到左时，调整孩子入队顺序或输出顺序
- DFS 递归先写空节点出口，再写当前节点和左右子树逻辑

