---
title: C++语法
tags:
  - STL
categories:
  - 语法
description: C++标准模板库（Standard Template Library)的使用方法。
top: 5
---



# tips

> \#define pii pair<int, int>
>
> 
>
> typedef pair<int, string> pis;

- memset进行数组初始化

  http://c.biancheng.net/view/231.html

# 数据范围

![20201215210918150](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2013/202303271603594.png)

$INT\_MAX=2147483647=2^{31}-1<3\times 10^9$

$LONG\_MAX=2147483647$

**INT_MIN:-2147483648
INT_MAX:2147483647
LONG_MIN:-2147483648
LONG_MAX:2147483647
LLONG_MIN:-9223372036854775808
LLONG_MAX:9223372036854775807**

> int : 4字节
>
> long: 4字节

> 直接初始化：不使用=来初始化
>
> 间接初始化：使用=初始化，新创建的object是初始化initializer的复制

## malloc和new的区别

> new在分配内存时会调用默认的构造函数，而malloc不会调用。
>
> 而STL的string在赋值之前须要调用默认的构造函数以初始化string后才干使用。

[C++ string中的几个小陷阱，你掉进过吗？](http://www.bubuko.com/infodetail-2094709.html)

<center>
    <img src="https://cdn.jsdelivr.net/gh/destiny0118/picgo/img/202206021813578.png" width=45%/>
    <img src="https://cdn.jsdelivr.net/gh/destiny0118/picgo/img/202206021814398.png" width=45%/>
 </center>






捆绑赋值

```c++
tie{a,b}=b,c
```

位运算库函数

```c++
__builtin_popcount(k)	//计算k二进制表示中1的个数

iota(nums.begin(),nums.end(),0)    //[0,1,2,3,...]
//根据nums1对num排序
sort(nums.begin(),nums.end(),[&](int a,int b){
return nums1[a]<nums1[b];
});
```

匿名表达式
```cpp
 auto calMax = [](vector<int> &arr, int boardr) -> int {
};
```




## 头文件

[C++中头文件（.h）和源文件（.cpp）都应该写些什么](https://www.cnblogs.com/fenghuan/p/4794514.html)

## unordered_map和map区别

>  map<pair<int,int>,int> used; 可以
>
> unordered_map<pair<int,int>, int> used  不可以
>
>  unordered_map<int, set\<int\>> ms;可以

# 运算符

[C的|、||、&、&&、异或、~、！运算符_C 语言_脚本之家 (jb51.net)](https://www.jb51.net/article/50569.htm)

| 运算符    | 操作              |                             |
| --------- | ----------------- | --------------------------- |
| ^（异或） | 2(10)^3(11)=1(01) | **1^1=0 1^0=1 0^1=1 0^0=1** |
| && 逻辑与 |                   |                             |

## 数学

- cmath
  - pow(a,b)，计算$a^b$
- 

# 常用函数

| 操作                                 | 说明                                                         | 头文件      |
| ------------------------------------ | ------------------------------------------------------------ | ----------- |
| 寻找大于指定值的第一个下标           | vector\<int\> nums = {1, 2, 3, 4, 5, 6, 7, 8, 9};<br>cout << upper_bound(nums.begin(), nums.end(), 5) - nums.begin() << endl;<br>输出为下标5 | algorithm.h |
| 第三个参数表明累加类型，避免结果越界 | accumulate(beans.begin(),beans.end(),(long long)0);<br>accumulate(beans.begin(),beans.end(),0); |             |



| 操作                        | 说明                                       | 头文件 |
| --------------------------- | ------------------------------------------ | ------ |
| 找到第一个大于等于val的位置 | lower_bound(nums.begin(), nums.end(), val) |        |



# 类型转换

[(7条消息) C++字符串转换（stoi；stol；stoul；stoll；stoull；stof；stod；stold）_WilliamX2020的博客-CSDN博客_c++ stod函数](https://blog.csdn.net/baidu_34884208/article/details/88342844)

## int ->string

```c++
int a=10;
string b=to_string(a);
```



|          | vector                             | stack | queue |
| -------- | ---------------------------------- | ----- | ----- |
| 修改大小 | vector\<int\> tmp;  tmp.resize(n); |       |       |
|          |                                    |       |       |
|          |                                    |       |       |

## string->int

![202203261127974](https://cdn.jsdelivr.net/gh/destiny0118/picgo/img/202206021019131.png)

# iostream

> cin：遇到空格结束读取
>
> getline：读取一行，遇到换行符结束，读取换行符，但最后的换行符不存入string中

[c++string标准输入和getline()整行读入](https://www.cnblogs.com/suchang/p/10547671.html)

>s.size()返回字符串长度，s.size()-1是最后一个字符，s.size()是字符串结束标记`\0`

  ```c++
  #include <iostream>
  #include <string>
  using namespace std;
  int main() {
      string s;
      while (getline(cin, s)) {
          cout << s << endl;
          cout << s.size() << endl;
          cout << s[s.size() - 1] << " " << s[s.size()] << endl;
          if (s[s.size()] == '\0')
              cout << "***" << endl;
      }
      return 0;
}
  ```

  ![image-20210406114903198](https://cdn.jsdelivr.net/gh/destiny0118/picgo/img/202204191123698.png)

  > 从流中读取内容
  >
  > 文件流\<fstream>

  ```c++
   unordered_map<string, int> keywords;
      int i = 0;
      string keyword;
      ifstream inKeyword("lap2/keywords.txt", ios::in);
      while (getline(inKeyword, keyword)) {
          // cout << keyword << endl;
          keywords[keyword] = i;
          ++i;
      }
  
  ```

  
# numeric

- accumulate

  >accumulate带有**三个形参**：头两个形参指定要**累加的元素范围**，第三个形参则是**累加的初值**。
  >
  >accumulate函数将它的一个内部变量设置为指定的初始值，然后在此初值上累加输入范围内所有元素的值。accumulate算法返回累加的结果，**其返回类型就是其第三个实参的类型**。

# list

> list 不是顺序放bai在内存里的，一定要遍历一次；
>
> list容器不提供 at() 和 操作符 operator[] ，对容器中元素的bai访问有些不便，但是我们可以使用迭代器进行元素的访问

# string

## string vs C-Style string

```c++
// 字符数组可以作为一个操作数或+=的右操作数
string s;
s+="abc";
s=s+"13124"

// string转化为字符数组
char *str=s.c_str()    
    
    
//替换字符串，range is either an index and a length or a pair of iterators into s
s.replace(range,args)
s.replace(int pos,int n,const char* s) //替换从pos开始的n个字符为字符串s
s.find(ch)==npos
```

![image-20230228160251850](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202302281602910.png)

![image-20230228165049715](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202302281650772.png)





```C++
int main
{
    //Cstring与string转换
    string s;
    CString c_s;
    c_s=s.c_str();

    //删除最后一个字符
    string str="123456";
    str=str.substr(0,str.size() - 1)
    str.erase(str.end()-1)
    str.pop_back()
}
```

accumulate


## 初始化

## 2. 操作

>s.find("a") 在s中查找a
>
>s.find("a",k) 在s中从位置k查找a
>



## 4. append方法

- 添加c类型字符串

> 输出hello world
>
> 函数中的指针并没有因为被释放而影响到与原字符串的链接，那append是如何附加的呢？

    void append_test(string &s) {
        const char *t = "world";
        s.append(t);
    }
    int main() {
        // string s;
        // char a = 'a';
        // s = (string)a;
        // cout << s << endl;
    
        string a = "hello ";
        append_test(a);
        cout << a << endl;
        getchar();
        return 0;
    }

- 添加c字符串的一部分

  ```c++
  string s=”hello “；const char *c = “out here “;
  s.append(c,3); // 把c类型字符串s的前n个字符连接到当前字符串结尾
  s = “hello out”;
  s.append(c,4,4);//从第4个字符数4个
  s="hello here"
  ```

- 向string后添加多个字符

    ```c++
    string s1 = "hello";
    s1.append(4, '!'); //在当前字符串结尾添加4个字符!
    
    s1="hello!!!!"
    ```

- 添加string

  ```c++
  string s1 = “hello “; string s2 = “wide “; string s3 = “world “;
  s1.append(s2); s1 += s3; //把字符串s连接到当前字符串的结尾
  s1 = “hello wide “; s1 = “hello wide world “;
  ```

- 添加string一部分

  ```c++
  string s1 = “hello “, s2 = “wide world “;
  s1.append(s2, 5, 5); ////把字符串s2中从5开始的5个字符连接到当前字符串的结尾
  s1 = “hello world”;
  string str1 = “hello “, str2 = “wide world “;
  str1.append(str2.begin()+5, str2.end()); //把s2的迭代器begin()+5和end()之间的部分连接到当前字符串的结尾
  str1 = “hello world”;
  ```



# vector(不定长数组)

push_back和emplace_back区别

[vector中emplace_back方法的用途 - 马谦的博客 (dyxmq.cn)](https://www.dyxmq.cn/program/code/c-cpp/emplace-back.html)

[(7条消息) C++容器vector的数组片段截取操作_stitching的博客-CSDN博客_vector截取一部分](https://blog.csdn.net/qq_40250056/article/details/114681940?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2~default~CTRLIST~Rate-1.pc_relevant_default&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2~default~CTRLIST~Rate-1.pc_relevant_default&utm_relevant_index=1)

```c++
//比较相等
a==b
```



| 操作           | vector: nums                                                 | 头文件                            |
| -------------- | ------------------------------------------------------------ | --------------------------------- |
| 初始化         | vector\<int\> num(10);//含有10个int元素的vector<br/>vector\<int\> num(10,0);//10个元素，初始化为0<br/>vector\<int> a = {1, 2, 3};//含3个元素，1、2、3 |                                   |
| 在头部添加元素 | nums.insert(nums.begin(), 1)                                 |                                   |
|                | {0} ——>{1,0}                                                 |                                   |
| 在尾部添加元素 | nums.push_back(2)                                            |                                   |
| 切片           | vector\<it> a<br/>vector\<int> b<br/>b.assign(a.begin(), a.end()) |                                   |
| 反转           | reverse(num.begin(), num.end())                              | algorithm                         |
| 取最大元素     | max_element(v.begin(), v.end())        返回vector\<type>::iterator类型 |                                   |
|                | resize                                                       | 容量增大的话，值由给定的val初始化 |
|                | assign                                                       |                                   |
| 比较相等       | a==b                                                         |                                   |

clear：清空元素，size变为0

## 2. 添加数据

>num.push_back();//向尾部添加数据
>
>num.pop_back();//删除最后一个元素，无返回值

## 3. 访问数据

> num.empty()  //为空返回1，判断vector是否为空
>
> num[i]
>
> num.at(i)
>
> num.back() 访问最后一个值，不删除

## 4. 压缩

> num.size()
>
> num.resize()
>
> num.clear()

## 5. 最大值、最小值

> 头文件 \<algorithm\>

## 6. 边界测试

```c++
 	vector<int> b(5);
    for (int i = 0; i < b.size(); i++)
        cout << b[i] << " ";
    cout << endl;
    b[5] = 100;
    cout << b[5] << endl;
```

> 0 0 0 0 0
>
> 100

在main函数中没有提醒，在子函数中会出现如下错误，子函数返回时变量b空间需要被释放

<img src="https://gitee.com/destiny0118/picgo/raw/master/20210521230255.png" alt="image-20210521230248016" style="zoom: 50%;" />

## 7. 截取一部分

截取a的一部分到b的后面，a与b同类型

```c++
 vector<int> a = {1, 2, 4, 5, 6}, b;
 vector<vector<int>> c;
 copy(begin(a) + 0, begin(a) + 2, back_inserter(b));
 c.push_back(b);
 for (int t : c[0])
     cout << t << " " << endl;
```

> 1 2

## 8. 去除vector重复元素

```c++
vector<vector<int>> ans;
set<vector<int>> setVec(ans.begin(), ans.end());
ans.assign(setVec.begin(), setVec.end());
```

## 9. 查找指定值是否存在

```c++
#include <algorithm>
vector<int> ans;
if(find(ans.begin(),ans.end(),value)==ans.end())//查找值不存在
```



## 测试代码

```c++
vector.cpp
    
#include <algorithm>
#include <iostream>
#include <vector>
using namespace std;
int main() {
    vector<int> num(10, 1);
    for (int i = 0; i < num.size(); i++) {
        if (i == 0)
            cout << num[i];
        else
            cout << "\t" << num[i];
    }
    cout << endl;
    cout << num.size() << endl;

    num.push_back(123);
    cout << num[10] << endl;

    // num.pop_back();

    // cout << num.empty() << endl;

    vector<int>::iterator itMax = max_element(num.begin(), num.end());
    vector<int>::iterator itMin = min_element(num.begin(), num.end());

    cout << "最大值：" << *itMax << "\t" << distance(num.begin(), itMax)
         << endl;
    cout << "最小值：" << *itMin << "\t" << distance(num.begin(), itMin)
         << endl;

    cout << *max_element(num.begin(), num.end()) << endl;
    cout << max_element(num.begin(), num.end()) - num.begin() << endl;
    system("pause");
    return 0;
}
```

![image-20210409152320212](https://gitee.com/destiny0118/picgo/raw/master/pic/image-20210409152320212.png)

# set(集合)

| 操作             | unordered_set\<int\> nums1; | set\<int\> nums2; |
| ---------------- | --------------------------- | ----------------- |
| 插入元素         | nums1.insert(10);           |                   |
| 判断元素是否存在 | nums1.find(10)!=nums1.end() |                   |
|                  | nums1.count(10)             |                   |
|                  |                             |                   |
|                  |                             |                   |



## 1. 插入元素

> s.insert("A"); //插入元素
>
> pair<iterator,bool> insert (const value_type& val);
>
> 返回布尔对以表示是否发生插入，如果重复插入一个元素会返回false。返回迭代器指向新插入元素

> set_union()
>
> set_intersect()
>
> set\<string\> s;
>

```c++
//遍历集合中元素
for (string s : identifier)
		cout << s << endl;
	set<string>::iterator it;
	for (it = identifier.begin(); it != identifier.end(); it++)
		cout << *it << endl;
```

| map                         | unordered_map                                   |
| --------------------------- | ----------------------------------------------- |
| map<pair<int,int>,int> used | unordered_map<pair<int,int>,int> used（不可以） |



# map(映射)

[C++判断map中key值是否存在](https://blog.csdn.net/u013095333/article/details/89322198)

声明：

> map<string,int> count;

# unordered_map

| 操作         |      |      |
| ------------ | ---- | ---- |
| test.count() |      |      |
| test.find()  |      |      |

```c++
unordered_map<int,int> cnt;
ans+=cnt[x]  会插入不存在的点
pair<int,int>不能作为建
```
使用 `[]` 运算符对 `unordered_map` 中的键进行访问时，如果该键不存在，会自动创建该键，并关联一个默认值（对于内置类型，这个默认值是0或者空，对于自定义类型可能是默认构造函数创建的对象）。


遍历容器

[(10条消息) c++ unordered_map4种遍历方式_菊头蝙蝠的博客-CSDN博客_遍历unordered_map](https://blog.csdn.net/qq_21539375/article/details/122003559)

```c++
/*
    遍历每一个元素
 */
void print_ele(unordered_map<int, int> &pos) {
    for(const auto &x:pos){
        int key=x.first,value=x.second;
    }
    
    // 结构化绑定 c++17
    for (auto [key, value] : pos)
        cout << key << ":" << value << endl;

    // 只使用键
    for (auto [key, _] : pos)
        cout << key << endl;

    // 迭代器遍历
    for (auto iter = pos.begin(); iter != pos.end(); iter++) {
        cout << iter->first << " " << iter->second << endl;
    }

    for (unordered_map<int, int>::iterator it = map.begin(); it != map.end();
         it++) {
        cout << it->first << it->second << endl;
    }
}
```



> map.find(key) == map.end()    查找的key不在map中

```c++
#include <iostream>
#include <unordered_map>
#include <vector>
using namespace std;
int main() {
    unordered_map<int, int> test = {{1, 3}};
    cout << test[1] << endl;
    cout << test[2] << endl;
    test[3]++;
    cout << test[3] << endl;
    test[4];
    cout << test[4] << endl;
    getchar();
}
```

![image-20210515104044254](https://gitee.com/destiny0118/picgo/raw/master/20210515104051.png)

# stack(栈)

声明：

> stack\<int\> s;

操作：

> s.push(10)：返回栈顶元素的引用
>
> s.pop()：无返回值，弹出栈顶元素，栈为空时不抛出异常
>
> s.empty()：返回一个bool值表示栈是否为空
>
> int t=s.top()：取栈顶元素，在栈为空时调用会出现`Segmentation fault`

# queue(队列)：先进先出

> queue\<int\> s
>
> s.push(int a)
>
> s.pop()
>
> s.empty()
>
> t=s.front()：取队首元素（不删除）
>
> 
>
>  priority_queue<pair<int,int>,vector<pair<int,int>>,greater<>> pq;
>
>  pq.push({0,0});
>
> pq.push(0,0);  //错误
>
> pq.emplace(0,0)

## [C++ Vector转Set与Set转Vector](https://www.cnblogs.com/xwxz/p/13323712.html)

```c++
#include<set>
#include<vector>
#include<iostream>
using namespace std;


int main()
{
    vector<int> vec;
    vec = { 1, 2, 3, 4, 8, 9, 3, 2, 1, 0, 4, 8 };
    set<int> st(vec.begin(), vec.end());
    vec.assign(st.begin(), st.end());

    vector<int>::iterator it;
    for (it = vec.begin(); it != vec.end(); it++)
        cout << *it<<endl;
    
    return 0;
}
```

## 输入输出

c++标准库函数，有两种形式

- 头文件\<istream\>中输入流成员函数

- \<string>中的普通函数

  getline(cin,s) //读取换行符并丢弃，不保存到s中

  ```c++
  istream& getline (istream&  is, string& str, char delim);
  istream& getline (istream&& is, string& str, char delim);
  istream& getline (istream&  is, string& str);
  istream& getline (istream&& is, string& str);
  /*
  	读取delim分隔符并丢弃，不保存到str中
  	is ：表示一个输入流，例如 cin。
  	str ：string类型的引用，用来存储输入流中的流信息。
  	delim ：char类型的变量，所设置的截断字符；在不自定义设置的情况下，遇到’\n’，则终止输入
  */
  ```

  ```c++
  //读取一行存放在字符串中，将字符串变为输入流，再进行处理
  getline(cin, str, '\n');
  stringstream ss(str);
  ```

  ## array
  
  ```c++
  #不能用另一个数组初始化一个数组，不能将一个数组赋值给一个数组
  int a[]={0,1,2};
  int a2[]=a;
  a2=a;
  ```
  
  
  
  迭代器并不是都可以进行加减
  迭代器实质上是一个指针，但是，并不是所有的容器的迭代器可以支持加减操作。
  
  能进行算术运算的迭代器只有随机访问迭代器，要求容器元素存储在连续内存空间内，
  即vector、string、deque的迭代器是有加减法的；
  而map、set、multimap、multiset、list的迭代器是没有加减法的。他们仅支持++itr、–itr这些操作。