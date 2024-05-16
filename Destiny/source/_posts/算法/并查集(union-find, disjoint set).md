
# 简洁写法
```c++
class UnionFind{
    vector<int> id;
public:
    UnionFind(int n):id(n){
        iota(id.begin(),id.end(),0);
    }
    // 找到p的父节点
    int find(int p){
        while(p!=id[p])
            p=id[p];
        return p;
    }
    // 在p和q之间添加边
    void connect(int p,int q){
        id[find(p)]=find(q);
    }
    // p和q是否同属于一个集合
    bool isConnect(int p,int q){
        return find(p)==find(q);
    }
};
```

# 路径压缩+按秩合并
[1319. 连通网络的操作次数 - 力扣（LeetCode）](https://leetcode.cn/problems/number-of-operations-to-make-network-connected/solutions/101780/lian-tong-wang-luo-de-cao-zuo-ci-shu-by-leetcode-s/)（模板，更清晰）
```c++
class UnionFind {
    vector<int> id, size;

  public:
	//把数组初始化为0到n-1
    UnionFind(int n) : id(n), size(n, 1) { iota(id.begin(), id.end(), 0); }

    // 路径压缩
    int find(int p) {
        // p的父节点
        while (id[p] != p) {
            id[p] = id[id[p]];
            p = id[p];
        }
        return p;
    }

    void connect(int p, int q) {
        int i = find(p), j = find(q);
        if (i != j) {
            // 按秩合并，把元素数少的集合合并到元素数多的集合
            if (size[i] > size[j]) {
                id[j] = i;
                size[i] += size[j];
            } else {
                id[i] = j;
                size[j] += size[i];
            }
        }
    }

    bool isConnect(int p, int q) { return find(p) == find(q); }
    // 返回由多少个独立的集合
    int getCnt() {
        int cnt = 0;
        for (int i = 0; i < id.size(); i++) {
            if (id[i] == i)
                cnt++;
        }
        return cnt;
    }
};
```


# 删除边

[并查集优化——树上删除操作 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/582511962)
[并查集-并查集的删除操作 - 僚机 - 博客园 (cnblogs.com)](https://www.cnblogs.com/wingman/p/10387069.html)
[并查集——简单易懂（内附并查集删除操作）_可删并查集-CSDN博客](https://blog.csdn.net/qq_41694395/article/details/79253047)