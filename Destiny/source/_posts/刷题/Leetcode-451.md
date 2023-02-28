---
title: Leetcode-451
categories: 算法
---

# 时间超时

```c++
#include <algorithm>
#include <iostream>
#include <string>
#include <unordered_map>
#include <vector>
using namespace std;
string frequencySort(string s) {
    int len = s.size();
    unordered_map<char, int> char_cnt;
    int max_cnt = 0;
    for (int i = 0; i < len; ++i) {
        max_cnt = max(max_cnt, ++char_cnt[s[i]]);
    }
    vector<vector<char>> counts(max_cnt + 1);
    for (auto &p : char_cnt)
        counts[p.second].push_back(p.first);

    string ans = "";
    for (int i = max_cnt; i >= 1; --i) {
        // cout << max_cnt << counts[max_cnt][0] << endl;
        for (const char &ch : counts[i])
            for (int j = i; j > 0; --j) {

                ans = ans + ch;
            }

        // cout << "***" << ans << endl;
    }
    return ans;
}
int main() {
    string s = "cccaaa";
    cout << frequencySort(s) << endl;
    getchar();
}
```

# 优化

```c++
string frequencySort(string s) {
    unordered_map<char, int> char_cnt;
    for (int i = 0; i < s.size(); ++i) {
        ++char_cnt[s[i]];
    }
    vector<vector<char>> counts(s.size() + 1);
    for (auto &p : char_cnt)
        counts[p.second].push_back(p.first);

    string ans = "";
    for (int i = counts.size() - 1; i >= 1; --i) {
        if (counts[i].size() > 0) {
            for (const char &ch : counts[i])
                // for (int j = i; j > 0; --j) {
                ans.append(i, ch);
            // }
        }
    }
    return ans;
}
```

![image-20210514233352587](https://gitee.com/destiny0118/picgo/raw/master/20210514233357.png)

# 差别（在append那里）

> 稍微改一点效率竟然差别这么大

```c++
string frequencySort(string s) {
    unordered_map<char, int> char_cnt;
    for (int i = 0; i < s.size(); ++i) {
        ++char_cnt[s[i]];
    }
    vector<vector<char>> counts(s.size() + 1);
    for (auto &p : char_cnt)
        counts[p.second].push_back(p.first);

    string ans = "";
    for (int i = counts.size() - 1; i >= 1; --i) {
        if (counts[i].size() > 0) {
            for (const char &ch : counts[i])
                 for (int j = i; j > 0; --j) {
                ans.append(1 ch);
             }
        }
    }
    return ans;
}
```



![image-20210514233021252](https://gitee.com/destiny0118/picgo/raw/master/20210514233028.png)