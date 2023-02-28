---
title: Leetcode695
---

# [695. 岛屿的最大面积](https://leetcode-cn.com/problems/max-area-of-island/)

给定一个包含了一些 0 和 1 的非空二维数组 grid 。

一个 岛屿 是由一些相邻的 1 (代表土地) 构成的组合，这里的「相邻」要求两个 1 必须在水平或者竖直方向上相邻。你可以假设 grid 的四个边缘都被 0（代表水）包围着。

找到给定的二维数组中最大的岛屿面积。(如果没有岛屿，则返回面积为 0 。)

``` 
示例 1:

[[0,0,1,0,0,0,0,1,0,0,0,0,0],
 [0,0,0,0,0,0,0,1,1,1,0,0,0],
 [0,1,1,0,1,0,0,0,0,0,0,0,0],
 [0,1,0,0,1,1,0,0,1,0,1,0,0],
 [0,1,0,0,1,1,0,0,1,1,1,0,0],
 [0,0,0,0,0,0,0,0,0,0,1,0,0],
 [0,0,0,0,0,0,0,1,1,1,0,0,0],
 [0,0,0,0,0,0,0,1,1,0,0,0,0]]
对于上面这个给定矩阵应返回 6。注意答案不应该是 11 ，因为岛屿只能包含水平或垂直的四个方向的 1 。
```

## tip1

```c++
#include <iostream>
#include <stack>
#include <vector>
using namespace std;
int maxAreaOfIsland(vector<vector<int>> &grid) {
    int m = grid.size(), n = m ? grid[0].size() : 0;
    int area = 0, max_area = 0;
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (grid[i][j] == 1) {
                area = 1;
                grid[i][j] = 0;
                stack<pair<int, int>> land;
                land.push({i, j});
                while (!land.empty()) {
                    //弹出栈顶元素
                    auto [row, coloumn] = land.top();
                    land.pop();

                    //查看元素四个方向

                    //下方
                    if (row + 1 < m && grid[row + 1][coloumn] == 1) {
                        grid[row + 1][coloumn] = 0;
                        area++;
                        land.push({row + 1, coloumn});
                    }
                    //上方
                    if (row - 1 >= 0 && grid[row - 1][coloumn] == 1) {
                        grid[row - 1][coloumn] = 0;
                        area++;
                        land.push({row - 1, coloumn});
                    }
                    //右方
                    if (coloumn + 1 < n && grid[row][coloumn + 1] == 1) {
                        grid[row][coloumn + 1] = 0;
                        area++;
                        land.push({row, coloumn + 1});
                    }
                    //左方
                    if (coloumn - 1 >= 0 && grid[row][coloumn - 1] == 1) {
                        grid[row][coloumn - 1] = 0;
                        area++;
                        land.push({row, coloumn - 1});
                    }
                }
            }
            max_area = max(max_area, area);
        }
    }
    return max_area;
}

int main() {
    vector<vector<int>> grid = {{0, 0, 1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0},
                                {0, 0, 0, 0, 0, 0, 0, 1, 1, 1, 0, 0, 0},
                                {0, 1, 1, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0},
                                {0, 1, 0, 0, 1, 1, 0, 0, 1, 0, 1, 0, 0},
                                {0, 1, 0, 0, 1, 1, 0, 0, 1, 1, 1, 0, 0},
                                {0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0},
                                {0, 0, 0, 0, 0, 0, 0, 1, 1, 1, 0, 0, 0},
                                {0, 0, 0, 0, 0, 0, 0, 1, 1, 0, 0, 0, 0}};
    cout << maxAreaOfIsland(grid) << endl;
    getchar();
}
```

## tip2

优化？？？