# STL
1. Sequence Containers：顺序容器
	1. vector：动态数组，O(1)的随机存取。在尾部增删的复杂度是O(1)，可以用作stack。
	2. list：双向链表，不支持随机存取。
	3. deque：双端队列，支持O(1)的随机存取，O(1)时间的头部增删和尾部增删。
	4. array：固定大小的数组
	5. forward_list：单向链表
2. Container Adaptors：基于其它容器实现的数据结构
	1. stack：后入先出（LIFO），默认基于deque实现。stack常用于深度优先搜索，字符串匹配以及单调栈问题
	2. queue：先入先出（FIFO），默认基于deque实现，常用于广度优先搜索。
	3. priority_queue：最大值先出(默认)的数据结构，默认基于vector实现堆结构，可以在O(nlogn)时间排序数组，O(logn)插入值，O(1)时间获取最大值，O(logn)删除最大值。
3. Associative Containers：实现了排好序的数据结构
	1. set：有序集合，元素不可重复，底层默认为==红黑树==，即一种特殊的二叉查找树(BST)。在O(nlogn)时间排序数组，O(logn)时间插入、删除、查找任意值，O(logn)时间获得最大值或最小值。priority_queue不支持删除==任意值==。
	2. multiset：支持重复元素的set
	3. map：有序映射或有序表，在set的基础上加上映射关系，可以对每个元素key存一个value
	4. multimap：支持重复元素的map
4. Unordered Associative Containers：对每个Associative Containers实现哈希版本
	1. unordered_set：哈希集合，可以在O(1)的时间快速插入、查找、删除元素，常用于快速的查询一个元素是否在这个容器内。
	2. unordered_multiset：支持重复元素的unordered_set
	3. unordered_map：哈希映射或和哈希表，在unordered_set的基础上加上映射关系，可以对每一个元素key存一个值value。在某些情况下，如果key的范围已知且较小，可以用==vector==代替unordered_map，用位置表示key，且每个位置的值表示value
	4. unordered_multimap：支持重复元素的unordered_map

