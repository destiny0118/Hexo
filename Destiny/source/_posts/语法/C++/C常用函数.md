---
title: c常用函数
categories: 语法
tags: 技术
---
# 1. sizeof

> sizeof返回占用的字节数，但数组作为参数传递时不能有效计算占用的字节数，需加参数指明占用的字节数

```c
#include <stdio.h>
int sum(int a[]) {
    int ans = 0;
    int len = sizeof(a);
    printf("len: %d\n", len);
    printf("%d\n", a[2]);
    return len;
}
int main() {
    int a[3] = {1, 2, 3};
    printf("%d\n", sizeof(a));
    sum(a);
    return 0;
}
```

![image-20210406120005828](https://gitee.com/destiny0118/picgo/raw/master/pic/image-20210406120005828.png)

# 2. malloc realloc

> 头文件： stdlib.h

[malloc/calloc/realloc之间区别详述](https://blog.csdn.net/qq_38810767/article/details/85265541)

> realloc后，申请空间比原来大，重新分配地址空间

```c
#include <stdio.h>
#include <stdlib.h>
int main() {
    int *a = (int *)malloc(10 * sizeof(int));
    a[0] = 10;
    printf("%d %d\n", a, a[0]);

    int *b = (int *)realloc(a, 20 * sizeof(int));
    printf("%d %d\n", b, b[0]);

    int *c = (int *)realloc(b, 5 * sizeof(int));
    printf("%d %d", c, c[0]);
    return 0;
}
```

![image-20210409110803688](https://gitee.com/destiny0118/picgo/raw/master/pic/image-20210409110803688.png)

![image-20210427184809807](https://gitee.com/destiny0118/picgo/raw/master/pic/image-20210427184809807.png)

# 3. memset

> string.h

> int column[MAX_N];
>
>memset(column, 0, sizeof(int) * MAX_N);

# 4. 条件表达式

> ax为0时，输出15
>
> ax不为0时，输出6

```c
#include <stdio.h>
#include <windows.h>
int main() {
    int ax = 0;
    int pc = 5;
    printf("%d", pc = ax ? pc + 1 : pc + 10);
    system("pause");
}
```

