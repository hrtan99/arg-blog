---
# 1) 新建一篇文章（会创建 posts/my-first-post/index.md）
# hugo new --kind post-bundle posts/my-first-post

# 2) 把配图放到同目录
# posts/my-first-post/
# ├─ index.md
# └─ cover.jpg  ← featuredImage 可填 "cover.jpg"

# 基本
title: "二阶线性非齐次微分方程特解的五大求解方法"
subtitle: ""
date: 2018-10-12
lastmod:
draft: false

# URL / SEO
slug: "二阶线性非齐次微分方程特解的五大求解方法"
aliases: []
description: ""
# 若你用自定义永久链接，可在 config 里用 :slug
# [permalinks]
# posts = "/blog/:year/:slug/"

# 分类
categories: ["Mathematics"]
tags: ["Calculus"]
series: []

# 文章选项
toc: true            # 是否显示目录
summary: ""          # 留空时 Hugo 会自动摘要（也可手写）
featured: false
featuredImage: ""    # e.g. "cover.jpg"（放在同目录）
featuredImageAlt: ""
featuredImageCaption: ""

# 版权/作者
author: ""
authors: []

# 页面开关（按需给主题读）
comments: true
lightgallery: false  # 若你有灯箱之类功能
math: true           # 用 MathJax/KaTeX 时置 true
tikz: false

# 多语言（若你启用 i18n，可留空或按语言单独写）
# translations:
#   - language: "en"
#     title: ""
#     description: ""
---
<!-- 摘要（可选）：写在此注释上方或 summary 字段里；正文从这里开始。 -->



我们知道，对于一个二阶线性非齐次微分方程，它的通解是它对应的二阶线性齐次微分方程的通解加上它自己的一个特解。而它所对应的二阶线性齐次微分方程的通解通常有两种情况：若该方程为常系数，我们根据特征方程容易求得通解。但若该方程为变系数，情况就稍麻烦些。大多数的变系数线性高阶微分方程不能利用初等函数求解（如$y''+xy=0$，它的解是无穷级数形式的），只有对于一些特殊形式的二阶变系数线性齐次微分方程，我们才可以利用降阶法等方法求得初等函数解。

求得对应齐次方程的通解后，另一个难点在于求它的一个特解，这也正是考研数学可以说必考的知识点。考虑到变系数方程求解的难度，一般考研题如果考察求解二阶线性非齐次微分方程，则通常是常系数的（不排除变系数的情况）。

下面我们以一个二阶常系数线性非齐次微分方程$y''-3y'+2y=2xe^x$为例，分别讲解五种方法如何用于求此二阶微分方程的特解。

## 待定系数法

待定系数法是最常用也是最基本的一种方法。

该微分方程的特征方程为$\lambda^2 -3 \lambda + 2 = 0$，得特征根$\lambda_1=1,\lambda_2=2$，从而通解为$y=C_1e^{x}+C_2e^{2x}$。

现考虑根据零化算子法确定特解$y^*$的形式。考虑零化算子$(D-1)^2$，可以将$2xe^x$零化。方程两端同时乘以零化算子，得到$(D-1)^2 (D-1)(D-2) = 0$。此齐次方程应当与题目所给非齐次方程具有相同的解形式。

又特征根$\lambda=1$已经出现过，故特解形式应当为$y^* = Axe^x+Bx^2e^x$。

对方程的特解求导：
$$
y^* = Axe^x+Bx^2e^x \\[2ex]
(y^*)'=[Ax^2+(2A+B)x + B]e^x \\[2ex]
(y^*)''=[Ax^2+(4A+B)x +2A+ 2B]e^x
$$
回代原方程，即可解得：
$$
A=-1,\;B=-2
$$
故我们所求的通解为：
$$
y^* = -xe^x-2x^2e^x \\[2ex]
$$
此方法难度上较为简单，但它的局限在于一般情况下计算量稍大。



## 常数变易法

常数变易法的优势在于它能够以较小的计算量求解变系数线性微分方程，比如我们都学过如何使用它求解一阶变系数线性微分方程。在这里，我们简单介绍一下二阶线性微分方程的常数变易法。

假设我们有如下标准形式的二阶常系数非齐次线性微分方程：
$$
y''+P(x)y'+Q(x)y=f(x)
$$
并且我们**已知**该方程对应的齐次方程的通解为$y(x)=C_1y_1(x)+C_2y_2(x)$的形式。（注：二阶线性变系数齐次微分方程通常可以通过降阶法求得通解。）

