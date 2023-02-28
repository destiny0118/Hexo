---
title: leetcode934
tags: [BFS, 最短路径]
---

# [934. 最短的桥](https://leetcode-cn.com/problems/shortest-bridge/)

在给定的二维二进制数组 A 中，存在两座岛。（岛是由四面相连的 1 形成的一个最大组。）

现在，我们可以将 0 变为 1，以使两座岛连接起来，变成一座岛。

返回必须翻转的 0 的最小数目。（可以保证答案至少是 1 。）

![leetcode954-最短路径-广度优先遍历](https://gitee.com/destiny0118/picgo/raw/master/20210522111600.png)

 ```
 示例 1：
 
 输入：A = [[0,1],[1,0]]
 输出：1
 ```

```c++
#include <iostream>
#include <queue>
#include <vector>
using namespace std;
vector<int> direction = {-1, 0, 1, 0, -1};
void bfs(vector<vector<int>> &grid, queue<pair<int, int>> &points, int r,
         int c) {
    if (grid[r][c] == 2)
        return;
    if (grid[r][c] == 0) {
        grid[r][c] = 2;
        points.push({r, c});
        return;
    }
    grid[r][c] = 2;
    int x, y;
    for (int i = 0; i < 4; i++) {
        x = r + direction[i];
        y = c + direction[i + 1];
        if (x >= 0 && x < grid[0].size() && y >= 0 && y < grid[0].size())
            bfs(grid, points, x, y);
    }
}
int shortestBridge(vector<vector<int>> &grid) {
    queue<pair<int, int>> points;
    int m = grid.size(), n = grid[0].size();

    //寻找第一个岛屿
    bool flipped = false;
    for (int i = 0; i < m; i++) {
        if (flipped)
            break;
        for (int j = 0; j < n; j++) {
            if (grid[i][j] == 1) {
                //对第一个岛屿进行广度搜索
                bfs(grid, points, i, j);
                flipped = true;
                break;
            }
        }
    }
    //寻找第二个岛屿
    int cnt = 0;
    int x, y;
    while (!points.empty()) {
        cnt++;
        int n_points = points.size();
        while (n_points--) {
            auto [r, c] = points.front();
            points.pop();
            for (int k = 0; k < 4; k++) {
                x = r + direction[k];
                y = c + direction[k + 1];
                if (x >= 0 && x < grid.size() && y >= 0 && y < grid[0].size()) {
                    if (grid[x][y] == 2)
                        continue;
                    if (grid[x][y] == 1)
                        return cnt;
                    grid[x][y] = 2;
                    points.push({x, y});
                }
            }
        }
    }
    return 0;
}
int main() {
    vector<vector<int>> grid = {
        {1, 1, 0, 0}, {1, 0, 0, 0}, {1, 0, 0, 0}, {0, 0, 1, 1}};
    cout << shortestBridge(grid) << endl;
    for (auto &ele : grid) {
        for (auto &t : ele)
            cout << t << " ";
        cout << endl;
    }
    getchar();
}
```



