---
title: python语法
categories: 语法
---

# 列表



1. 函数参数加*号，代表将多个参数组装成列表
2. 数组前面加参数，代表把数组拆分成多个逗号分割的变量，类似js中的展开符号.

```python
def add(x,y):
    return x+y
ls=[10,12]
add(*ls)
```

