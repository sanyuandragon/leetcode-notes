# 模拟

## 方向模拟

适用：机器人移动、网格行走、转向、判断路径状态。

```cpp
int dir = 0; // 0: 北, 1: 东, 2: 南, 3: 西
int dx[4] = {0, 1, 0, -1};
int dy[4] = {1, 0, -1, 0};

int x = 0;
int y = 0;

for (char c : instructions) {
    if (c == 'G') {
        x += dx[dir];
        y += dy[dir];
    } else if (c == 'L') {
        dir = (dir + 3) % 4;
    } else if (c == 'R') {
        dir = (dir + 1) % 4;
    }
}
```

## 重复执行的周期判断

适用：一段指令会无限重复执行，题目问是否有界、是否回到某种状态。

```cpp
bool boundedAfterRepeat(string instructions) {
    int dir = 0;
    int dx[4] = {0, 1, 0, -1};
    int dy[4] = {1, 0, -1, 0};
    int x = 0;
    int y = 0;

    for (char c : instructions) {
        if (c == 'G') {
            x += dx[dir];
            y += dy[dir];
        } else if (c == 'L') {
            dir = (dir + 3) % 4;
        } else {
            dir = (dir + 1) % 4;
        }
    }

    return (x == 0 && y == 0) || dir != 0;
}
```

## 易错点

- `dir` 的编号顺序要和 `dx/dy` 完全对应
- 左转不要直接写 `(dir - 1) % 4`，C++ 负数取模结果可能还是负数
- 无限重复类问题优先看“一轮结束后的状态”，不要盲目模拟很多轮
- 判断有界时，除了回到原点，还要考虑方向是否改变
