## 1. 两数之和

`两数之和为目标值`

## 15. 三数之和

`三数之和为0`

> Given an integer array nums, return all the triplets [nums[i], nums[j], nums[k]] such that i != j, i != k, and j != k, and nums[i] + nums[j] + nums[k] == 0.
>
> Notice that the solution set must not contain duplicate triplets.
>
> Example 1:
>
> Input: nums = [-1,0,1,2,-1,-4]
> Output: [[-1,-1,2],[-1,0,1]]
> 链接：https://leetcode-cn.com/problems/3sum

### 排序+双指针

> 当我们需要枚举数组中的两个元素时，如果我们发现随着第一个元素的递增，第二个元素是递减的，那么就可以使用双指针的方法

i标识第一个元素位置，并且first=-(second+third)

此时m<n作为判断条件无法进行重复三元组的判断

```c++
vector<vector<int>> threeSum(vector<int> &nums) {
    sort(nums.begin(), nums.end());
    vector<vector<int>> ans;
    int m, n;
    if (nums.size() < 3)
        return ans;
    for (int i = 0; i < nums.size() - 2; i++) {
        m = i + 1;
        n = nums.size() - 1;
        while (m < n) {
            if (nums[m] + nums[n] + nums[i] == 0) {
                ans.push_back(vector<int>{nums[i], nums[m], nums[n]});
                m++;
                n--;
            } else if (nums[m] + nums[n] + nums[i] < 0)
                m++;
            else
                n--;
        }
    }
    set<vector<int>> setVec(ans.begin(), ans.end());
    ans.assign(setVec.begin(), setVec.end());
    return ans;
}
```

### tip2

```c++
vector<vector<int>> threeSum1(vector<int> &nums) {
    sort(nums.begin(), nums.end());
    vector<vector<int>> ans;
    int m, n;
    //考虑空数组情况，这里i<nums.size()-2为实际范围，这样写不用判断数组大小是否符合
    for (int i = 0; i < nums.size(); i++) {
        if (i > 0 && nums[i] == nums[i - 1])
            continue;
        m = i + 1;
        n = nums.size() - 1;
        for (; m < nums.size(); m++) {
            if (m > i + 1 && nums[m] == nums[m - 1])
                continue;
            //寻找第三个元素
            while (m < n && nums[i] + nums[m] + nums[n] > 0)
                n--;
            //比较巧妙的情况，当m==n时，说明三个数相加还是大于0的，因为已经排好序，继续向后寻找第二个数还是会大于0
            if (m == n)
                break;
            if (nums[i] + nums[m] + nums[n] == 0)
                ans.push_back(vector<int>{nums[i], nums[m], nums[n]});
        }
    }
    return ans;
}
```

## 16. 最接近的三数之和