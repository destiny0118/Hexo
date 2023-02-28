---
title: LeetCode-4
categories: 算法
tags: 二分查找
---

> 给定两个大小分别为 m 和 n 的正序（从小到大）数组 nums1 和 nums2。请你找出并返回这两个正序数组的 中位数 。

![image-20210426231141571](https://gitee.com/destiny0118/picgo/raw/master/pic/image-20210426231141571.png)

# tip1: 寻找中间位置的值

> 从两个正序数组的头部开始查找，类似于合并两个有序数组，查找中间位置对应的值

- 偶数

  4/2=2，查找三次

  第三次找到的值赋给right

- 奇数

  5/2=2，查找三次

  第三次找到的值赋给right

```c++
#include <iostream>
#include <vector>
using namespace std;
double findMedianSortedArrays(vector<int> &nums1, vector<int> &nums2) {
    int len1 = nums1.size(), len2 = nums2.size();
    int left = 0, right = 0; //记录两个中间值
    int index1 = 0, index2 = 0;
    for (int i = 0; i <= (len1 + len2) / 2; ++i) {
        left = right;
        if (index1 < len1 &&
            (index2 >= len2 || nums1[index1] <= nums2[index2])) {
            right = nums1[index1++];
        } else {
            right = nums2[index2++];
        }
    }
    if ((len1 + len2) % 2 == 0) {
        return (left + right) / 2.0;
    } else {
        return right;
    }
}
int main() {
    vector<int> nums1 = {2};
    vector<int> nums2 = {};
    cout << findMedianSortedArrays(nums1, nums2);
    return 0;
}
```

# tip2: 每次减少一半的查找范围

> left, right为要找的元素的位序，这里的技巧使得奇数与偶数情况统一
>
> 设要找的元素位序为k，每次排除k/2的元素，接着寻找
>
> - 其中一个数组的元素都被排除，则在另一个数组查找对应位序的元素
> - 要查找的位序为1的元素，比较两个数组的开头

```c++
#include <iostream>
#include <vector>
using namespace std;
//取得两个数组中从对应起点到终点第k大的数
int getKth(vector<int> &nums1, int start1, int end1, vector<int> &nums2,
           int start2, int end2, int k) {
    int len1 = end1 - start1 + 1;
    int len2 = end2 - start2 + 1;
    //某一数组查找完不符合条件时，只需在另一个数组中查找
    if (start1 > end1)
        return nums2[start2 + k - 1];
    if (start2 > end2)
        return nums1[start1 + k - 1];

    if (k == 1)
        return min(nums1[start1], nums2[start2]);

    int i = start1 + min(len1, k / 2) - 1;
    int j = start2 + min(len2, k / 2) - 1;

    if (nums1[i] > nums2[j]) {
        return getKth(nums1, start1, end1, nums2, j + 1, end2,
                      k - (j - start2 + 1));
    } else {
        return getKth(nums1, i + 1, end1, nums2, start2, end2,
                      k - (i - start1 + 1));
    }
}
double findMedianSortedArrays(vector<int> &nums1, vector<int> &nums2) {
    int n = nums1.size();
    int m = nums2.size();
    //取得第left,right大的数
    int left = (n + m + 1) / 2;
    int right = (n + m + 2) / 2;
    return (getKth(nums1, 0, n - 1, nums2, 0, m - 1, left) +
            getKth(nums1, 0, n - 1, nums2, 0, m - 1, right)) *
           0.5;
}
int main() {
    vector<int> num1 = {1, 2};
    vector<int> num2 = {3, 4};
    cout << findMedianSortedArrays(num1, num2) << endl;
    return 0;
}
```