类似于一阶非齐次线性微分方程的常数变易法，我们设该方程的特解为$y^* = u_1(x)y_1(x)+u_2(x)y_2(x)$。

对特解求导，得：
$$
\begin{aligned}
&({y^*})^{\prime}=u_{1} y_{1}^{\prime}+y_{1} u_{1}^{\prime}+u_{2} y_{2}^{\prime}+y_{2} u_{2}^{\prime} \\[2ex]
&({y^*})^{\prime \prime}{ }=u_{1} y^{\prime \prime}{ }_{1}+y_{1}^{\prime} u_{1}^{\prime}+y_{1} u^{\prime \prime}{ }_{1}+u_{1}^{\prime} y_{1}^{\prime}+u_{2} y^{\prime \prime}{ }_{2}+y_{2}^{\prime} u_{2}^{\prime}+y_{2} u_{2}^{\prime \prime}+u_{2}^{\prime} y_{2}^{\prime}
\end{aligned}
$$
再将特解回代入微分方程，整理后得到
$$
\begin{aligned}
({y^*})^{\prime \prime}+P(x) ({y^*})^{\prime}+Q(x) {y^*}=& u_{1}\left[y^{\prime \prime}{ }_{1}+P y_{1}^{\prime}+Q y_{1}\right]+u_{2}\left[y_{2}^{\prime \prime}+P y_{2}^{\prime}+Q y_{2}\right] \\[2ex]
&+y_{1} u_{1}^{\prime \prime}+u_{1}^{\prime} y_{1}^{\prime}+y_{2} u_{2}^{\prime \prime}+u_{2}^{\prime} y_{2}^{\prime}+P\left[y_{1} u_{1}^{\prime}+y_{2} u_{2}^{\prime}\right]+y_{1}^{\prime} u_{1}^{\prime}+y_{2}^{\prime} u_{2}^{\prime} \\[2ex]
=& \frac{\mathrm{d}}{\mathrm{d} x}\left[y_{1} u_{1}^{\prime}\right]+\frac{\mathrm{d}}{\mathrm{d} x}\left[y_{2} u_{2}^{\prime}\right]+P\left[y_{1} u_{1}^{\prime}+y_{2} u_{2}^{\prime}\right]+y_{1}^{\prime} u_{1}^{\prime}+y_{2}^{\prime} u_{2}^{\prime} \\[2ex]
=& \frac{\mathrm{d}}{\mathrm{d} x}\left[y_{1} u_{1}^{\prime}+y_{2} u_{2}^{\prime}\right]+P\left[y_{1} u_{1}^{\prime}+y_{2} u_{2}^{\prime}\right]+y_{1}^{\prime} u_{1}^{\prime}+y_{2}^{\prime} u_{2}^{\prime}=f(x)
\end{aligned}
$$
由于目前为止对于两个未知函数$u_1,u_2$只有一个约束关系，观察上述等式的结构， 若我们令$y_{1} u_{1}^{\prime}+y_{2} u_{2}^{\prime}=0$，即可立即得到一个二元线性方程组：
$$
\begin{gathered}
y_{1} u_{1}^{\prime}+y_{2} u_{2}^{\prime}=0 \\[2ex]
y_{1}^{\prime} u_{1}^{\prime}+y_{2}^{\prime} u_{2}^{\prime}=f(x)
\end{gathered}
$$
只要该二元线性方程组的Wronskian行列式：
$$
W=
\begin{vmatrix} y_1 & y_2 \\ y_1' & y_2' \\ \end{vmatrix}
$$
不为零，我们就可以通过求解线性方程组的Cramer法则解出$u_1'(x),u_2'(x)$，再对它们进行积分，从而得到方程组的特解。

