# Interval trees（线段树、区间树）

[线段树详解「汇总级别整理 🔥🔥🔥」 - 我的日程安排表 I - 力扣（LeetCode）](https://leetcode.cn/problems/my-calendar-i/solution/by-lfool-xvpv/)
[(6条消息) 线段树详解 （原理，实现与应用）_岩之痕的博客-CSDN博客_线段树](https://blog.csdn.net/zearot/article/details/48299459)
# 线段树应用

线段树用于解决区间和问题，且该区间会被修改。

如果需要多次求某区间的和，可以使用前缀和。如果对某个元素进行修改，或者对某个区间内的元素进行修改，则前缀和不再适用。
线段树每个节点代表一个区间，节点的值是区间的和。对于数组$nums=[1,2,3,4,5]$：
![image.png](https://raw.githubusercontent.com/destiny0118/picgo/master/pic2023/202307112002172.png)


每个节点不仅可以用于表示区间的和

- 数字之和「总数字之和 = 左区间数字之和 + 右区间数字之和」
- 最大公因数 (GCD)「总 GCD = gcd(左区间 GCD, 右区间 GCD)」
- 最大值「总最大值 = max(左区间最大值，右区间最大值)」

不符合区间加法的例子：
- 众数「只知道左右区间的众数，没法求总区间的众数」
- 01 序列的最长连续零「只知道左右区间的最长连续零，没法知道总的最长连续零」

# 数据结构

[(50条消息) 线段树 4n 开四倍空间的原因_Andy-Miao的博客-CSDN博客](https://blog.csdn.net/mmww1994/article/details/104206072)

可以使用数组表示一颗线段树：

- 假设根节点为`i`，左孩子为`2*i`，右孩子为`2*i+1`（数组下标从1开始）
- 假设根节点为`i`，左孩子为`2*i+1`，右孩子为`2*i+2`（数组下标从0开始）。

![线段树](https://raw.githubusercontent.com/destiny0118/picgo/master/pic2023/202307122155009.png)

区间大小为$n=2^k$，需要节点数$1+2+2^2+...+2^k=2n-1$；$n=2^{k+1}$时，需要节点$4n-1$。

当$2^k < n<2^{k+1}$时，节点落在下标[2n,4n-1)内，



链表表示线段树：

```c++
struct Node{
    Node *left,*right;
    int val;		//当前节点值，节点表示区间(区间的和，区间的最大值。。。)
    int lazy;		//懒惰标记
}
```

# 建立线段树

给定具体区间范围，根据范围建立线段树，此时可以用数组或者链表表示线段树。[307. 区域和检索 - 数组可修改](https://leetcode.cn/problems/range-sum-query-mutable/)

```c++
n=nums.size();
builtTree(root,0,n-1,nums);

//[start,end]是node当前表示的区间范围
void builtTree(Node *node,int start,int end,vector<int>& nums){
    if(start==end){
        node->val=nums[start];
        return ;
    }
    if(!node->left) node->left=new Node();
    if(!node->right) node->right=new Node();
    
    int mid=start+(end-start)/2;
    builtTree(node->left,start,mid,nums);
    builtTree(node->right,mid+1,end,nums);
    //pushUp，向上更新
    node->val=node->left->val+node->right->val;
}
```

如果题目没有具体范围，只有数据的取值范围，可以采用**[动态开点线段树]**。假设数组长度为5，添加元素`[2,2], val=3`，`update(root,0,4,2,2,3)`。如果一个节点没有左右孩子，会一下子把左右孩子节点都给创建出来，如下图橙色节点所示。两个橙色的叶子节点仅仅只是被创建出来了，并无实际的值，均为 0；而另外一个橙色的非叶子节点，值为 3 的原因是下面的孩子节点的值向上更新得到的

![image-20230711215828443](C:\Users\16325\AppData\Roaming\Typora\typora-user-images\image-20230711215828443.png)
添加节点变化，「动态开点」一般是在「更新」或「查询」的时候动态的建立节点：
![image.png](https://raw.githubusercontent.com/destiny0118/picgo/master/pic2023/202307120940030.png)

# 线段树的更新
更新区间[2,4]值+1，`update(root,0,4,2,4,1)。此时9号节点代表区间[3,4]都需要+1，则节点值+(end-start+1)*val，对节点添加懒惰标记lazy。当查询区间[3,3]时，懒惰标记在9号节点上，需要将标记下推给4和5号节点，同时取消标记。

![image.png](https://raw.githubusercontent.com/destiny0118/picgo/master/pic2023/202307120943105.png)


```c++
 // 查询区间[l,r]区间+val
void update(Node* node,int start,int end,int l,int r,int val){
    // 当前区间位于查询区间内，当前区间每一个元素增加val
    if(l<=start&&end<=r){
        node->val+=(end-start+1)*val;
        // 查询时可能查找当前区间子区间，记录标记，表示区间的增加量，以下推给子区间
        node->add+=val;
        return;
    }

    // 当前区间不全部位于查询区间，只有部分元素增加，当前节点的懒惰标记向下推
    int mid=start+(end-start)/2;
    pushDown(node,mid-start+1,end-mid);
    // 分为两个区间：[start,mid] [mid+1,end]
    if(l<=mid)
        update(node->left,start,mid,l,r,val);
    if(r>mid)
        update(node->right,mid+1,end,l,r,val);
    pushUp(node);
}
void pushUp(Node* node){
    node->val=node->left->val+node->right->val;
}

// leftNum 和 rightNum 表示左右孩子区间的叶子节点数量
void pushDown(Node* node,int leftNum,int rightNum){
    if(!node->left) node->left=new Node();
    if(!node->right) node->right=new Node();
    // 如果add为0，则没有标记，不需要下推
    if(node->add==0)
        return;

    // node表示区间[start,end]，其左右孩子分别表示区间[start,mid], [mid+1,end]
    // add标记最初添加在node节点，对区间[start,end]的数都添加add
    node->left->val+=leftNum*node->add;
    node->right->val+=rightNum*node->add;

    // 把标记下推给子区间
    node->left->add+=node->add;
    node->right->add+=node->add;

    // 已经将标记分别添加到区间[start,mid], [mid+1,end]，当前区间取消标记
    node->add=0;
}
```

# 线段树的查询

```c++
//在当前区间[start,end]，查询区间[l,r]
int query(Node *node,int start,int end,int l,int r){
        if(l<=start&&end<=r){
            return node->val;
        }
        //[start,end]划分为区间[start,mid],[mid+1,right]
        int mid=start+(end-start)/2;
        //下推标记
		pushDown(node,mid-start+1,end-mid);

        int ans=0;
        if(l<=mid)
            ans+=query(node->left,start,mid,l,r);
        if(r>mid)
            ans+=query(node->right,mid+1,end,l,r);
        return ans;
    }
```
# 线段树模板
## 基于[区间和]

```c++
class SegmentTreeDynamic {
    struct Node{
        Node *left,*right;
        int val;
        int add;
    };

public:
 
    int N=1e9;
    Node* root=new Node(); 

	//调用函数
    bool book(int start, int end) {
        if(query(root,0,N,start,end-1)!=0)
            return false;
        update(root,0,N,start,end-1,1);
        return true;
    }




    // [l,r]区间+val
    void update(Node* node,int start,int end,int l,int r,int val){
        if(l<=start&&end<=r){
            node->val+=(end-start+1)*val;
            node->add+=val;
            return;
        }

        // 当前节点的懒惰标记向下推
        int mid=start+(end-start)/2;
        pushDown(node,mid-start+1,end-mid);
        // 分为两个区间：[start,mid] [mid+1,end]
        if(l<=mid)
            update(node->left,start,mid,l,r,val);
        if(r>mid)
            update(node->right,mid+1,end,l,r,val);
        pushUp(node);
    }
    void pushUp(Node* node){
        node->val=node->left->val+node->right->val;
    }
    void pushDown(Node* node,int leftNum,int rightNum){
        if(!node->left) node->left=new Node();
        if(!node->right) node->right=new Node();
        // 如果add为0，则没有标记，不需要下推
        if(node->add==0)
            return;

        node->left->val+=leftNum*node->add;
        node->right->val+=rightNum*node->add;

        node->left->add+=node->add;
        node->right->add+=node->add;

        node->add=0;
    }
    // 查询区间未[l,r]，当前区间为[start,end]
    int query(Node* node,int start,int end,int l,int r){
        if(l<=start&&end<=r){
            return node->val;
        }
        int mid=start+(end-start)/2;
        int ans=0;
        pushDown(node,mid-start+1,end-mid);
        if(l<=mid)
            ans+=query(node->left,start,mid,l,r);
        if(r>mid)
            ans+=query(node->right,mid+1,end,l,r);
        return ans;
    }
};
```

## 基于区间最大值

```c++
class MyCalendarTwo {
  public:
    struct Node {
        Node *left, *right;
        int val;
        int lazy;
    };
    Node *root;
    int N = 1e9;
   
    void update(Node *root, int start, int end, int l, int r, int val) {
        if (l <= start && end <= r) {
            root->val += val;
            root->lazy += val;
            return;
        }
        int mid = start + (end - start) / 2;

        // 下推懒惰标记
        pushDown(root, mid - start + 1, end - mid);
        if (l <= mid)
            update(root->left, start, mid, l, r, val);
        if (r > mid)
            update(root->right, mid + 1, end, l, r, val);

        // 更新当前节点区间最大值
        root->val = max(root->left->val, root->right->val);
    }
    void pushDown(Node *root, int leftNum, int rightNum) {
        if (!root->left)
            root->left = new Node();
        if (!root->right)
            root->right = new Node();

        if (root->lazy == 0)
            return;

        int val = root->lazy;
        // 左右节点最大值分别加val
        root->left->val += val;
        root->right->val += val;
        // 左右子节点懒惰标记
        root->left->lazy += val;
        root->right->lazy += val;

        root->lazy = 0;
    }
    // 查询区间最大值
    int query(Node *root, int start, int end, int l, int r) {
        if (l <= start && end <= r) {
            return root->val;
        }

        int mid = start + (end - start) / 2;
        pushDown(root, mid - start + 1, end - mid);
        int ans = -1;
        if (l <= mid)
            ans = max(ans, query(root->left, start, mid, l, r));
        if (r > mid)
            ans = max(ans, query(root->right, mid + 1, end, l, r));
        return ans;
    }

    MyCalendarTwo() { root = new Node(); }
    bool book(int start, int end) {
        if (query(root, 0, N, start, end-1) >= 2)
            return false;
        update(root, 0, N, start, end-1, 1);
        return true;
    }

};
```

## 区间最小值

#### [1851. 包含每个查询的最小区间](https://leetcode.cn/problems/minimum-interval-to-include-each-query/)

```c++
class Solution {
  public:
    int N = 1e7;
    struct Node {
        Node *left, *right;
        int val = INT_MAX;
        int lazy = INT_MAX;
    };
    vector<int> minInterval(vector<vector<int>> &intervals,
                            vector<int> &queries) {
        Node *root = new Node();

        for (auto &interval : intervals) {
            int l = interval[0], r = interval[1];
            update(root, 0, N, l, r, r - l + 1);
        }
        int n = queries.size();
        vector<int> ans(n, -1);

        for (int i = 0; i < n; i++) {
            int t = query(root, 0, N, queries[i], queries[i]);
            if (t != INT_MAX)
                ans[i] = t;
        }
        return ans;
    }

    void update(Node *root, int start, int end, int l, int r, int val) {
        //val记录root表示区间的最小值
        //lazy表示可以在当前区间继续向下更新的值
        if (l <= start && end <= r) {
            root->val = min(root->val, val);
            root->lazy = min(root->lazy,val);
            return;
        }

        int mid = start + (end - start) / 2;
        pushDown(root);
        if (l <= mid)
            update(root->left, start, mid, l, r, val);
        if (r > mid)
            update(root->right, mid + 1, end, l, r, val);
        root->val = min(root->left->val, root->right->val);
    }
    void pushDown(Node *root) {
        if (!root->left)
            root->left = new Node();
        if (!root->right)
            root->right = new Node();
		// 不需要向下更新
        if (root->lazy == INT_MAX)
            return;
        //子区间可以进一步向下更新的值
        root->left->lazy = min(root->lazy,root->left->lazy);
        root->right->lazy = min(root->lazy,root->right->lazy);
		
        //更新子区间最小值
        root->left->val = min(root->left->val,root->lazy);
        root->right->val = min(root->right->val,root->lazy);

        root->lazy = INT_MAX;
    }
    int query(Node *root, int start, int end, int l, int r) {
        if (l <= start && end <= r) {
            return root->val;
        }
        int mid = start + (end - start) / 2;
        pushDown(root);
        int ans = INT_MAX;
        if (l <= mid)
            ans = min(ans, query(root->left, start, mid, l, r));
        if (r > mid)
            ans = min(ans, query(root->right, mid + 1, end, l, r));
        return ans;
    }
};
```



# 题目

-   对于表示为「**区间和**」且**对区间进行「加减」的更新操作**的情况，我们在更新节点值的时候『需要✖️左右孩子区间叶子节点的数量 (注意是叶子节点的数量)』；我们在下推懒惰标记的时候『需要累加』！！(这种情况和模版一致！！) **如题目 [最近的请求次数](https://leetcode.cn/problems/number-of-recent-calls/)**
-   对于表示为「**区间和**」且**对区间进行「覆盖」的更新操作**的情况，我们在更新节点值的时候『需要✖️左右孩子区间叶子节点的数量 (注意是叶子节点的数量)』；我们在下推懒惰标记的时候『**不**需要累加』！！(因为是覆盖操作！！) **如题目 [区域和检索 - 数组可修改](https://leetcode.cn/problems/range-sum-query-mutable/)**
-   对于表示为「**区间最值**」且**对区间进行「加减」的更新操作**的情况，我们在更新节点值的时候『**不**需要✖️左右孩子区间叶子节点的数量 (注意是叶子节点的数量)』；我们在下推懒惰标记的时候『需要累加』！！ **如题目 [我的日程安排表 I](https://leetcode.cn/problems/my-calendar-i/)、[我的日程安排表 III](https://leetcode.cn/problems/my-calendar-iii/)**

## 固定范围

### [307. 区域和检索 - 数组可修改](https://leetcode.cn/problems/range-sum-query-mutable/)（固定范围，单点更新）

#### 方法一：链表实现

```c++
class NumArray {
public:
    struct Node{
        Node *left,*right;
        int val;
    };
    Node *root=new Node();
    int n;
    NumArray(vector<int>& nums) {
        this->n=nums.size();
        builtTree(root,0,n-1,nums);
    }

    void builtTree(Node *node,int start,int end,vector<int>& nums){
        if(start==end){
            node->val=nums[start];
            return ;
        }
        if(!node->left) node->left=new Node();
        if(!node->right) node->right=new Node();
        int mid=start+(end-start)/2;
        builtTree(node->left,start,mid,nums);
        builtTree(node->right,mid+1,end,nums);
        node->val=node->left->val+node->right->val;
    }
    
    void update(int index, int val) {
        update(root,0,n-1,index,val);
    }

    void update(Node *node,int start,int end, int index,int val){
        if(start==end){
            node->val=val;
            return;
        }

        int mid=start+(end-start)/2;
        if(index<=mid)
            update(node->left,start,mid,index,val);
        if(index>mid)
            update(node->right,mid+1,end,index,val);
        // 单点修改后更新节点值
        node->val=node->left->val+node->right->val;
    }
    
    int sumRange(int left, int right) {
        return query(root,0,n-1,left,right);
    }
    
    int query(Node *node,int start,int end,int l,int r){
        if(l<=start&&end<=r){
            return node->val;
        }
        int mid=start+(end-start)/2;
        int ans=0;
        if(l<=mid)
            ans+=query(node->left,start,mid,l,r);
        if(r>mid)
            ans+=query(node->right,mid+1,end,l,r);
        return ans;
    }
};
```

#### 方法二：数组实现

```c++
class NumArray {
public:
    int n;
    vector<int> tree;
    NumArray(vector<int>& nums) {
        this->n=nums.size();
        tree.resize(4*n);

        builtTree(0,0,n-1,nums);
    }

    // root节点对应的数组下标索引
    void builtTree(int root,int left,int right,vector<int>& nums){
        if(left==right){
            tree[root]=nums[left];
            return;
        }

        int mid=left+(right-left)/2;
        builtTree(root*2+1,left,mid,nums);
        builtTree(root*2+2,mid+1,right,nums);
        // 更新
        tree[root]=tree[root*2+1]+tree[root*2+2];
    }
    
    void update(int index, int val) {
        update(0,0,n-1,index,val);
    }
    
    void update(int root,int start,int end,int index,int val){
        // 当前root索引代表区间[index,index]
        if(start==end){
            tree[root]=val;
            return;
        }

        int mid=start+(end-start)/2;
        // [index,index]区间在左子树
        if(index<=mid)
            update(root*2+1,start,mid,index,val);
        // 区间位于右子树
        if(index>mid)
            update(root*2+2,mid+1,end,index,val);
        // pushUp
        tree[root]=tree[root*2+1]+tree[root*2+2];
    }
    int sumRange(int left, int right) {
        return query(0,0,n-1,left,right);
    }
    // 当前区间[start,end]，查询区间[l,r]
    int query(int root,int start,int end,int l,int r){
        // 当前区间全部位于查询区间
        if(l<=start&&end<=r){
            return tree[root];
        }

        int mid=start+(end-start)/2;
        int ans=0;
        // [l,r]与[start,mid]有交集
        if(l<=mid)
            ans+=query(root*2+1,start,mid,l,r);
        // [l,r]与[mid+1,end]有交集
        if(r>mid)
            ans+=query(root*2+2,mid+1,end,l,r);
        return ans;
    }
};
```



## 不定范围

#### [729. 我的日程安排表 I](https://leetcode.cn/problems/my-calendar-i/)（动态开点，区间更新）

```c++
class MyCalendar {
    struct Node{
        Node *left,*right;
        int val;
        int add;
    };

public:
 
    int N=1e9;
    Node* root=new Node(); 
    MyCalendar() {

    }
    
    bool book(int start, int end) {
        if(query(root,0,N,start,end-1)!=0)
            return false;
        update(root,0,N,start,end-1,1);
        return true;
    }
    // [l,r]区间+val
    void update(Node* node,int start,int end,int l,int r,int val){
        if(l<=start&&end<=r){
            node->val+=(end-start+1)*val;
            node->add+=val;
            return;
        }

        // 当前节点的懒惰标记向下推
        int mid=start+(end-start)/2;
        pushDown(node,mid-start+1,end-mid);
        // 分为两个区间：[start,mid] [mid+1,end]
        if(l<=mid)
            update(node->left,start,mid,l,r,val);
        if(r>mid)
            update(node->right,mid+1,end,l,r,val);
        pushUp(node);
    }
    void pushUp(Node* node){
        node->val=node->left->val+node->right->val;
    }
    void pushDown(Node* node,int leftNum,int rightNum){
        if(!node->left) node->left=new Node();
        if(!node->right) node->right=new Node();
        // 如果add为0，则没有标记，不需要下推
        if(node->add==0)
            return;

        node->left->val+=leftNum*node->add;
        node->right->val+=rightNum*node->add;

        node->left->add+=node->add;
        node->right->add+=node->add;

        node->add=0;
    }
    // 查询区间未[l,r]，当前区间为[start,end]
    int query(Node* node,int start,int end,int l,int r){
        if(l<=start&&end<=r){
            return node->val;
        }
        int mid=start+(end-start)/2;
        int ans=0;
        pushDown(node,mid-start+1,end-mid);
        if(l<=mid)
            ans+=query(node->left,start,mid,l,r);
        if(r>mid)
            ans+=query(node->right,mid+1,end,l,r);
        return ans;
    }
};
```

#### [715. Range 模块](https://leetcode.cn/problems/range-module/)

> 线段树超时，但是对于track表示范围是否覆盖，lazy定义当前区间操作很有启发意义

```c++
class RangeModule {
  public:
    struct Node {
        Node *left, *right;
        bool track;
        int lazy;           //1表示跟踪，-1表示排除，0表示无操作
    };
    Node *root;
    int N = 1e9;

    void update(Node *root, int start, int end, int l, int r, int val) {
        if (l <= start && end <= r) {
            root->track = (val==1);
            root->lazy=val;
            return;
        }
        int mid = start + (end - start) / 2;
        pushDown(root);
        if (l <= mid)
            update(root->left, start, mid, l, r, val);
        if (r > mid)
            update(root->right, mid + 1, end, l, r, val);

        // 更新root节点表示的区间范围
        root->track = (root->left->track && root->right->track);
    }

    void pushDown(Node *root) {
        if (!root->left)
            root->left = new Node();
        if (!root->right)
            root->right = new Node();
        
        if(root->lazy==0)
            return;

        root->left->track = (root->lazy==1);
        root->right->track = (root->lazy==1);

        root->left->lazy=root->lazy;
        root->right->lazy=root->lazy;

        root->lazy=0;
    }

    bool query(Node *root, int start, int end, int l, int r) {
        if (l <= start && end <= r) {
            return root->track;
        }

        int mid = start + (end - start) / 2;
        pushDown(root);

        bool ans = true;
        if (l <= mid)
            ans = (ans && query(root->left, start, mid, l, r));
        if (r > mid)
            ans = (ans && query(root->right, mid + 1, end, l, r));
        return ans;
    }

    RangeModule() { root = new Node(); }

    void addRange(int left, int right) {
        update(root, 0, N, left, right-1, 1);
    }

    bool queryRange(int left, int right) {
        return query(root, 0, N, left, right-1);
    }

    void removeRange(int left, int right) {
        update(root, 0, N, left, right-1, -1);
    }
};
```





## 列表

- [x] #### [729. 我的日程安排表 I](https://leetcode.cn/problems/my-calendar-i/)（区间更新，动态开点，维护区间和）

- [x] #### [731. 我的日程安排表 II](https://leetcode.cn/problems/my-calendar-ii/)（区间更新，动态开点，维护最大值）

- [x] #### [732. 我的日程安排表 III](https://leetcode.cn/problems/my-calendar-iii/)（区间更新，动态开点，维护最大值）

- [ ] #### [715. Range 模块](https://leetcode.cn/problems/range-module/)









> - 单点修改
> - 区间查询

[线段树入门【力扣双周赛 79】LeetCode_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV18t4y1p736/?spm_id_from=333.788&vd_source=5427cf02c00273188250be648b53eced)

#### [2286. 以组为单位订音乐会的门票](https://leetcode.cn/problems/booking-concert-tickets-in-groups/)

> 数组实现线段树

```c++
class BookMyShow {
public:
    int n,m;
    vector<long> minPeople,sumPeople;
    BookMyShow(int n, int m) {
        this->n=n;
        this->m=m;
        minPeople.resize(4*n);  //区间中一排坐的最少人数
        sumPeople.resize(4*n);  //区间中坐的总人数
    }
    
    // 将idx排的人数增加val
    void add(int o,int l,int r,int idx,int val){
        if(l==r){
            minPeople[o]+=val;
            sumPeople[o]+=val;
            return;
        }
        // 二分
        int mid=l+(r-l)/2;
        if(idx<=mid)
            add(2*o+1,l,mid,idx,val);
        else
            add(2*o+2,mid+1,r,idx,val);
        
        minPeople[o]=min(minPeople[2*o+1],minPeople[2*o+2]);
        sumPeople[o]=sumPeople[2*o+1]+sumPeople[2*o+2];
    }
    // 查询[L,R]之间的区间和
    long query_sum(int o,int left,int right,int L,int R){
        if(L<=left&&right<=R)
            return sumPeople[o];
        int mid=left+(right-left)/2;
        long sum=0;
        if(L<=mid)
            sum+=query_sum(2*o+1,left,mid,L,R);
        if(R>mid)
            sum+=query_sum(2*o+2,mid+1,right,L,R);
        return sum;
    }

    // 返回区间[0,R]中<=val的最小下标，不存在返回-1
    int index(int o,int left,int right,int R,int val){
        if(minPeople[o]>val)
            return -1;
        if(left==right)
            return left;

        int mid=left+(right-left)/2;
        if(minPeople[2*o+1]<=val)
            return index(2*o+1,left,mid,R,val);
        
        if(mid<R)
            return index(2*o+2,mid+1,right,R,val);
        
        return -1;
    }
    vector<int> gather(int k, int maxRow) {
        int i=index(0,0,n-1,maxRow,m-k);
        if(i==-1)
            return {};
        
        int seats=query_sum(0,0,n-1,i,i);
        add(0,0,n-1,i,k);
        return {i,seats};
    }
    
    bool scatter(int k, int maxRow) {
        if((long)(maxRow+1)*m-query_sum(0,0,n-1,0,maxRow)<k)
            return false;
        // 找到第一个有空位置的排
        int i=index(0,0,n-1,maxRow,m-1);

        while(true){
            int left_seats=m-query_sum(0,0,n-1,i,i);
            if(k<=left_seats){
                add(0,0,n-1,i,k);
                return true;
            }
            k-=left_seats;
            add(0,0,n-1,i,left_seats);
            i+=1;
        }
    }
};

/**
 * Your BookMyShow object will be instantiated and called as such:
 * BookMyShow* obj = new BookMyShow(n, m);
 * vector<int> param_1 = obj->gather(k,maxRow);
 * bool param_2 = obj->scatter(k,maxRow);
 */
```

线段树区间合并法解决**多次询问**的「区间最长连续上升序列问题」和「区间最大子段和问题」

#### [53. 最大子数组和](https://leetcode.cn/problems/maximum-subarray/)

> 适用于大规模查询情况

```c++
class Solution {
public:
    // lsum表示[l,r]内以l为左端点的最大子段和
    // rsum表示[l,r]内以r为右端点的最大子段和
    // msum表示[l,r]内的最大子段和
    // isum表示[l,r]的区间和
    struct Status{
        int lsum,rsum,msum,isum;
    };

    Status PushUp(Status l,Status r){
        int isum=l.isum+r.isum;
        int lsum=max(l.lsum,l.isum+r.lsum);
        int rsum=max(r.rsum,l.rsum+r.isum);
        int msum=max(max(l.msum,r.msum),l.rsum+r.lsum);
        return Status{lsum,rsum,msum,isum};
    }
    Status get(vector<int>&nums,int left,int right){
        if(left==right)
            return Status{nums[left],nums[left],nums[left],nums[left]};

        int mid=left+(right-left)/2;
        Status lsub=get(nums,left,mid);
        Status rsub=get(nums,mid+1,right);
        return PushUp(lsub,rsub);
    }
    int maxSubArray(vector<int>& nums) {
        int n=nums.size();
        return get(nums,0,n-1).msum;
    }
};
```

动态开点线段树

#### [6899. 达到末尾下标所需的最大跳跃次数](https://leetcode.cn/problems/maximum-number-of-jumps-to-reach-the-last-index/)







# 疑问

#### [1851. 包含每个查询的最小区间](https://leetcode.cn/problems/minimum-interval-to-include-each-query/)

> 结构体中需要初始化指针为空，前面为什么不需要？代码哪里写的存在问题

```c++
class Solution {
  public:
    int N = 10;
    struct Node {
        Node *left = nullptr, *right = nullptr;
        int val = INT_MAX;
        int lazy = 0;
    };
    vector<int> minInterval(vector<vector<int>> &intervals,
                            vector<int> &queries) {
        Node *root = new Node;

        for (auto &interval : intervals) {
            int l = interval[0], r = interval[1];
            update(root, 0, N, l, r, r - l + 1);
        }
        int n = queries.size();
        vector<int> ans(n, -1);

        for (int i = 0; i < n; i++) {
            int t = query(root, 0, N, queries[i], queries[i]);
            if (t != INT_MAX)
                ans[i] = t;
        }
        return ans;
    }

    void update(Node *root, int start, int end, int l, int r, int val) {
        if (l <= start && end <= r) {
            root->val = min(root->val, val);
            root->lazy = root->val;
            return;
        }

        int mid = start + (end - start) / 2;
        pushDown(root);
        if (l <= mid)
            update(root->left, start, mid, l, r, val);
        if (r > mid)
            update(root->right, mid + 1, end, l, r, val);
        root->val = min(root->left->val, root->right->val);
    }
    void pushDown(Node *root) {
        if (!root->left)
            root->left = new Node();
        if (!root->right)
            root->right = new Node();

        if (root->lazy == 0)
            return;
        root->left->lazy = root->lazy;
        root->right->lazy = root->lazy;

        root->left->val = root->val;
        root->right->val = root->val;

        root->lazy = 0;
    }
    int query(Node *root, int start, int end, int l, int r) {
        if (l <= start && end <= r) {
            return root->val;
        }
        int mid = start + (end - start) / 2;
        pushDown(root);
        int ans = INT_MAX;
        if (l <= mid)
            ans = min(ans, query(root->left, start, mid, l, r));
        if (r > mid)
            ans = min(ans, query(root->right, mid + 1, end, l, r));
        return ans;
    }
};
```

