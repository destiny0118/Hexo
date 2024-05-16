---
title: CSAPP
date: 2023-6-16
description: 书中内容学习记录
---

# 第3章 程序的机器级表示

## 寄存器

![image-20211119213426522](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111192134619.png)

## 操作数格式

![img](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111041123574.png)

## mov后缀

> b: 1字节									al
>
> w: 2字节									ax
>
> l												 eax
>
> q												rax

## 2. 条件码

### 2.1 标志位

![image-20211108104541048](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111081045189.png)

### 2.2 set指令

![image-20211108104638724](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111081046782.png)

#### a、b比较：cmp b, a

| 有符号数比较:  greater lower |           | 无符号数比较：above below |      |
| :--------------------------: | --------- | :-----------------------: | ---- |
|             a<b              | a>=b      |            a<b            | a>=b |
|          OF=0 SF=1           | OF=0 SF=0 |           CF=1            |      |
|          OF=1 SF=0           | OF=1      |           CF=1            |      |



#### 有符号数比较

SF OF ZF

#### 无符号数比较

CF ZF

### 2.3 跳转指令

#### jump

![image-20211109221922634](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111092219784.png)

#### 条件跳转指令

![image-20211109222026421](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111092220475.png)

### 过程

![image-20211110170645507](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111101706596.png)

### 栈帧

![image-20211110171554335](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111101715379.png)

### 数据传送(参数传递)

![image-20211111104856160](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111111049241.png)

X86-64处理器

AVX多媒体指令

# 第4章 处理器体系结构

## Y86-64指令集

![image-20211116115227060](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111161152163.png)

## 指令编码

![image-20211116115239557](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111161152609.png)

![image-20211116115337745](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111161153806.png)

## 指令执行

![image-20211124161139192](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111241611286.png)

### OPq、rrmovq、irmovq

![image-20211124161712200](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111241617241.png)

### rmmovq、mrmovq

![image-20211124162704530](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111241627565.png)

### pushq、popq

![image-20211124163446911](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111241634946.png)

### jmp、call、ret

![image-20211124170645919](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111241706956.png)

### 4-23

![image-20211124202339602](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111242023642.png)

![image-20211124202937606](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111242029637.png)

### 4-40

![image-20211130162835535](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111301630466.png)

### 4-52

![image-20211201163636338](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202112011636435.png)