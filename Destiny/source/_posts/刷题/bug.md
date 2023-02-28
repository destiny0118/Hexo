---
title: bug
categories: 技术
---



## 数组越界访问（内存非法访问）

- **AddressSanitizer: heap-buffer-overflow on address 0x6020000000bc at pc 0x00000034b263 bp 0x7ffd8d423d50 sp 0x7ffd8d423d48**

  ```c++
  #include <iostream>
  #include <vector>
  #include <numeric>
  using namespace std;
  
  int main()
  {
      vector<int> ratings = {1, 0, 2};
      int size = ratings.size();
      vector<int> ans(size, 1);
      if (size < 2)
          return size;
      for (int i = 0; i < size - 1; i++)
          if (ratings[i + 1] > ratings[i])
              ans[i + 1] = ans[i] + 1;
      cout << ratings[size] << endl;
      //i=size-1
      for (int i = size; i > 0; i--)
          if (ratings[i - 1] > ratings[i] && ans[i - 1] <= ans[i])
              ans[i - 1] = ans[i] + 1;
      int sum = accumulate(ans.begin(), ans.end(), 0);
      // return sum;
      cout << sum << endl;
      return 0;
  }
  ```

  

# invalid operands to binary expression

> [二进制](https://so.csdn.net/so/search?q=二进制&spm=1001.2101.3001.7020)表达式的操作数无效
> 顾名思义 错误出在操作符上 对类型的操作问题