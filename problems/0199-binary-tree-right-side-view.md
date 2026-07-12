# 199. 二叉树的右视图

## 题目信息

- 难度：中等
- 链接：https://leetcode.cn/problems/binary-tree-right-side-view/
- 主知识点：二叉树
- 相关知识点：BFS、队列、层序遍历
- 复习状态：`new`

## 一眼识别

看到这些信号时，应该想到本题方法：

- 输入是 `TreeNode* root`
- 题目要求“从右侧能看到的节点”
- 本质是每一层只取最右边的一个节点
- 需要按从上到下的顺序返回答案
- 二叉树按层处理天然对应 BFS 队列

## 解题思路

### 暴力思路

可以先做完整层序遍历，把每一层的所有节点值都保存下来，最后取每一层数组的最后一个值。这样能做，但会多保存不需要的节点值。

### 优化关键

右视图只关心每一层最右边的节点。BFS 按层遍历时，只要在每层开始前固定当前层节点数：

```cpp
int size = que.size();
```

然后处理这一层的 `size` 个节点。当 `i == size - 1` 时，当前节点就是这一层从左到右遍历到的最后一个节点，也就是右侧能看到的节点，把它加入答案即可。

### 最终做法

1. 如果 `root == nullptr`，直接返回空数组
2. 创建队列 `que`，把根节点入队
3. 当队列不为空时，先记录当前层节点数 `size`
4. 循环处理当前层的 `size` 个节点
5. 每次弹出队头节点，如果它是本层最后一个节点，就加入答案
6. 把非空左右孩子入队，留到下一层处理
7. 队列清空后返回答案

## C++ 代码讲解

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
    vector<int> rightSideView(TreeNode* root) {
        if (root == nullptr) return {};

        queue<TreeNode*> que;
        que.push(root);
        vector<int> ans;

        while (!que.empty()) {
            // 固定当前层节点数，避免下一层孩子入队后影响本层循环
            int size = que.size();

            for (int i = 0; i < size; ++i) {
                TreeNode* node = que.front();
                que.pop();

                // 从左到右遍历时，本层最后一个节点就是右视图节点
                if (i == size - 1) ans.push_back(node->val);

                if (node->left != nullptr) que.push(node->left);
                if (node->right != nullptr) que.push(node->right);
            }
        }

        return ans;
    }
};
```

关键点：

- `root == nullptr`：空树没有右视图，直接返回空数组
- `queue<TreeNode*> que`：BFS 队列，保存接下来要访问的节点
- `size = que.size()`：当前层节点数量，是分层的关键
- `i == size - 1`：当前节点是这一层最后弹出的节点，也就是最右节点
- 先入左孩子再入右孩子：保证本层访问顺序是从左到右，所以最后一个就是右侧可见节点

循环不变量 / 状态含义：

- 每次进入 `while` 时，队列中保存的是当前层所有节点，且顺序是从左到右
- `size` 固定后，本轮 `for` 只处理当前层节点
- 本轮中新入队的左右孩子都属于下一层
- 每轮结束时，答案中会新增当前层的右视图节点

边界处理：

- 空树直接返回 `{}`，避免把空指针放入队列
- 单节点树会在第一层满足 `i == size - 1`，返回根节点值
- 某一层缺少右孩子时，右视图不一定是右孩子，而是这一层最右侧存在的节点
- `size` 必须在每层开始时固定，不能在循环条件里动态使用 `que.size()`

## 复杂度分析

- 时间复杂度：O(n)，每个节点入队、出队各一次
- 空间复杂度：O(n)，最坏情况下队列保存一层的大量节点，答案最多保存树的高度个节点

## 面试讲法

可以这样讲：

右视图其实就是每一层最右边的节点。我用 BFS 做层序遍历，每一层开始时先记录队列当前长度 `size`，这个长度就是当前层节点数。然后只处理这 `size` 个节点，过程中把孩子节点加入队列，留给下一层处理。因为我按左孩子、右孩子的顺序入队，所以当前层从队列中弹出的顺序是从左到右，当 `i == size - 1` 时就是这一层最右边的节点，把它加入答案。空树直接返回空数组。

可能追问：

- 为什么每层最后一个节点就是右视图：因为 BFS 当前层按从左到右访问，右侧看到的是该层最右侧存在的节点
- 如果某层没有右孩子怎么办：仍然取该层最后一个存在的节点，可能来自左子树
- 能不能用 DFS：可以，优先访问右子树，第一次到达某个深度时记录节点值
- 为什么要固定 `size`：否则孩子入队会改变队列长度，当前层和下一层会混在一起

## 模板沉淀

- 是否沉淀模板：是
- 对应模板：[二叉树层序遍历 BFS](../templates/cpp/binary-tree.md)
- 可复用点：按层处理二叉树时，先固定当前层 `size`；只需要每层某个特定位置时，可以在 `for` 里用下标判断

## 易错点

- 忘记处理 `root == nullptr`
- 把“右视图”误解成一路沿着右孩子走，实际每一层都要看最右侧存在的节点
- `size` 不提前保存，导致下一层节点混入当前层
- 如果按左到右入队，就取 `i == size - 1`；如果按右到左入队，则要取 `i == 0`
- 孩子入队前要判空

## 复盘卡片

- 一句话记忆：右视图 = 层序遍历每层取最后一个节点
- 下次先想：这题不是只走右链，而是每层取最右侧存在的节点
- 如果写错，优先检查：空树、`size` 是否提前保存、取的是本层最后一个还是全局最后一个