根据上述方法，对于例题，我们有$y_{1}=e^{x}$和$y_{2}=e^{2 x}$， 故可设原方程的特解为$y^*=e^{x} v_{1}(x)+e^{2 x} v_{2}(x)$，得到方程组：
$$
\left\{\begin{array}{l}e^{x} v_{1}{ }^{\prime}(x)+e^{2 x} v_{2}{ }^{\prime}(x)=0 \\[2ex] 
e^{x} v_{1}{ }^{\prime}(x)+2 e^{2 x} v_{2}{ }^{\prime}(x)=2 x e^{x}\end{array}\right.
$$
对应的Wronskian行列式：
$$
W=\begin{vmatrix} e^x & e^{2x}  \\ e^x & 2e^{2x}  \\  \end{vmatrix}= e^{3x}\neq0
$$
根据Cramer法则解出$v_1',v_2'$后积分（**不保留积分常数**），得
$$
\begin{aligned}
v_{1}(x)=&-x^{2} \\[2ex]
v_{2}(x)=&-2(x+1) e^{-x}
\end{aligned}
$$
所以我们得到原方程的特解：
$$
y^*=e^{x}\left(-x^{2}+\right)+e^{2 x}\left(-2(x+1) e^{-x}\right)= e^{2 x}-\left(x^{2}+2 x+2\right) e^{x}
$$
实际上，我们注意到，使用常数变易法我们可以**直接求出方程的通解形式**。

不难发现，若根据Cramer法则解出$v_1', v_2'$后积分（**保留积分常数**），得 
$$
\begin{aligned}
v_{1}(x)=&-x^{2}+C_{1} \\[2ex]
v_{2}(x)=&-2(x+1) e^{-x}+C_{2}
\end{aligned}
$$
这样，就得到原方程的通解：
$$
y=e^{x}\left(-x^{2}+C_{1}\right)+e^{2 x}\left(-2(x+1) e^{-x}+C_{2}\right)=C_{1} e^{x}+C_{2} e^{2 x}-\left(x^{2}+2 x+2\right) e^{x}
$$

## 降阶法

对于任意的二阶常系数线性非齐次微分方程$y''+py'+q=f(x)$，设对应的齐次微分方程的特征根$\lambda_1,\lambda_2$，则根据韦达定理，总有
$$
\lambda_1+\lambda_2 = -p ,\; \lambda_1\lambda_2 = q
$$
在这里我们仅讨论$\lambda_1,\lambda_2$均为实根的情况。于是方程总可化为$(y'-\lambda_1y)'-\lambda_2(y'-\lambda_1y)=f(x)$.

作换元$y'-\lambda_1y=u$，于是原方程便化为一个一阶线性微分方程$u'-\lambda_2u=f(x)$.

于是问题便转化为求解下列两个一阶线性微分方程：
$$
\begin{aligned}
y'-\lambda_1y  = & u \\[2ex]
u'-\lambda_2u =&f
\end{aligned}
$$
回到我们的例题中。作换元后再直接根据一阶线性微分方程的通解公式，得到以下步骤：
$$
\begin{aligned}
u'-u &=  2xe^x   \\[2ex]
u &=e^{-\int(-1) d x}\left[\int 2 x e^{x} e^{\int(-1) d x} d x+C_{1}\right] \\[2ex]
&=e^{x}\left(\int 2 x d x+C_{1}\right)\\[2ex]
&=x^{2} e^{x}+C_{1} e^{x}\\[2ex]
\end{aligned}
$$
然后我们再用相同的办法解出通解：
$$
\begin{aligned}
y^{\prime}-2 y
&=x^{2} e^{x}+C_{1} e^{x} \\[2ex]
y 
&=e^{2 x}\left[\int\left(x^{2} e^{x}+C_{1} e^{x}\right) e^{-2 x} d x+C_{2}\right] \\[2ex]
&=-\left(x^{2}+2 x+1\right) e^{x}-C_{1} e^{x}+C_{2} e^{2 x}\\[2ex]
\end{aligned}
$$
可见此方法的计算量也较小。



## 积分因子法

积分因子法实际上也是来源于一阶线性微分方程的积分因子法，同时是受到上述降阶法的启发。

我们观察到降阶法是将原二阶常系数线性非齐次微分方程降阶成为了两个一阶线性微分方程，联想到一阶线性微分方程的的积分因子法，

考虑我们转化后的方程组：
$$
\begin{eqnarray*}
y'-\lambda_1y  =  u \tag{1}  \\[2ex]
u'-\lambda_2u = f \tag{2}
\end{eqnarray*}
$$
我们可以首先对方程$(2)$两边同时乘以积分因子$e^{-\lambda_2x}$，得：
$$
\begin{eqnarray*}
u'e^{-\lambda_2x}-\lambda_2e^{-\lambda_2x}u  = f(x) e^{-\lambda_2x} \\[2ex]
(ue^{-\lambda_2x})'= f(x) e^{-\lambda_2x} \\[2ex]
ue^{-\lambda_2x}= \int f(x) e^{-\lambda_2x} \mathrm dx \tag{3}\\[2ex] 
\end{eqnarray*}
$$
注意到$u= y'-\lambda_1y$，于是得到：
$$
\begin{eqnarray*}
e^{-\lambda_2x} (y'-\lambda_1y )=  \int f(x) e^{-\lambda_2x} \mathrm dx \\[2ex] 
\end{eqnarray*}
$$
为了继续运用积分因子法，我们自然想到补齐方程左端的积分因子。于是我们对次方程两边再乘以积分因子$e^{(\lambda_2  - \lambda_1)x}$后，一定有：
$$
\begin{eqnarray*}
e^{-\lambda_1x} (y'-\lambda_1y )= e^{(\lambda_2  - \lambda_1)x} \int f(x) e^{-\lambda_2x} \mathrm dx \\[2ex] 
(e^{-\lambda_1x} y)'= e^{(\lambda_2  - \lambda_1)x} \int f(x) e^{-\lambda_2x} \mathrm dx \\[2ex] 
e^{-\lambda_1x} y= \int \left( e^{(\lambda_2  - \lambda_1)x} \int f(x) e^{-\lambda_2x} \mathrm dx \right) \mathrm dx \\[2ex] 
\end{eqnarray*}
$$
所以解得：
$$
y= e^{\lambda_1x} \int \left( e^{(\lambda_2  - \lambda_1)x} \int f(x) e^{-\lambda_2x} \mathrm dx \right) \mathrm dx \tag{4}\\[2ex]
$$
上述方法告诉我们，此类二阶线性微分方程一定能通过积分因子法求解。另外，虽然得到的结果虽然看起来很复杂，但实际上往往在求解的过程中就逐渐简化了。

为了更好的理解上述过程，我们将积分因子法运用到此题中，得到一个完全形式化的步骤：
$$
\begin{aligned}
y^{\prime \prime}-3 y^{\prime}+2 y=&2 x e^{x} \\[2ex]
e^{-x} y^{\prime \prime}-3 e^{-x} y^{\prime}+2 e^{-x} y=&2 x \\[2ex]
e^{-x}\left(y^{\prime}-2 y\right)^{\prime}-e^{-x}\left(y^{\prime}-2 y\right)=&2 x \\[2ex]
e^{-x}\left(y^{\prime}-2 y\right)=&x^{2} \\[2ex]
e^{-2 x}\left(y^{\prime}-2 y\right)=&x^{2} e^{-x} \\[2ex]
e^{-2 x} y=&-\left(x^{2}+2 x+2\right) e^{-x} \\[2ex]
y=&-\left(x^{2}+2 x+2\right) e^{x}
\end{aligned}
$$
可见, 积分因子法实际上略去了降阶法中降阶的步骤, 直接一步步利用积分因子转化即可得到最终的结果.

## 微分算子法

微分算子法可以使用, 但若对它理解不深, 不建议使用.

由微分算子表示法, 我们将原方程写为$(D^2-3D+2)y=xe^x$.

根据输入函数为$P(x)e^x$形式下微分算子的移位法则，我们可以得到以下求解步骤：
$$
\begin{aligned}
y^* &=\frac{1}{D^{2}-3 D+2} 2x e^{x}=e^{x} \frac{1}{(D+1)^{2}-3(D+1)+2} 2x \\[2ex]
&=e^{x} \frac{1}{D^{2}-D} 2x=-e^{x} \frac{1}{D} \frac{1}{1-D} 2x \\[2ex]
&=-e^{x}\left(\frac{1}{D}+1+D+D^{2}+\cdots\right) 2x \\[2ex]
&=-e^{x}\left( x^{2}+2x+2\right)\\[2ex]
\end{aligned}
$$
虽然最终的结果为$y^*=-e^{x}\left(x^{2}+2x+2\right)$，但我们能够看出，此结果与其他方式得到的结果仅仅相差了齐次方程的通解，所以这个结果也是正确的（事实上，从此处求得的$y^*$中减去通解部分，就能得到与答案一样的形式）。





