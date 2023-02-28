---
title: leetcode-215
categories: 算法
tags: 快速排序
---

# 215. 数组中的第K个最大元素

> 在未排序的数组中找到第 **k** 个最大的元素。请注意，你需要找的是数组排序后的第 k 个最大的元素，而不是第 k 个不同的元素。

```
输入: [3,2,1,5,6,4] 和 k = 2
输出: 5
```

```c++
#include <iostream>
#include <vector>
using namespace std;
/*
    找出数组中第K大的数，从0开始
*/
int quickSelection(vector<int> &nums, int l, int r) {
    int key = nums[l];
    while (l < r) {
        while (l < r && nums[r] >= key) {
            --r;
        }
        nums[l] = nums[r];
        while (l < r && nums[l] <= key) {
            l++;
        }
        nums[r] = nums[l];
    }
    nums[l] = key;
    return l;
}
int findKthLargest(vector<int> &nums, int k) {
    int l = 0, r = nums.size() - 1;
    int target = nums.size() - k;
    while (l < r) {
        int mid = quickSelection(nums, l, r);
        // cout << "l" << l << " mid" << mid << " r" << r << endl;
        if (mid == target)
            return nums[mid];
        if (mid > target)
            r = mid - 1;
        else {
            l = mid + 1;
        }
    }
    return nums[l];
}

int main() {
    vector<int> nums = {3, 2, 1, 5, 6, 4};
    cout << findKthLargest(nums, 2);
    return 0;
}
```

## tip: 

快速排序算法思想的应用，快速排序每次可以确定一个元组的位置