# set、multiset
[C++ STL multiset容器详解 (biancheng.net)](http://c.biancheng.net/view/7203.html)
multiset可以存储int, string, struct, class等类型，对于自定义类型需要实现比较函数
```c++
struct Node{
	int x,y;
};

struct cmp{
	bool operator()(const Node&a, const Node&b){
	return a.x==b.x?a.y<b.y:a.x<b.x;
	}
};

multiset<Node,cmp> s;
```

| 成员方法         | 功能                                                                                                                                |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| begin()          | 返回指向容器中第一个（注意，是已排好序的第一个）元素的双向迭代器。                                                                  |
| end()            | 返回指向容器最后一个元素（注意，是已排好序的最后一个）所在位置后一个位置的双向迭代器                                                |
| rbegin()         | 返回指向最后一个（注意，是已排好序的最后一个）元素的反向双向迭代器。                                                                |
| rend()           | 返回指向第一个（注意，是已排好序的第一个）元素所在位置前一个位置的反向双向迭代器。                                                  |
| cbegin()         | 和 begin() 功能相同，只不过在其基础上，增加了 const 属性，不能用于修改容器内存储的元素值。                                          |
| cend()           | 和 end() 功能相同，只不过在其基础上，增加了 const 属性，不能用于修改容器内存储的元素值。                                            |
| crbegin()        | 和 rbegin() 功能相同，只不过在其基础上，增加了 const 属性，不能用于修改容器内存储的元素值。                                         |
| crend()          | 和 rend() 功能相同，只不过在其基础上，增加了 const 属性，不能用于修改容器内存储的元素值。                                           |
| find(val)        | 查找值为 val 的第一个元素，如果成功找到，则返回指向该元素的迭代器；反之，则返回end() 方法一样的迭代器。                             |
| lower_bound(val) | 返回第一个大于或等于 val 的元素的迭代器，即元素位置。                                                                               |
| upper_bound(val) | 返回第一个大于 val 的元素的迭代器。                                                                                                 |
| equal_range(val) | 该方法返回一个 pair ，(pair.first,pair.second)=(lower_bound(),upper_bound())，表示值为val的范围。                                   |
| empty()          | 若容器为空，则返回 true；否则 false。                                                                                               |
| size()           | 返回当前 multiset 容器中存有元素的个数。                                                                                            |
| max_size()       | 返回 multiset 容器所能容纳元素的最大个数                                                        |
| insert(val)      | 向 multiset 容器中插入元素。                                                                                                        |
| erase(iter)      | 删除迭代器所指位置元素，无返回值                                                                                                                                    |
| erase(val)       | 删除值为val的所有元素，返回删除个数                                                                                                 |
| swap()           | 交换 2 个 multiset 容器中存储的所有元素。这意味着，操作的 2 个 multiset 容器的类型必须相同。                                        |
| clear()          | 清空 multiset 容器中所有的元素，即令 multiset 容器的 size() 为 0。                                                                  |
| emplace()        | 在当前 multiset 容器中的指定位置直接构造新元素。其效果和 insert() 一样，但效率更高。                                                |
| emplace_hint()   | 和emplace()构造新元素的方式是一样的，不同之处在于，使用者必须为该方法提供一个指示新元素生成位置的迭代器，并作为该方法的第一个参数。 |
| count(val)       | 查找值为 val 的元素的个数，并返回。                                                                                                 |

       c++语言中，multiset是<set>库中一个非常有用的类型，它可以看成一个序列，插入一个数，删除一个数都能够在O(logn)的时间内完成，而且他能时刻保证序列中的数是有序的，而且序列中可以存在重复的数。

### 简单应用：

通过一个程序来看如何使用multiset：
```c++
#include <string>  
#include <iostream>  
#include <set>  
using namespace std;  
void main(){      
    int x;  
    scanf("%ld",&x);      
    multiset<int>h;            
    while(x!=0){          
        h.insert(x);           
        scanf("%ld",&x);      
    }      
    while(!h.empty()){                 
        __typeof(h.begin()) c=h.begin();  
        printf("%ld ",*c);             
        h.erase(c);                
    }  
}
```
> 对于输入数据：32 61 12 2 12 0,该程序的输出是：2 12 12 32 61。  

### 放入自定义类型的数据：

不只是int类型，multiset还可以存储其他的类型诸如 string类型，结构(struct或class)类型。而我们一般在编程当中遇到的问题经常用到自定义的类型，即struct或class。例如下面的例子：
```c++
struct rec{int x,y;};  
multiset<rec>h;

struct cmp{  
    bool operator()(const rec&a,const rec&b{  
    return a.x<b.x||a.x==b.x&&a.y<b.y;    }  
};
					
struct rec{int x,y;};  
struct cmp{bool operator()(const rec&a,const rec&b){return a.x<b.x||a.x==b.x&&a.y<b.y;    }};  
multiset<rec,cmp>h;
```


不过以上的代码是没有任何用处的，因为multiset并不知道如何去比较一个自定义的类型。怎么办呢？我们可以定义multiset里面rec类型变量之间的小于关系的含义（这里以x为第一关键字为例），具体过程如下：

定义一个比较类cmp，cmp内部的operator函数的作用是比较rec类型a和b的大小(以x为第一关键字，y为第二关键字)，告诉了序列h如何去比较里面的元素(重载运算符)


# set、unordered_set区别
```c++
// unordered_set<pair<int, int>> test;   # error: use of deleted function function
set<pair<int, int>> record, gu; // 记录边
```

# vector
| 成员方法                           | 功能             |
| ------------------------------ | -------------- |
| nums.erase(nums.begin()+index) | 删除下标为index处的元素 |
# array

| 成员方法 | 功能 |
| -------- | ---- |
| array<int, 26> cnt = {};         | 初始化为0，没有{}则为默认初始化     |



# string
>cin：遇到空格结束读取
>getline(cin, line)：读取一行（包括换行符），line不包括换行符


to_string
stoi

| 成员方法                           | 功能                       | 头文件       |
| ------------------------------ | ------------------------ | --------- |
| s.push_back(ch)                | 在后面添加一个字符                |           |
| s.append(args)                 | 参数字符串可以是string，也可以是c_str |           |
| s.append(args,cnt)             | 从args起始位置添加cnt个字符        |           |
| s.substr(pos,n)                | 截取字符串，从pos位置开始截取n个字符     |           |
| count(s.begin(), s.end(), 'A') | 字符计数                     | algorithm |
| reverse(s.begin(),s.end())     | 字符串翻转                    | algorithm |

## 字符相关函数
>#include <ctype.h>

| cctype                                                                                                |     |
| ----------------------------------------------------------------------------------------------------- | --- |
| ![image-20221029205740860](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2013/202303291127460.png) |     |

## 根据空格分割字符串
```c++
string line="this student is studious";
stringstream ss;
string w;
ss<<line;
while(ss>>w){
	cout<<w<<endl;
}
```


# priority_queue
```c++
priority_queue<int> pq;


//自定义排序，小根堆
struct Comp{
    bool operator()(ListNode* a,ListNode *b){
        return a->val>b->val;
    }
 };
priority_queue<ListNode*,vector<ListNode*>,Comp> pq;

```

| 成员方法  | 功能         |
| --------- | ------------ |
| pq.push(val) | 插入一个元素 |
| pq.pop()     | 弹出最值     |
| pq.top()     | 返回最值             |



# algorithm
## sort
```c++
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
bool compare(vector<int> &a, vector<int> b)
{
    return a[1] < b[1];
}

int main(){
	//自定义排序
	vector<vector<int>> nums={{1, 2}, {2, 4}, {1, 3}};
	sort(nums.begin(),nums.end(),[](auto &a,auto &b){
		return a[1]<b[1];
	});
	//定义排序函数
	sort(nums.beign(),nums.end(),compare);

	sort(nums.begin(), nums.end(), greater<pair<int,int>>());从大到小排序
	
	vector<int> nums;
	sort(nums.begin(),nums.end(),greater<int>());
}
```
  [(6条消息) 浅析C/C++中sort函数的用法_WHEgqing的专栏-CSDN博客](https://blog.csdn.net/WHEgqing/article/details/42455705?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2~default~CTRLIST~default-1.no_search_link&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2~default~CTRLIST~default-1.no_search_link)


## lower_bound
查找大于或者等于x的第一个位置