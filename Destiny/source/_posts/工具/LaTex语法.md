---
title: LaTex语法
categories: 工具
mathjax: true
---

- inline 行间公式：\$...\$
- display 块间公式：\$$...\$\$

[Typora中利用LaTeX 插入数学公式](https://blog.csdn.net/happyday_d/article/details/83715440)

[VSCODE+latex 自动换行 - 简书 (jianshu.com)](https://www.jianshu.com/p/5207a41baf51/)

# Latex排版



[LaTeX（XeLaTeX）写的文档如何一键转为word？ - 知乎 (zhihu.com)](https://www.zhihu.com/question/31850346)

## 换行

```latex
\\ 换行
\par 	换行并缩进

abc &= 
	&=
等号对齐
```



## 1. 插入图片

[(11条消息) Latex中插入图片_还能坚持的博客-CSDN博客_latex插入图片](https://blog.csdn.net/qq_35091353/article/details/111403178?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-0.pc_relevant_default&spm=1001.2101.3001.4242.1&utm_relevant_index=3)

```latex
\begin{figure}
    \centering
    \begin{minipage}[c]{0.5\textwidth}
        \centering
        \includegraphics[height=4.5cm,width=7.5cm]{original_invert.jpg}
    \end{minipage}
    \begin{minipage}[c]{0.5\textwidth}
        \centering
        \includegraphics[height=4.5cm,width=7.5cm]{invert_image.jpg}
    \end{minipage}
    \caption{并排图形}
\end{figure}
```

![image-20220501102617993](https://raw.githubusercontent.com/destiny0118/picgo/master/img/202205011026064.png)

```latex
\begin{figure}[htbp]
    \centering
    \begin{minipage}[t]{0.4\linewidth}  %并排插图时，线宽很重要，自己慢慢试，俩张图就不要超过0.5，三张图不要超过0.33之类的，自己看着办  
        \centering
        \includegraphics[height=5cm]{original_invert.jpg}
        % \label{fig4}
        % \caption{原始图像}
    \end{minipage}
    \hfill%分栏的意思吧
    \begin{minipage}[t]{0.5\linewidth}
        \centering
        \includegraphics[height=5cm]{invert_image.jpg}
        % \label{fig5}
        % \caption{invert操作图像}
    \end{minipage}
    \label{fig4}
    \caption{原始图像与invert操作图像}
\end{figure}
```

## 2. 插入列表

[(11条消息) [翻译\] LaTeX 中的列表_Xovee的博客-CSDN博客_latex 列表](https://blog.csdn.net/xovee/article/details/106365532)

列表缩进

[(11条消息) item调整缩进 latex - CSDN](https://www.csdn.net/tags/NtzaYg3sMzg3ODQtYmxvZwO0O0OO0O0O.html)

[Latex 插入列举条目、编号item及间隔调整_latex列举_衷科知眠的博客-CSDN博客](https://blog.csdn.net/JingpengSun/article/details/129166744)

https://blog.csdn.net/robert_chen1988/article/details/83179571

## 3. 插入表格

[LaTeX插入表格教程（心得分享） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/392190222)



## 4. 参考文献

[Latex插入参考文献的两种方式（以IEEE为例） - 简书 (jianshu.com)](https://www.jianshu.com/p/bb0585d34e47)

[(11条消息) Latex中如何插入参考文献的两种方法_进击的程序媛阿飒的博客-CSDN博客_latex添加参考文献](https://blog.csdn.net/qq_36235935/article/details/116938313)

[(11条消息) LATEX插入参考文献（两种方法）_feiba54的博客-CSDN博客_latex参考文献](https://blog.csdn.net/qq_39540454/article/details/107604031)



## 空格

[LaTeX中的宽度单位em,ex,px,pt_latex pt-CSDN博客](https://blog.csdn.net/gsgbgxp/article/details/129693747)

[Latex 中的空格汇总_latex 空白-CSDN博客](https://blog.csdn.net/hysterisis/article/details/114123131)

[(78条消息) Latex 中的空格汇总_latex中空格_零度蛋花粥的博客-CSDN博客](https://blog.csdn.net/hysterisis/article/details/114123131)

![img](https://raw.githubusercontent.com/destiny0118/picgo/master/pic2023/202307261602226.jpeg)









# 数学公式

## 1. 函数

对数 $log_ax$

```latex
$log_ax$
$lnx$
$lgx$
```

> $log_ax$
>
> $lnx$
>
> $lgx$

## 2. 符号表

```latex
$\alpha$
\nabla
\partial 
\underline a
```

$\alpha$

$\nabla$

$\partial$

$\underline a$

空格表

![img](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202112292226115.png)

希腊字母表

![img](D:%5CHexo%5Cimage%5C202112292214231.png)

数学模式重音符号

![img](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202112292226278.png)

二元关系

![img](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202112292227484.png)

二元运算符

![img](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202112292227918.png)

“大”运算符

![img](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202112292228849.png)

箭头

![img](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202112292253282.png)

定界符

![img](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202112292254595.png)

空心字符
$$
\mathbb{R}
$$


## 3. 上下标、根号、省略号

```latex
下标：
$x_i$
$x_{i1}$

上标：
$x^2$

根号：
$5\sqrt[n]{3}$

省略号：
$\dots$
$\cdots$

分式中上下标
\frac{\sum_1^n}{n}
\frac{\sum\limits_1^n}{n}
```

-  $x_i$

  > 上下标多于一个字母时： $x_{i1}$
  
- $x^2$

- $5\sqrt[n]{3}$

-  $\dots$

- $\cdots$

$$
\frac{\sum_1^n}{n}\\
\frac{\sum\limits_1^n}{n}
$$



## 4. 箭头

```
$leftarrow$
```

  $\leftarrow$

![img](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/20181104140310286.JPG)

## 5. 除法

```
\frac{1}{2}
```

$\frac{1}{2}$

```
$$
 a \div b\\
 a \times b\\
 a \cdot b
 $$
```


$$
a \div b\\
a \times b\\
a \cdot b
$$

## 6. 上下分层

```
a_{-}^{+}
```

$a_{-}^{+}$

## 7. 分段函数

```
\begin{equation}
next[j]=

\begin{cases}
0& \text{j=1}\\
max\{k,1<k<j且'p_1...p_k'='p_{j-k+1}...p_{j-1}'\}& \text{此集合不空时}\\
1& \text{其他情况}

\end{cases}

\end{equation}
```

$$
\begin{equation}
next[j]=

\begin{cases}
0& \text{j=1}\\
max\{k,1<k<j且'p_1...p_k'='p_{j-k+1}...p_{j-1}'\}& \text{此集合不空时}\\
1& \text{其他情况}

\end{cases}

\end{equation}
$$

## 8. 比较符号

```
\leq	
\geq
```

$\leq$

## 9. 导数

偏导符号

```
\frac{\partial f}{\partial x}
\frac{\mathrm{d} f}{\mathrm{d} y}
\frac{y^{'}}{x^{'}}
```

$$
\frac{\partial f}{\partial x} \\
\frac{\mathrm{d} f}{\mathrm{d} y}\\
\frac{y^{'}}{x^{'}}
$$

**点形式的求导符号：\dot x 和 \ddot y**

```
$ \frac{ \dot y }{ \dot x } $  # 一个点
$ \frac{ \ddot y }{ \ddot x } $  # 两个点
$ \frac{ \dddot y }{ \dddot x } $  # 几个点就是几个d
```

$$
\frac{ \dot y }{ \dot x } \\
\frac{ \ddot y }{ \ddot x } \\
\frac{ \dddot y }{ \dddot x }
$$



**全微分算子：\nabla f**

```
 \nabla f
 \Delta f
```

$$
\nabla f
$$

## 10. 矩阵

## 向上取整/\*向下取整\***

*千次阅读*

2020-03-11 17:00:58

个人博客：https://www.vectormoon.net/

向上取整指令：

```bash
$\lceil x \rceil$
```

向下取整指令：

```bash
$\lfloor x \rfloor$
```

$$
[\enspace\lceil \frac{k_w-1}{2}\rceil, 
\lfloor\frac{k_w-1}{2}\rfloor,
\lceil \frac{k_h-1}{2}\rceil, 
\lfloor\frac{k_h-1}{2}\rfloor\enspace]
$$

$$
\gradient
$$

