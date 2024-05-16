---
title: UVA
tags: 数论
categories: 算法
description: UVA题目
---



# UVA

## uva136

> 将数存入集合中（忽略集合中已经存在的数）

```c++
#include <iostream>
#include <set>
using namespace std;

int main()
{
    set<long long int> s;
    s.insert(1);
    set<long long int>::iterator it;
    it = s.begin();
    for (int i = 0; i < 1500; i++)
    {
        long long int num = *it;
        if (!s.count(num * 2))
            s.insert(num * 2);
        if (!s.count(num * 3))
            s.insert(num * 3);
        if (!s.count(num * 5))
            s.insert(num * 5);
        *it++;
    }
    int i = 0;
    for (it = s.begin(); it != s.end(); it++)
    {
        ++i;
        if (i == 1500)
            cout << "The 1500'th ugly number is " << *it << ".\n";
        // cout << ++i << "***" << *it << endl;
    }

    return 0;
}
```

> 优先队列：
>
> push()
>
> pop()
>
> top():取首元素
>
> 越小的整数优先级越大	priority_queue< int, vector <int>, greater <int> > pq;
>
> 越小的整数优先级越低	priority_queue<int> pq;

```c++
// UVa136 Ugly Numbers
// Rujia Liu
#include<iostream>
#include<vector>
#include<queue>
#include<set>
using namespace std;
typedef long long LL;
const int coeff[3] = {2, 3, 5};

int main() {
  priority_queue<LL, vector<LL>, greater<LL> > pq;
  set<LL> s;
  pq.push(1);
  s.insert(1);
  for(int i = 1; ; i++) {
    LL x = pq.top(); pq.pop();
    if(i == 1500) {
      cout << "The 1500'th ugly number is " << x << ".\n";
      break;
    }
    for(int j = 0; j < 3; j++) {
      LL x2 = x * coeff[j];
      if(!s.count(x2)) { s.insert(x2); pq.push(x2); }
    }
  }
  return 0;
}
```

## uva400 Unix ls命令

```c++
#include <iostream>
#include <set>
#include <string>
#include <vector>
using namespace std;

void print(string &f, int len)
{
    int f_len = f.length();
    cout << f;
    for (int i = 0; i < len - f_len; i++)
        cout << ' ';
    // cout << endl;
}
int main()
{
    freopen("ch5/5-2-8.in", "r", stdin);
    freopen("ch5/5-2-8.out", "w", stdout);
    int n;
    while (scanf("%d", &n) == 1 && n)
    {
        set<string> filename;
        vector<string> result;
        int max_size = 0; //文件最大长度
        for (int i = 0; i < 60; i++)
            cout << '-';
        cout << endl;

        for (int i = 0; i < n; i++)
        {
            string temp_file;
            cin >> temp_file;
            if (temp_file.length() > max_size)
                max_size = temp_file.length();
            filename.insert(temp_file);
        }

        int col = (60 + 2) / (max_size + 2); //最大列数
        // cout << "col= " << col << endl;
        result.assign(filename.begin(), filename.end());
        int r = n / col;
        if (n % col)
            r++;
        // cout << "row=" << r << endl;
        // cout << "max_size=" << max_size << endl;
        for (int i = 0; i < r; i++)
        {
            for (int j = 0; j < col; j++)
            {
                if (j * r + i >= result.size())
                    continue;
                if (j == col - 1)
                    print(result[j * r + i], max_size);
                else
                    print(result[j * r + i], max_size + 2);
            }
            cout << endl;
        }
    }
}
```

