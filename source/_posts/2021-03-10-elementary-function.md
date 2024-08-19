---
title: 基本初等函数
date: 2021-03-10
updated: 2021-03-28
categories:
- Mathematics
mathjax: true
---

**高等数学**将基本初等函数归为五类：幂函数、指数函数、对数函数、三角函数、反三角函数。
**数学分析**将基本初等函数归为六类：常函数、幂函数、指数函数、对数函数、三角函数、反三角函数。
接下来将按照**数学分析**的分类，逐一介绍这些函数。



<!-- more -->

# 1. 基本初等函数

## 1.1 常函数

常函数是指不管自变量值如何变化，函数值都不变的函数。

形式为 $f(x)=C$，其中 $C$ 为常数，定义域为$(-\infty,+\infty)$。

### 1.1.1 图像

{% asset_img constant-function-example-1.png 300 300 %}

$$f(x)=1$$



## 1.2 幂函数

幂函数是以底数为自变量，幂为因变量，指数为常数的函数。

形式为 $f(x)=x^α$（$a$ 可以是任意实数或者复数）。

### 1.2.1 有理数指数幂

当指数 $a$ 是有理数时，幂函数可以写作如下形式：

$f(x)=x^{k\frac{m}{n}}(k\in\lbrace-1,1,0\rbrace,m,n\in{N}^{*})$

|       |  定义域   |  值域   | 奇偶性 | 图像 |
| :---: | :----: | :----: | :----: | ------ |
| $k=1,m,n$ 均为奇数 | $R$ | $R$ | 奇函数 | {% asset_img power-function-example-1.png 300 300 %} $$red:f(x)=x^\frac{1}{3}$$$$blue:f(x)=x^{3}$$ |
| $k=-1,m,n$ 均为奇数 | $(-\infty,0)\cup(0,+\infty)$ | $(-\infty,0)\cup(0,+\infty)$ | 奇函数 | {% asset_img power-function-example-2.png 300 300 %} $$red:f(x)=x^{-\frac{1}{3}}$$$$blue:f(x)=x^{-3}$$ |
| $k=1,m$ 为奇数$,n$ 为偶数 | $[0,+\infty)$ | $[0,+\infty)$ | 非奇非偶函数 | {% asset_img power-function-example-3.png 300 300 %} $$red:f(x)=x^{\frac{3}{2}}$$$$blue:f(x)=x^{\frac{1}{2}}$$$$green:f(x)=x^{\frac{5}{2}}$$ |
| $k=-1,m$ 为奇数$,n$ 为偶数 | $(0,+\infty)$ | $(0,+\infty)$ | 非奇非偶函数 | {% asset_img power-function-example-4.png 300 300 %} $$red:f(x)=x^{-\frac{3}{2}}$$$$blue:f(x)=x^{-\frac{1}{2}}$$$$green:f(x)=x^{-\frac{5}{2}}$$ |
| $k=1,m$ 为偶数$,n$ 为奇数 | $R$ | $[0,+\infty)$ | 偶函数 | {% asset_img power-function-example-5.png 300 300 %} $$red:f(x)=x^{\frac{4}{3}}$$$$blue:f(x)=x^{\frac{2}{3}}$$$$green:f(x)=x^{2}$$ |
| $k=-1,m$ 为偶数$,n$ 为奇数 | $(-\infty,0)\cup(0,+\infty)$ | $(0,+\infty)$ | 偶函数 | {% asset_img power-function-example-6.png 300 300 %} $$red:f(x)=x^{-\frac{4}{3}}$$$$blue:f(x)=x^{-\frac{2}{3}}$$$$green:f(x)=x^{-2}$$ |
| $k=0$ | $(-\infty,0)\cup(0,+\infty)$ | $\lbrace1\rbrace$ | 偶函数 | {% asset_img power-function-example-7.png 300 300 %} $$red:f(x)=x^{0}$$注意，$f(x)=x^{0}$ 的图像并不是直线，而是直线 $y=1$ 去掉一点 $(0,1)$ |



## 1.3 指数函数

一般地，指数函数的形式为 $f(x)=b^x$（$b$ 为常数且 $b\in(0,1)\cup(1,+\infty)$），函数的定义域为 $R$，值域为 $(0,+\infty)$。

注意，在指数函数的定义表达式中，在 $b^x$ 前的系数必须为 $1$，自变量 $x$ 必须在指数的位置上，且不能为 $x$ 的其他表达式，否则，就不是指数函数。

### 1.3.1 图像
|       |  值域   | 图像 |
| :---: | :----: | ------ |
| $0<b<1$ | $(0,+\infty)$ | {% asset_img exponential-function-example-1.png 300 300 %} $$red:f(x)=\frac{1}{2}^x$$$$blue:f(x)=\frac{1}{4}^x$$ |
| $b>1$ | $(0,+\infty)$ | {% asset_img exponential-function-example-2.png 300 300 %} $$red:f(x)=2^x$$$$blue:f(x)=4^x$$ |

