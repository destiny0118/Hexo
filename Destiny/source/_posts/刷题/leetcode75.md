---
title: leetcode75
tags: 桶排序
---

给定一个包含红色、白色和蓝色，一共 n 个元素的数组，原地对它们进行排序，使得相同颜色的元素相邻，并按照红色、白色、蓝色顺序排列。

此题中，我们使用整数 0、 1 和 2 分别表示红色、白色和蓝色。

``` 
示例 1：
输入：nums = [2,0,2,1,1,0]
输出：[0,0,1,1,2,2]

示例 2：
输入：nums = [2,0,1]
输出：[0,1,2]
```
# tip1

```c++
#include <iostream>
#include <unordered_map>
#include <vector>
using namespace std;
void sortColors(vector<int> &nums) {
    unordered_map<int, int> counts;
    for (int i = 0; i < nums.size(); ++i)
        counts[nums[i]]++;
    int k1 = counts[0], k2 = counts[1], k3 = counts[2];
    for (int i = 0; i < k1; ++i)
        nums[i] = 0;
    for (int i = k1; i < k1 + k2; ++i)
        nums[i] = 1;
    for (int i = k1 + k2; i < nums.size(); ++i)
        nums[i] = 2;
}
int main() {
    vector<int> nums = {1, 0, 0, 1, 2};
    sortColors(nums);
    for (int i = 0; i < nums.size(); ++i)
        cout << nums[i] << " ";
    getchar();
}
```

![image-20210515104840656](https://gitee.com/destiny0118/picgo/raw/master/20210515104840.png)

# tip2

> 单指针，两次遍历，第一次遍历将所有0放到开头，第二遍将1放到0后面

```c++
void sortColors1(vector<int> &nums) {
    int ptr = 0;
    for (int i = 0; i < nums.size(); ++i) {
        if (nums[i] == 0) {
            swap(nums[i], nums[ptr]);
            ptr++;
        }
    }
    for (int j = ptr; j < nums.size(); ++j) {
        if (nums[j] == 1) {
            swap(nums[j], nums[ptr]);
            ptr++;
        }
    }
}
```

![image-20210515232120963](https://gitee.com/destiny0118/picgo/raw/master/20210515232121.png)

# tip3

```c++
//双指针，分别指向0和1
void sortColors3(vector<int> &nums) {
    int p0 = 0, p1 = 0;
    for (int i = 0; i < nums.size(); ++i) {
        if (nums[i] == 1) {
            swap(nums[i], nums[p1++]);
        }
        if (nums[i] == 0) {
            swap(nums[i], nums[p0]);
            if (p0 < p1) {
                swap(nums[i], nums[p1]);
            }
            p0++;
            p1++;
        }
    }
}
```

![image-20210515234937945](https://gitee.com/destiny0118/picgo/raw/master/20210515234937.png)

# tip4

```c++
//双指针，分别指向0和2
void sortColors4(vector<int> &nums) {
    int p0 = 0, p2 = nums.size() - 1;
    for (int i = 0; i <= p2; ++i) {
        while (i <= p2 && nums[i] == 2) {
            swap(nums[i], nums[p2--]);
        }
        if (nums[i] == 0)
            swap(nums[i], nums[p0++]);
    }
}
```

![image-20210515235417810](https://gitee.com/destiny0118/picgo/raw/master/20210515235417.png)

# [题解]([颜色分类 - 颜色分类 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/sort-colors/solution/yan-se-fen-lei-by-leetcode-solution/))

