---
title: Leetcode
tags: Leetcode
categories: 算法
description: Leetcode题目
---

# 贪心算法

## p405

> 双指针

```c++
 int findContentChildren(vector<int>& g, vector<int>& s) {
    sort(g.begin(), g.end());//胃口
    sort(s.begin(), s.end());//满足
    int count = 0;
    int j = 0;
    for (int i = 0; i < g.size() && j < s.size(); j++)
    {
        if (g[i] <= s[j])
        {
            count++;
            i++;
        }
    }
       return count;
    }
```

## p435

> 自定义sort排序

```c++
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
bool compare(vector<int> &a, vector<int> b)
{
    return a[1] < b[1];
}
int main()
{
    vector<vector<int>> intervals = {{1, 2}, {2, 4}, {1, 3}};
    if (intervals.empty())
        return 0;
    int n = intervals.size();

    // sort(intervals.begin(), intervals.end(), compare);
    sort(
        intervals.begin(), intervals.end(), [](vector<int> &a, vector<int> &b) { return a[1] < b[1]; });
    int pre = intervals[0][1];
    int total = 0; //移出的区间
    for (int i = 1; i < intervals.size(); i++)
    {
        if (intervals[i][0] < pre)
            ++total;
        else
            pre = intervals[i][1];
    }
    cout << total << endl;
    return 0;
}
```

## p605

> 艰难的修改无数次，各种边界条件的修补

```c++
#include <iostream>
#include <vector>
using namespace std;

int main()
{
    vector<int> flowerbed = {0, 0, 1, 0, 0};
    int n = 1;

    int size = flowerbed.size();
    if (size == 1 && flowerbed[0] == 1 && n <= 1)
    {
        n = 0;
    }
    else if (size > 1)
    {
        if (!flowerbed[0] && !flowerbed[1])
        {
            flowerbed[0] = 1;
            n--;
        }
        if (!flowerbed[size - 1] && !flowerbed[size - 2])
        {
            flowerbed[flowerbed.size() - 1] = 1;
            n--;
        }
        for (int i = 1; i < flowerbed.size() - 1 && n; i++)
        {
            if (!flowerbed[i - 1] && !flowerbed[i] && !flowerbed[i + 1])
            {
                n--;
                flowerbed[i] = 1;
            }
        }
    }
    if (n <= 0)
        cout << true;
    else
        cout << false;
    return 0;
}
```

> 太巧妙了
>
> 首先这里我用的是连跳两格的方法，因为如果遇到1,那么下一格子一定是0，这是毋庸置疑的（规则限定），所以如果遇到最后一个格子，或者下个格子不是1，果断填充

```c++
class Solution {
public:
    bool canPlaceFlowers(vector<int>& flowerbed, int n) {
        // 每次跳两格
         for (int i = 0; i < flowerbed.size(); i += 2) {
             // 如果当前为空地
            if (flowerbed[i] == 0) {
                // 如果是最后一格或者下一格为空
                if (i == flowerbed.size() - 1 || flowerbed[i + 1] == 0) {
                    n--;
                } else {
                    i++;
                }
            }
        }
        return n <= 0;
    }
};
```