### 1.3.2 性质

#### 1.3.2.1 性质 1

由指数函数的定义：

​	$e^x=\lim_{n \to \infty}(1+\frac{x}{n})^{n}$

可以得出以下定律：

​	$e^0=1$

​	$e^1=e$

​	$e^{x+y}=e^xe^y$

​	$e^{xy}=(e^x)^y$

​	$\frac{e^x}{e^y}=e^{x-y}$

​	$e^{-x}=e^{0-x}=\frac{e^0}{e^x}=\frac{1}{e^x}$

其中 $x\in{R},y\in{R}$。



#### 1.3.2.2 性质 2

因为在指数函数的定义中 $x$ 是实数，可以使用自然对数 $e$，把更一般的指数函数，即正实数的实数幂函数定义为

$b^x=(e^{\ln{b}})^x=e^{x\ln{b}}$



#### 1.3.2.3 性质 3

定义于所有的 $b>0$，和所有的实数 $x$。它叫做"底数为 $b$ 的指数函数"。从而拓展了通过乘方和方根运算定义的正实数的有理数幂函数：

$b^{\frac{m}{n}=\sqrt[n]{b^m}}$

而方根运算可通过自然对数和指数函数来表示：

$\sqrt[n]{b}=b^{\frac{1}{n}}=(e^{\ln{b}})^{\frac{1}{n}}=e^{\frac{\ln{b}}{n}}$



## 1.4 对数函数

对数是幂运算的逆运算。

如果 $b^x=N(b>0,且b\neq1)$，那么 $x$ 叫做以 $b$ 为底 $N$ 的对数，记作 $x=\log_{b}{N}$，读作以 $b$ 为底 $N$ 的对数，其中 $b$ 叫做对数的底，$N$ 叫做幂或者真数。

一般地，对数函数的形式为 $f(x)=\log_{b}{x}(b>0,且b\neq1)$，函数的定义域为 $(0,+\infty)$，值域为 $R$ 。

对数函数实际上就是指数函数的反函数，可表示为 $x=b^y$。因此指数函数里对于常数 $b$ 的规定，同样适用于对数函数。

### 1.4.1 图像
|       |  值域   | 图像 |
| :---: | :----: | ------ |
| $0<b<1$ | $R$ | {% asset_img logarithmic-function-example-1.png 300 300 %} $$red:f(x)=\log_{\frac{1}{2}}{x}$$$$blue:f(x)=\log_{\frac{1}{4}}{x}$$ |
| $b>1$ | $R$ | {% asset_img logarithmic-function-example-2.png 300 300 %} $$red:f(x)=\log_{2}{x}$$$$blue:f(x)=\log_{4}{x}$$ |

### 1.4.2 性质

- 对数函数的函数图像恒定过点$(1,0)$

- 当 $0<b<1$ 时，函数在定义域 $(0,+\infty)$ 上为单调减函数；$b>1$ 时，函数在定义域 $(0,+\infty)$ 上为单调增函数

- 对数函数 $f(x)=\log_{b}{x}(b>0,b\neq1,x>0)$

  当 $0<b<1,0<x<1$ 时，$f(x)=\log_{b}{x}>0$
  
  当 $b>1, x>1$ 时，$f(x)=\log_{b}{x}>0$
  
  当 $0<b<1, x>1$ 时，$f(x)=\log_{b}{x}<0$
  
  当 $b>1, 0<x<1$ 时，$f(x)=\log_{b}{x}<0$
  
- 底数为 $b$ 的对数函数$f(x)=\log_{b}{x}与$指数函数 $f(x)=b^x$ 互为反函数，两者的函数图像关于直线 $y = x$ 对称。

{% asset_img logarithmic-function-example-3.png 300 300 %} $$red:f(x)=\log_{2}{x}$$$$blue:f(x)=2^{x}$$$$green:f(x)=\log_{\frac{1}{2}}{x}$$$$blue:f(x)={\frac{1}{2}}^{x}$$

### 1.4.3 公式

#### 1.4.3.1 换底公式

$\log_{b}{x}=\frac{\log_{k}{x}}{\log_{k}{b}}$

##### 1.4.3.1.1 证明

由对数函数的定义，可得：

$x=b^{\log_{b}{x}}$

等式两边同时以 $k$ 为底取对数，可得：

$\log_{k}{x}=\log_{k}({b^{\log_{b}{x}})}$

$\because b^x=N(b>0,且b\neq1)$，有 $x=\log_{b}{N}$。

那么 $(b^x)^t=b^{xt}=N^t\ (t\in{R})$，可得：

