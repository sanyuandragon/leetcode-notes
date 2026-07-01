# 46. 全排列

## 题目信息

- 难度：中等
- 链接：https://leetcode.cn/problems/permutations/
- 主知识点：回溯
- 相关知识点：递归、DFS、排列、状态恢复
- 复习状态：`new`

## 一眼识别

看到这些信号时，应该想到本题方法：

- 要返回所有可能结果，而不是只求一个最优值
- 每个排列要使用所有元素
- 数组中元素互不相同
- 每个位置都要从还没用过的元素里选择一个
- 需要“选择 -> 递归 -> 撤销选择”的回溯结构

## 解题思路

### 暴力思路

可以尝试手动嵌套多层循环枚举排列，但数组长度不固定，循环层数也不固定。这类“层数由输入规模决定”的枚举问题，更适合用递归回溯。

### 优化关键

用 `path` 表示当前已经构造出的排列前缀，用 `used[i]` 表示 `nums[i]` 是否已经在当前路径中使用过。

递归函数做的事是：

```text
在当前 path 后面继续选择一个还没用过的数
```

当 `path.size() == nums.size()` 时，说明已经构造出一个完整排列，把它加入答案。

每次选择一个数后，进入下一层递归；递归结束后，要撤销这次选择，让其他分支可以继续使用这个数。

### 最终做法

1. 准备答案数组 `ans`
2. 准备当前路径 `path`
3. 准备使用标记 `used`
4. 调用回溯函数
5. 如果 `path` 长度等于 `nums` 长度，收集答案
6. 否则枚举所有下标
7. 跳过已经使用过的元素
8. 选择当前元素，递归，撤销选择

## C++ 代码讲解

```cpp
class Solution {
public:
    vector<vector<int>> permute(vector<int>& nums) {
        vector<vector<int>> ans;
        vector<int> path;
        vector<bool> used(nums.size(), false);

        backtrack(nums, path, used, ans);

        return ans;
    }

private:
    void backtrack(const vector<int>& nums,
                   vector<int>& path,
                   vector<bool>& used,
                   vector<vector<int>>& ans) {
        if (path.size() == nums.size()) {
            ans.push_back(path);
            return;
        }

        for (int i = 0; i < nums.size(); ++i) {
            if (used[i]) {
                continue;
            }

            path.push_back(nums[i]);
            used[i] = true;

            backtrack(nums, path, used, ans);

            path.pop_back();
            used[i] = false;
        }
    }
};
```

关键点：

- `ans`：保存所有完整排列
- `path`：当前正在构造的排列
- `used`：标记每个下标对应的元素是否已经在当前排列里
- `path.size() == nums.size()`：递归终止条件，说明得到一个完整排列
- `used[i]`：避免同一个元素在同一个排列中被重复使用
- `path.push_back(nums[i])` / `used[i] = true`：做选择
- `path.pop_back()` / `used[i] = false`：撤销选择，恢复现场

循环不变量 / 状态含义：

- 进入 `backtrack` 时，`path` 中的元素都已经被标记为 `used = true`
- `path` 的长度表示当前正在填写排列的第几个位置
- 每一层递归只选择一个还没用过的元素放到当前位置
- 回溯返回后，`path` 和 `used` 必须恢复到进入该分支前的状态

边界处理：

- 题目保证 `nums.length >= 1`
- 题目保证元素互不相同，所以不需要去重逻辑
- `ans.push_back(path)` 必须拷贝当前路径，不能保存引用
- 撤销选择的顺序要和做选择对应：先弹出路径，再把 `used[i]` 复原

## 复杂度分析

- 时间复杂度：O(n * n!)。共有 `n!` 个排列，每个排列拷贝到答案需要 O(n)
- 空间复杂度：O(n)，递归栈、`path` 和 `used` 都是 O(n)，不计答案数组

## 面试讲法

可以这样讲：

全排列要枚举所有可能顺序，我用回溯来构造排列。`path` 表示当前已经选出的前缀，`used` 记录哪些元素已经被放进当前排列。每一层递归代表填写排列中的一个位置，我遍历所有还没使用过的元素，把它加入 `path`，标记为已使用，然后递归填写下一个位置。递归回来后撤销选择，让其他分支可以继续尝试。当 `path` 的长度等于 `nums` 的长度时，就得到一个完整排列，加入答案。

可能追问：

- 为什么需要 `used`：排列中每个元素只能用一次，需要记录当前路径里哪些元素已经被选
- 为什么递归后要撤销：否则状态会污染其他分支
- 如果 `nums` 有重复元素怎么办：需要先排序，并在同层跳过重复选择
- 能不能原地交换：可以，用交换当前位置和后续位置的方式生成排列，不需要 `used`

## 模板沉淀

- 是否沉淀模板：是
- 对应模板：[全排列回溯](../templates/cpp/backtracking.md)
- 可复用点：排列、组合、子集、棋盘搜索等枚举问题，都可以从“路径、选择、结束条件、撤销选择”四件事入手

## 易错点

- 忘记 `used[i] = false`，会导致后续分支无法使用该元素
- 忘记 `path.pop_back()`，会让路径越来越长
- 终止条件应该是 `path.size() == nums.size()`
- 题目元素互不相同，不需要写去重；有重复元素时才需要同层去重
- `ans.push_back(path)` 要放在终止条件里，而不是每一层都加入

## 复盘卡片

- 一句话记忆：全排列 = 每一层选一个没用过的数，递归后撤销选择
- 下次先想：`path` 存当前答案，`used` 防止重复使用元素
- 如果写错，优先检查：终止条件、做选择和撤销选择是否成对