[链接](https://leetcode-cn.com/problems/can-place-flowers/solution/qi-si-miao-jie-by-heroding-h7m0/)

## [452. 用最少数量的箭引爆气球](https://leetcode-cn.com/problems/minimum-number-of-arrows-to-burst-balloons/)

> 每次尽量引爆最多气球

```c++
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
int findMinArrowShots(vector<vector<int>> &points)
{
    int size = points.size();
    if (size < 2)
        return size;
    sort(points.begin(), points.end(), [](vector<int> &a, vector<int> &b) { return a[1] < b[1]; });
    // cout << points[0][1] << endl;
    int count = 0;
    int p = points[0][0];
    int q = points[0][1];
    for (int i = 1; i < points.size(); i++)
    {
        if (points[i][0] > p)
            p = points[i][0];
        if (p <= q)
            continue;
        else
        {
            count++;
            q = points[i][1];
        }
    }
    if (p <= q)
        count++;
    return count;
}
int main()
{
    vector<vector<int>> points = {{10, 16}, {2, 8}, {1, 6}, {7, 12}};
    cout << findMinArrowShots(points) << endl;
    return 0;
}
```

## [763. 划分字母区间](https://leetcode-cn.com/problems/partition-labels/)

### tip1

> 待优化

```c++
#include <iostream>
#include <string>
#include <vector>
#include <map>
#include <set>
using namespace std;
vector<int> partitionLabels(string S);
int main()
{
    string s = "ababcbacadefegdehijhklij";
    vector<int> ans;
    ans = partitionLabels(s);

    vector<int>::iterator it;
    for (it = ans.begin(); it != ans.end(); it++)
        cout << *it << endl;
    return 0;
}

vector<int> partitionLabels(string S)
{
    map<char, int> labelCount;
    for (int i = 0; i < S.length(); i++)
    {
        if (!labelCount.count(S[i]))
            labelCount[S[i]] = 1;
        else
            labelCount[S[i]]++;
    }
    // cout << labelCount['a'] << endl;
    set<char> cache;
    vector<int> ans;
    int sum = 0;
    int startPos = 0, endPos = 0;
    for (int i = 0; i < S.length(); i++)
    {
        //当前字母没有加入缓存
        if (!cache.count(S[i]))
        {
            cache.insert(S[i]);
            sum += labelCount[S[i]];
        }
        sum--;
        if (sum == 0)
        {
            ans.push_back(i - startPos + 1);
            startPos = i + 1;
        }
    }
    return ans;
}
```



![image-20210129162939091](C:/Users/16325/AppData/Roaming/Typora/typora-user-images/image-20210129162939091.png)

### tip2

## [122. 买卖股票的最佳时机 II](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/)

### tip1 贪心算法

> 选择最低点买入，相邻的最高点卖出

```c++
#include <iostream>
#include <vector>
using namespace std;

int maxProfit(vector<int> &prices)
{
    int sum = 0;
    int size = prices.size();
    if (size < 2)
        return 0;

    int buy = prices[0], sell;
    bool canSell = false;
    for (int i = 1; i < prices.size(); i++)
    {
        canSell = true;
        // cout << "i=" << i << endl;
        if (prices[i] <= buy)
        {
            buy = prices[i];
            continue;
        }
        //找到卖出最高价
        else
        {
            // cout << "buy:" << buy << endl;
            sell = prices[i];
            for (i = i + 1; i < prices.size(); i++)
            {
                if (prices[i] >= sell)
                {
                    sell = prices[i];
                    continue;
                }
                // cout << "sell:" << sell << endl;
                sum += sell - buy;
                buy = prices[i];
                canSell = false;
                // i++;
                // cout << "***buy:" << buy << endl;
                // cout << "***i=" << i << endl;
                break;
            }
            if (i == prices.size() && sell > buy && canSell)
            {
                sum += sell - buy;
                break;
            }
        }
    }
    return sum;
}

int main()
{
    vector<int> prices = {1, 2, 3, 4, 5};
    int profit = maxProfit(prices);
    cout << profit << endl;
    return 0;
}
```

### tip2 贪心算法*

```c++
int maxProfit(vector<int> &prices)
{
    int sum = 0;
    int size = prices.size();
    for (int i = 0; i < size - 1; i++)
    {
        if (prices[i + 1] - prices[i] > 0)
            sum += prices[i + 1] - prices[i];
    }

    return sum;
}
```

## [406. 根据身高重建队列](https://leetcode-cn.com/problems/queue-reconstruction-by-height/)

> 重点在排序，对于身高升序
>
> 对于**人数**降序

```c++
vector<vector<int>> reconstructQueue(vector<vector<int>> &people)
{
    //排序，身高优先，人数次之
    sort(people.begin(), people.end(), [](vector<int> &a, vector<int> &b) {if(a[0]==b[0]) return a[1]>b[1]; else return a[0]<b[0]; });
    int size = people.size();
    vector<vector<int>> queue(size);

    for (int i = 0; i < size; i++)
    {
        int num = people[i][1] + 1; //比其高的人数
        for (int j = 0; j < size; j++)
        {
            if (queue[j].empty())
                num--;
            if (num == 0)
            {
                queue[j] = people[i];
                break; //跳出当前循环
            }
        }
    }
    return queue;
}
```

## [665. 非递减数列](https://leetcode-cn.com/problems/non-decreasing-array/)

### tip1

> 情况太多
>
> 5 7 1 8 为判断的情况，修改1为7继续7<8
>
> 1 2 1 8 为跳过的情况，此时也可修改2为1，跳过判断
>
> 4 2 3
>
> 4 2 1这两种归于第二种
>
> 2 4 2 3（2 4 3 3归于第一种，可以修改4，接下来判断3 3）
>
> 2 4 2 1
>
> 3 4 2 3
>
> 3 4 2 1

```c++
bool checkPossibility(vector<int> &nums)
{
    int n = 0;
    int size = nums.size();
    for (int i = 1; i < size && n <= 1; i++)
    {
        if (nums[i] < nums[i - 1])
        {
            if (i - 2 >= 0 && nums[i - 2] > nums[i])
                nums[i] = nums[i - 1];
            n++;
        }
    }
    return n <= 1;
}
```

# 双指针

## [680. 验证回文字符串 Ⅱ](https://leetcode-cn.com/problems/valid-palindrome-ii/)

对比665修改一个数字，非递减数列

给定一个非空字符串 `s`，**最多**删除一个字符。判断是否能成为回文字符串。

**示例 1:**

```
输入: "aba"
输出: True
```

```c++
bool Palindrome(string &s, int i, int j)
{
    while (s[i] == s[j]&&i<j)
    {
        i++;
        j--;
    }
    return i >= j;
}
bool validPalindrome(string s)
{
    int p = 0, q = s.size() - 1;
    while (s[p] == s[q]&&p<q)
    {
        p++;
        q--;
    }
    return Palindrome(s, p, q - 1) || Palindrome(s, p + 1, q);
}
```

## [524. 通过删除字母匹配到字典里最长单词](https://leetcode-cn.com/problems/longest-word-in-dictionary-through-deleting/)

给定一个字符串和一个字符串字典，找到字典里面最长的字符串，该字符串可以通过删除给定字符串的某些字符来得到。如果答案不止一个，返回长度最长且字典顺序最小的字符串。如果答案不存在，则返回空字符串。

**示例 1:**

```
输入:
s = "abpcplea", d = ["ale","apple","monkey","plea"]

输出: 
"apple"
```

```c++
string findLongestWord(string s, vector<string> &d)
{
    string ans = "";
    for (string dict : d)
    {
        int j = 0;
        for (int i = 0; i < s.size() && j < dict.size(); i++)
        {
            if (s[i] == dict[j])
            {
                j++;
            }
        }
        // cout << j << endl;
        if (j == dict.length())
        {
            if ((j > ans.length()) || (j == ans.length() && ans.compare(dict) > 0))
                ans = dict;
        }
    }
    return ans;
}
```

# 二分查找

## [69. x 的平方根](https://leetcode-cn.com/problems/sqrtx/)

实现 int sqrt(int x) 函数。

计算并返回 x 的平方根，其中 x 是非负整数。

由于返回类型是整数，结果只保留整数的部分，小数部分将被舍去。

示例 1:

输入: 4
输出: 2

### tip1

```c++
int mySqrt(int x) {
         int left = 0, right = x;

    long mid = (left + right) / 2;
    while (left <= right)
    {
        if (mid * mid == x)
            return mid;
        else if (mid * mid > x)
        {
            right = mid - 1;
        }
        else
            left = mid + 1;
        mid = (left + right) / 2;
    }
    return (right + left) / 2;
    }
```

### tip2

```c++
 int mySqrt(int x) {
        int l=0,r=x,ans=0;
        while(l<=r)
        {
            long mid=(l+r)/2;
            if(mid*mid<=x)
            {
                ans=mid;
                l=mid+1;
            }
            else
            {
                r=mid-1;
            }
        }
        return ans;
    }
```

### tip3 牛顿迭代法

## [154. 寻找旋转排序数组中的最小值 II](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array-ii/)

### tip1

```c++
int findMin(vector<int> &nums)
{
    int start = 0, end = nums.size() - 1, min = nums[0];
    while (start <= end)
    {
        int mid = (start + end) / 2;
        // cout << start << mid << end << endl;
        if (nums[mid] == nums[start] && start < mid)
        {
            start++;
            // cout << start << mid << end << endl;
        }
        //右区间递增
        else if (nums[mid] <= nums[end])
        {
            if (nums[mid] < min)
                min = nums[mid];
            end = mid - 1;
        }
        //左区间递增
        else
        {
            if (nums[start] < min)
                min = nums[start];
            start = mid + 1;
        }
    }
    return min;
}
```

### [tip2 优化](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array-ii/solution/154-find-minimum-in-rotated-sorted-array-ii-by-jyd/)

## [540. 有序数组中的单一元素](https://leetcode-cn.com/problems/single-element-in-a-sorted-array/)

```c++
int singleNonDuplicate(vector<int> &nums)
{
    int start = 0, end = nums.size() - 1;
    while (start < end)
    {
        int mid = (start + end) / 2;
        if (mid % 2 == 1)
        {
            if (nums[mid] == nums[mid - 1])
                start = mid + 1;
            else
                end = mid;
        }
        else
        {
            if (nums[mid] == nums[mid + 1])
                start = mid + 2;
            else
                end = mid;
        }
    }
    return nums[start];
}
```

## [4. 寻找两个正序数组的中位数](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/)

### tip1 归并两个有序数组

> 调试了五次，边界问题

```c++
double findMedianSortedArrays(vector<int> &nums1, vector<int> &nums2)
{
    int m = nums1.size(), n = nums2.size();
    int len = m + n;
    vector<int> result(len);
    if (n == 0)
    {
        if (m % 2 == 0)
            return (nums1[m / 2 - 1] + nums1[m / 2]) / 2.0;
        else
            return nums1[m / 2];
    }
    else if (m == 0)
    {
        if (n % 2 == 0)
            return (nums2[n / 2 - 1] + nums2[n / 2]) / 2.0;
        else
            return nums2[n / 2];
    }
    else
    {
        int cnt = 0, index1 = 0, index2 = 0;
        while (cnt <= len && (index1 < m || index2 < n))
        {
            // cout << "####" << endl;
            // cout << "index1=" << index1 << " index2=" << index2 << endl;
            if (index1 >= m)
                result[cnt++] = nums2[index2++];
            else if (index2 >= n)
                result[cnt++] = nums1[index1++];
            else
            {
                if (nums1[index1] <= nums2[index2])
                    result[cnt++] = nums1[index1++];
                else
                    result[cnt++] = nums2[index2++];
            }
        }
        // for (int i = 0; i < result.size(); i++)
        //     cout << result[i] << endl;
        if (len % 2 == 0)
            return (result[len / 2 - 1] + result[len / 2]) / 2.0;
        else
            return result[len / 2];
    }
}
```



### tip2 记录中间数的值

> 这么简单的思想，怎么当时没想到呢

```c++
double findMedianSortedArrays(vector<int> &nums1, vector<int> &nums2)
{
    int m = nums1.size(), n = nums2.size();
    int len = m + n;
    int left, right = 0;
    int index1 = 0, index2 = 0;
    for (int i = 0; i <= len / 2; i++)
    {
        left = right;
        if (index1 < m && (index2 >= n || nums1[index1] <= nums2[index2]))
        {
            right = nums1[index1++];
        }
        else
            right = nums2[index2++];
    }
    if (len % 2 == 0)
        return (left + right) / 2.0;
    else
        return right;
}
```

### tip3 递归、二分查找

> 这代码结构太优美了

```c++
#include <iostream>
#include <vector>
#include <set>
#include <cmath>
using namespace std;
int getKth(vector<int> &nums1, int start1, int end1, vector<int> &nums2, int start2, int end2, int k);
double findMedianSortedArrays(vector<int> &nums1, vector<int> &nums2)
{
    int n = nums1.size(), m = nums2.size();
    int left = (n + m + 1) / 2;
    int right = (n + m + 2) / 2;
    //将偶数和奇数的情况合并，如果是奇数，求两次相同的k
    return (getKth(nums1, 0, n - 1, nums2, 0, m - 1, left) + getKth(nums1, 0, n - 1, nums2, 0, m - 1, right)) * 0.5;
}
int getKth(vector<int> &nums1, int start1, int end1, vector<int> &nums2, int start2, int end2, int k)
{
    int len1 = end1 - start1 + 1;
    int len2 = end2 - start2 + 1;
    if (len1 > len2)
        return getKth(nums2, start2, end2, nums1, start1, end1, k);
    if (len1 == 0)
        return nums2[start2 + k - 1];

    if (k == 1)
        return min(nums1[start1], nums2[start2]);

    int i = start1 + min(len1, k / 2) - 1;
    int j = start2 + min(len2, k / 2) - 1;
    if (nums1[i] > nums2[j])
        return getKth(nums1, start1, end1, nums2, j + 1, end2, k - (j - start2 + 1));
    else
        return getKth(nums1, i + 1, end1, nums2, start2, end2, k - (i - start1 + 1));
}
int main()
{
    vector<int> nums1 = {1, 2};
    vector<int> nums2 = {3, 4};
    cout << findMedianSortedArrays(nums1, nums2);
    return 0;
}
```

## [413. 等差数列划分](https://leetcode-cn.com/problems/arithmetic-slices/)

`如果一个数列至少有三个元素，并且任意两个相邻元素之差相同，则称该数列为等差数列。`

### tip1

```c++
int numberOfArithmeticSlices(vector<int> &A)
{
    int n = A.size();
    if (n <= 2)
        return 0;
    vector<int> dp(n + 1, 0);
    for (int i = 3; i <= n; i++)
    {
        if (A[i - 2] - A[i - 3] == A[i - 1] - A[i - 2])
        {
            dp[i] = dp[i - 1] + 1;
        }
    }
    return accumulate(dp.begin(), dp.end(), 0);
}
```

### tip2

```c++
int numberOfArithmeticSlices(vector<int>& A) {
        int n = A.size();
    if (n <= 2)
        return 0;
    int sum = 0, pre = 0;
    for (int i = 3; i <= n; i++)
    {
        if (A[i - 2] - A[i - 3] == A[i - 1] - A[i - 2])
        {
            pre = pre + 1;
            sum += pre;
        }
        else
            pre=0;
    }
    return sum;
    }
```

## [64. 最小路径和](https://leetcode-cn.com/problems/minimum-path-sum/)

### tip1

```c++
int minPathSum(vector<vector<int>> &grid)
{
    if (grid.empty())
        return 0;
    int row = grid.size(), col = grid[0].size();
    vector<vector<int>> dp(row, vector<int>(col, 0));
    dp[0][0] = grid[0][0];
    for (int i = 1; i < row; i++)
        dp[i][0] = grid[i][0] + dp[i - 1][0];
    for (int i = 1; i < col; i++)
        dp[0][i] = grid[0][i] + dp[0][i - 1];
    for (int i = 1; i < row; i++)
    {
        for (int j = 1; j < col; j++)
        {
            dp[i][j] = min(dp[i - 1][j], dp[i][j - 1]) + grid[i][j];
            // cout << "dp[" << i << "][" << j << "]=" << dp[i][j] << endl;
        }
    }
    return dp[row - 1][col - 1];
}
```

## [542. 01 矩阵](https://leetcode-cn.com/problems/01-matrix/)

### tip1

> 对比64最小路径和

```c++
if (matrix.empty())
        return {};
    int row = matrix.size(), col = matrix[0].size();
    vector<vector<int>> ans(row, vector<int>(col, __INT_MAX__ - 1));
    //左，上
    for (int i = 0; i < row; ++i)
    {
        for (int j = 0; j < col; ++j)
        {
            if (matrix[i][j] == 0)
                ans[i][j] = 0;
            else
            {
                if (i > 0)
                    ans[i][j] = min(ans[i - 1][j] + 1, ans[i][j]);
                if (j > 0)
                    ans[i][j] = min(ans[i][j - 1] + 1, ans[i][j]);
            }
        }
    }
    //右，下
    for (int i = row - 1; i >= 0; --i)
    {
        for (int j = col - 1; j >= 0; --j)
        {
            if (matrix[i][j] != 0)
            {
                if (i < row - 1)
                    ans[i][j] = min(ans[i + 1][j] + 1, ans[i][j]);
                if (j < col - 1)
                    ans[i][j] = min(ans[i][j + 1] + 1, ans[i][j]);
            }
        }
    }
    return ans;
```

## [322. 零钱兑换](https://leetcode-cn.com/problems/coin-change/)

### tip1

```c++
int coinChange(vector<int> &coins, int amount)
{
    if (coins.empty())
        return -1;
    int n = coins.size();
    vector<int> dp(amount + 1, amount + 1);
    dp[0] = 0;
    for (int i = 1; i <= n; i++)
    {
        for (int j = coins[i - 1]; j <= amount; j++)
            dp[j] = min(dp[j], dp[j - coins[i - 1]] + 1);
    }
    return dp[amount] > amount ? -1 : dp[amount];
}
```

### tip2

```c++

```