$xt=\log_{b}{N^t}=t\log_{b}{N}\ (t\in{R})$

$\therefore\log_{k}{x}=\log_{k}{b^{\log_{b}{x}}}=\log_{b}{x}\log_{k}{b}$

$\log_{b}{x}=\frac{\log_{k}{x}}{\log_{k}{b}}$ 得证。



#### 1.4.3.2 和差

$\log_{b}{MN}=\log_{b}{M}+\log_{b}{N}$

$\log_{b}{\frac{M}{N}}=\log_{b}{M}-\log_{b}{N}$

##### 1.4.3.2.1 证明

设 $M=\beta^{m},N=\beta^{n}$

则 $\log_{b}{MN}=\log_{b}({\beta^{m}\beta^{n}})$

$\ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ =\log_{b}({\beta^{m+n}})$

$\ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ =(m+n)\log_{b}{\beta}$

$\ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ =m\log_{b}{\beta}+n\log_{b}{\beta}$

$\ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ =\log_{b}{\beta^m}+\log_{b}{\beta^n}$

$\ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ =\log_{b}{M}+\log_{b}{N}$

$\log_{b}{\frac{M}{N}}=\log_{b}{M}+\log_{b}{\frac{1}{N}}$

$\ \ \ \ \ \ \ \ \ \ \ \ =\log_{b}{M}+\log_{b}{N^{-1}}$

$\ \ \ \ \ \ \ \ \ \ \ \ =\log_{b}{M}-\log_{b}{N}$



#### 1.4.3.3 次方公式

$\log_{b^N}({x^M})=\frac{M}{N}\log_{b}{x}$

##### 1.4.3.3.1 证明

由换底公式，可得：

$\log_{b^N}({x^M})=\frac{\ln{b^N}}{\ln{x^M}}$

$\ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ =\frac{N\ln{b}}{M\ln{x}}$

$\ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ =\frac{M}{N}\log_{b}{x}$

##### 

#### 1.4.3.4 还原

$b^{\log_{b}{x}}=x$

$\ \ \ \ \ \ \ \ =\log_{b}{b^x}$



#### 1.4.3.5 互换

$M^{\log_{b}{N}} = N^{\log_{b}{M}}$

##### 1.4.3.5.1 证明

设 $\alpha=\log_{b}{N},\  \beta=\log_{b}{M}$，则有 $b^\alpha=N,\ b^\beta=M, \ (b^\beta)^\alpha=(b^\alpha)^\beta$，

$\therefore M^{\log_{b}{N}} = N^{\log_{b}{M}}$



#### 1.4.3.6 倒数

$\log_{b}{\theta}=\frac{\ln \theta}{\ln b}=\frac{1}{\frac{\ln b}{\ln \theta}}=\frac{1}{\log_{\theta}{b}}$



## 1.5 三角函数

<!--TODO-->



## 1.6 反三角函数

<!--TODO-->



# 2. 初等函数

初等函数是由**基本初等函数**经过有限次的有理运算（加、减、乘、除、有理数次乘方、有理数次开方）及有限次函数复合所产生、并且在定义域上能用一个解析式表示的函数。

基本初等函数和初等函数在其定义区间内均为**连续函数**。

一般来说，分段函数不是初等函数，因为在这些分段函数的定义域上不能用一个解析式表示。




> 参考：
>
> https://baike.baidu.com/item/%E5%9F%BA%E6%9C%AC%E5%88%9D%E7%AD%89%E5%87%BD%E6%95%B0
>
> https://zh.wikipedia.org/wiki/%E5%88%9D%E7%AD%89%E5%87%BD%E6%95%B0


<style>
  table {
    width: 1100px; /*表格宽度*/
    max-width: 1100px; /*表格最大宽度，避免表格过宽*/
    border: 1px solid #dedede; /*表格外边框设置*/
    margin: 1px auto; /*外边距*/
    border-collapse: collapse; /*使用单一线条的边框*/
    empty-cells: show; /*单元格无内容依旧绘制边框*/
  }
  table th,
  table td {
    height: 35px; /*统一每一行的默认高度*/
    border: 1px solid #dedede; /*内部边框样式*/
    padding: 0 10px; /*内边距*/
  }
  table th {
    font-weight: bold; /*加粗*/
    text-align: center !important; /*内容居中，加上 !important 避免被 Markdown 样式覆盖*/
    background: #F8F8F8; /*背景色*/
  }
  table tbody tr:nth-child(n) {
    background: #FFFFFF; 
  }
  table tr:hover {
    background: #EFEFEF; 
}
  table th {
    white-space: nowrap; /*表头内容强制在一行显示*/
  }
  table td:nth-child(1) {
    white-space: nowrap; /*表格第一列单元格内容不换行*/
  }
</style>
