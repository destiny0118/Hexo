---
title: C语法
categories: 语法
---



# <stdio.h>

- scanf

  > 读取空格、换行符

  ```c
  #include <stdio.h>
  int main() {
      int a;
      char c;
      scanf("%d", &a);
      scanf("%c", &c);
      if (c == '\n')
          printf("****\n");
      if (c == ' ')
          printf("^^^^\n");
      printf("%d %c\n", a, c);
      return 0;
  }
  ```

  ![image-20210406120658645](https://raw.githubusercontent.com/destiny0118/picgo/master/img/202204191133356.png)

  ![image-20210406120721087](https://raw.githubusercontent.com/destiny0118/picgo/master/img/202204191133436.png)

- 

# 数组

## 1. 初始化

> int a[10];	声明，未初始化
>
> int a[10]={0};  //全部初始化为0
>
> int a[10]={1};	//第一个元素为1，后面初始化为0

```c
#include <stdio.h>
#include <windows.h>
int main() {
    int a[10], b[10] = {0}, c[10] = {1};
    for (int i = 0; i < sizeof(a) / sizeof(int); ++i)
        printf("%d ", a[i]);
    printf("\n");

    for (int i = 0; i < sizeof(b) / sizeof(int); ++i)
        printf("%d ", b[i]);
    printf("\n");

    for (int i = 0; i < sizeof(c) / sizeof(int); ++i)
        printf("%d ", c[i]);
    printf("\n");
    system("pause");
}
```

![image-20210411175728807](https://gitee.com/destiny0118/picgo/raw/master/pic/image-20210411175728807.png)

# 表达式

## 1. 赋值表达式

结果输出10

```c
#include <stdio.h>
#include <windows.h>
int main() {
    int a, b, c;
    c = 10;
    printf("%d\n", a = b = c);
    system("pause");
}
```

