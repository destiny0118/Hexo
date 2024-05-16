---
title: 区间查询：ST表
description: 求区间最值，适用于数组不变，多次查询
data: 2023-7-27
---

# ST表

离散表（Sparse Table，ST），主要用来解决最大值/最小值查询(Range Minimum/Maximum Query，RMQ)，可以快速的查询区间内的最大值最小值。`lookup[i][j]表示从下标i开始长度为`$2^j$`的区间的最小值`。

![img](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202307270950867.webp)

区间`[i,j]`长度为`L=j-i+1`，得到`k`使得$2^k<=L并且2^{k+1}>L$，考虑两个子区间$[i,i+2^k-1]$和$[j-2^k+1,j]$，两个区间的长度为$2^k$，这两个区间重叠。则$min(i,j)=min(lookup[i][k],lookup[j-2^k+1][k])$。

计算`lookup`，$lookup[i][j]=min(lookup[i][j-1],lookup[i+2^{j-1}][j-1])$，将长度为$2^j$的区间分为长度为$2^{j-1}$的两个区间。
$$
计算lookup需要O(nlogn)的时间和空间复杂度\\
查询O(1)复杂度
$$

> 适用于数组不变、多次查询

其实ST表不仅能处理最大值/最小值，凡是符合**结合律**且**可重复贡献**的信息查询都可以使用ST表高效进行。什么叫可重复贡献呢？设有一个二元运算$f(x,y)$，满足 $f(a,a)=a$，则$f$是可重复贡献的。显然最大值、最小值、最大公因数、最小公倍数、按位或、按位与都符合这个条件。可重复贡献的意义在于，可以对两个交集不为空的区间进行信息合并。

#### [2762. 不间断子数组](https://leetcode.cn/problems/continuous-subarrays/)

```c++
class Solution {
  public:
    long long continuousSubarrays(vector<int> &nums) {
        int n = nums.size();
        int m = log2(n);

        vector<vector<int>> dpMin(n, vector<int>(m + 1)),
            dpMax(n, vector<int>(m + 1));
        // 初始化
        for (int i = 0; i < n; i++) {
            dpMin[i][0] = nums[i];
            dpMax[i][0] = nums[i];
        }

        // 计算
        for (int j = 1; (1 << j) <= n; j++) {
            for (int i = 0; i + (1 << j) <= n; i++) {
                dpMin[i][j] =
                    min(dpMin[i][j - 1], dpMin[i + (1 << (j - 1))][j - 1]);
                dpMax[i][j] =
                    max(dpMax[i][j - 1], dpMax[i + (1 << (j - 1))][j - 1]);
            }
        }

        long long ans = 0;
        int left = 0;

        for (int right = 0; right < n; right++) {
            int k = log2(right - left + 1);
            int mn = min(dpMin[left][k], dpMin[right - (1 << k) + 1][k]);
            int mx = max(dpMax[left][k], dpMax[right - (1 << k) + 1][k]);
            // 最大值和最小值的差值>2
            while (mx - mn > 2) {
                left++;
                k = log2(right - left + 1);
                mn = min(dpMin[left][k], dpMin[right - (1 << k) + 1][k]);
                mx = max(dpMax[left][k], dpMax[right - (1 << k) + 1][k]);
            }
            ans += (right - left + 1);
        }
        return ans;
    }
};
```

## 参考

[数组连续区间的最大最小值查询 - 简书 (jianshu.com)](https://www.jianshu.com/p/f7d66ea6577e)

[算法学习笔记(12): ST表 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/105439034)

 [思维提升|leetcode\]6种算法解决LeetCode困难题：滑动窗口最大值_leetcode st表_ErikTse_的博客-CSDN博客](https://blog.csdn.net/qq_29495615/article/details/129521626)

[ST表算法详解 - soul_maker - 博客园 (cnblogs.com)](https://www.cnblogs.com/soul-maker/p/soul_maker1.html)

[(78条消息) ST表详解(稀疏表)_C+G的博客-CSDN博客](https://blog.csdn.net/m0_50945504/article/details/120070372)（题目）