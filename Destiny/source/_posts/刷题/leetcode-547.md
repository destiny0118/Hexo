---
title: leetcode-547
---

```c++
#include <iostream>
#include <stack>
#include <vector>
using namespace std;
int findCircleNum(vector<vector<int>> &isConnected) {
    int n = isConnected.size(), cnt = 0;
    // n标识城市个数，visisted标识城市是否访问
    vector<bool> visisted(n, false);
    for (int i = 0; i < n; i++) {
        if (!visisted[i]) {
            stack<int> city;
            ++cnt;
            visisted[i] = true;
            city.push(i);
            while (!city.empty()) {
                int t = city.top();
                city.pop();
                for (int k = 0; k < n; k++) {
                    // cout << isConnected[t][k] << endl;
                    if (isConnected[t][k] == 1 && !visisted[k]) {
                        visisted[k] = true;
                        city.push(k);
                        // cout << "k=" << k << endl;
                    }
                }
            }
        }
    }
    return cnt;
}
int main() {
    // vector<vector<int>> isConnected = {{1, 0, 0}, {0, 1, 0}, {0, 0, 1}};
    vector<vector<int>> isConnected = {{1, 1, 0}, {1, 1, 0}, {0, 0, 1}};
    cout << findCircleNum(isConnected) << endl;
    return 0;
}
```

![image-20210518152509783](https://gitee.com/destiny0118/picgo/raw/master/20210518152516.png)