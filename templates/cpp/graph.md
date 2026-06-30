# 图论

## 网格 BFS 连通块

适用：岛屿数量、网格区域搜索、四方向连通块计数。

```cpp
int countComponents(vector<vector<char>>& grid) {
    int m = grid.size();
    int n = grid[0].size();
    int ans = 0;

    int dx[4] = {-1, 1, 0, 0};
    int dy[4] = {0, 0, -1, 1};

    for (int i = 0; i < m; ++i) {
        for (int j = 0; j < n; ++j) {
            if (grid[i][j] != '1') {
                continue;
            }

            ++ans;
            queue<pair<int, int>> q;
            q.push({i, j});
            grid[i][j] = '0';

            while (!q.empty()) {
                auto [x, y] = q.front();
                q.pop();

                for (int k = 0; k < 4; ++k) {
                    int nx = x + dx[k];
                    int ny = y + dy[k];

                    if (nx < 0 || nx >= m || ny < 0 || ny >= n) {
                        continue;
                    }
                    if (grid[nx][ny] != '1') {
                        continue;
                    }

                    q.push({nx, ny});
                    grid[nx][ny] = '0';
                }
            }
        }
    }

    return ans;
}
```

## 网格 DFS

```cpp
void dfs(vector<vector<char>>& grid, int x, int y) {
    int m = grid.size();
    int n = grid[0].size();
    if (x < 0 || x >= m || y < 0 || y >= n || grid[x][y] != '1') {
        return;
    }

    grid[x][y] = '0';
    dfs(grid, x - 1, y);
    dfs(grid, x + 1, y);
    dfs(grid, x, y - 1);
    dfs(grid, x, y + 1);
}
```

## 易错点

- 发现新连通块时才给答案加一，BFS/DFS 内部只负责标记整块区域
- 入队或递归前就标记访问，避免重复入队或重复递归
- `x` 通常对应行，范围是 `[0, m)`；`y` 对应列，范围是 `[0, n)`
- 字符网格比较 `'1'` / `'0'`，整数网格比较 `1` / `0`

