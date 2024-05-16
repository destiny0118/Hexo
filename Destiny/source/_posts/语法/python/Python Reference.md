---
title: python语法
categories: 语法
---

sequence

# 列表(lists)

> [1,2,3]
>
> odds=[3,5,7,9,11]
>
> list(range(1,3))
>
> ​     	[1,2]
>
> [odds[i] for i in range(1,3)]
>
> ​	[5,7]
>
> sum([1,2,3])=6
>
> sum([[1,2],[3]],[])=[1,2,3]

![image-20230911201852400](https://raw.githubusercontent.com/destiny0118/picgo/master/pic2023/202309112018499.png)

![image-20230911201922327](https://raw.githubusercontent.com/destiny0118/picgo/master/pic2023/202309112019380.png)



1. 函数参数加*号，代表将多个参数组装成列表
2. 数组前面加参数，代表把数组拆分成多个逗号分割的变量，类似js中的展开符号.

```python
def add(x,y):
    return x+y
ls=[10,12]
add(*ls)
```

## range



tuple

> immutable sequences





Lecture18

![image-20230913103055254](https://raw.githubusercontent.com/destiny0118/picgo/master/pic2023/202309131030411.png)
