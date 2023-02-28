---
title: 树状数组(Binary Indexed Tree, BIT)
tags:
	- 动态规划
	- 位运算
categories: 
	- 算法
---

[什么是树状数组？让这个12岁年轻人为你讲解_lowbit (sohu.com)](https://www.sohu.com/a/471187961_121124361)

#### [315. 计算右侧小于当前元素的个数](https://leetcode-cn.com/problems/count-of-smaller-numbers-after-self/)

> 给你一个整数数组 nums ，按要求返回一个新数组 counts 。数组 counts 有该性质： counts[i] 的值是  nums[i] 右侧小于 nums[i] 的元素的数量。
>
> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/count-of-smaller-numbers-after-self
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

```c++
class BIT{
    private:
        int n;
        vector<int> tree;
    public:
        BIT(int _n): n(_n),tree(_n+1){}

        static int lowbit(int x){
            return x&(-x);
        }
        // 查询前缀和
        int query(int x){
            int res=0;
            while(x){
                res+=tree[x];
                x-=lowbit(x);
            }
            return res;
        }
        // 更新前缀和
        void update(int x){
            while(x<=n){
                tree[x]++;
                x+=lowbit(x);
            }
        }
};

class Solution {
public:
    vector<int> countSmaller(vector<int>& nums) {
        int n=nums.size();
        vector<int> tmp=nums;
        sort(tmp.begin(),tmp.end());
        // 将数据离散到[1:n]
        for(int &num:nums){
            num=lower_bound(tmp.begin(),tmp.end(),num)-tmp.begin()+1;
        }
        vector<int> counts(n,0);
        BIT bit(n);
        for(int i=n-1;i>=0;i--){
            counts[i]=bit.query(nums[i]-1);
            bit.update(nums[i]);
        }
        return counts;
    }
};
```

#### [剑指 Offer 51. 数组中的逆序对](https://leetcode-cn.com/problems/shu-zu-zhong-de-ni-xu-dui-lcof/)

> 在数组中的两个数字，如果前面一个数字大于后面的数字，则这两个数字组成一个逆序对。输入一个数组，求出这个数组中的逆序对的总数。

```c++
class BIT{
    private:
        vector<int> tree;
        int n;
    public:
        BIT(int _n):n(_n),tree(_n+1){}
        static int lowbit(int x){
            return x&(-x);
        }
        int query(int x){
            int ret=0;
            while(x){
                ret+=tree[x];
                x-=lowbit(x);
            }
            return ret;
        }
        void update(int x){
            while(x<=n){
                tree[x]++;
                x+=lowbit(x);
            }
        }
};

class Solution {
public:
    int reversePairs(vector<int>& nums) {
        int n=nums.size();
        vector<int> tmps=nums;
        sort(tmps.begin(),tmps.end());
        for(int &num:nums)
            num=lower_bound(tmps.begin(),tmps.end(),num)-tmps.begin()+1;
        BIT bit(n);
        int ans=0;
        for(int i=n-1;i>=0;i--){
            // 查找比num[i]小的数字个数
            ans+=bit.query(nums[i]-1);
            bit.update(nums[i]);
        }
        return ans;
    }
};
```

