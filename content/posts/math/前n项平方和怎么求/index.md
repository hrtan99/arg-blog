---
# 1) 新建一篇文章（会创建 posts/my-first-post/index.md）
# hugo new --kind post-bundle posts/my-first-post

# 2) 把配图放到同目录
# posts/my-first-post/
# ├─ index.md
# └─ cover.jpg  ← featuredImage 可填 "cover.jpg"

# 基本
title: "前n项平方和怎么求"
subtitle: ""
date: 2020-02-18
lastmod: 
draft: false

# URL / SEO
slug: "前n项平方和怎么求"
aliases: []
description: ""
# 若你用自定义永久链接，可在 config 里用 :slug
# [permalinks]
# posts = "/blog/:year/:slug/"

# 分类
categories: ["Mathematics"]
tags: ["Combinatorics"]
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



若$S_n$为前$n$项平方和：
$$
S_n = 1^2+2^2+\cdots + n^2
$$
它的通项公式我们在中学阶段已经学习过，即
$$
S_n = \frac {n(n+1)(2n+1)} 6
$$
但是它的推导过程却不算简单。本文主要从组合数学的角度罗列一些此级数通项公式的推导方法。要理解本文的内容，读者应当学习过一些组合数学或离散数学的知识。



## 方法一：求解递推关系

考虑
$$
S_n-S_{n-1} = n^2
$$
上式为常系数非线性递推关系。若我们尝试直接求解，则需要先求通解，再求特解，然后将他们线性组合。显然，通解为$1^n=1$，但是特解却并不好求。所以我们换个思路，尝试消去递推关系的非齐次项$n^2$。
$$
\begin{align}
S_n - S_{n-1} &= n^2 \\[2ex]
S_{n-1} - S_{n-2} &= (n-1)^2\\
\end{align}
$$
上述两式相减，并利用$n-1$替换$n$，可得
$$
\begin{align}
S_n -2S_{n-1} + S_{n-2} &= 2n-1\\[2ex]
S_{n-1} -2S_{n-2} + S_{n-3} &= 2n-3 \\
\end{align}
$$
WOW，我们成功让等式右边的降幂了！这暗示着我们可以使用这个方法，将右边的幂函数一直降至常数。当然，随之而来的就是左边的递推关系会越变越复杂。
$$
\begin{align}
S_n -3S_{n-1}  +3S_{n-2} - S_{n-3} &= 2\\[2ex]
S_{n-1} -3S_{n-2}  +3S_{n-3} - S_{n-4} &= 2\\
\end{align}
$$
化简之后，我们得到了一个相当复杂的递推关系，但庆幸的是它是齐次的，我们无需再考虑通解。
$$
S_n -4S_{n-1}  +6S_{n-2} - 4S_{n-3} + S_{n-4} = 0 \\
$$
我们列出上式的特征方程：
$$
\begin{align}
\lambda^4-4\lambda^3+6\lambda^2-4\lambda + 1 &= 0
\end{align}
$$
先别急着解它，我断言，它一定只有$1$这一个实数根。否则，$S_n$就将会以指数级的速度增长了，这与我们的直觉不符。当然，不难发现，
$$
\begin{align}
\lambda^4-4\lambda^3+6\lambda^2-4\lambda + 1 = (\lambda -1)^4
\end{align}
$$
所以$1$的的确确是它的四重根。那么该递推关系的解应该是：
$$
S_n = An^3+Bn^2+Cn+D
$$
注意到，根据$S_n$的定义：
$$
\begin{align}
S_0&=0\\
S_1&=1 \\
S_2&=5\\
S_3&=14 \\
&\vdots
\end{align}
$$
将前四项代入，得到一个线性方程组：
$$
\begin{cases}
D=0 \\[2ex]
A+B+C+D =1 \\[2ex]
8A+4B+2C+D = 5 \\[2ex]
27A +9B+3C +D = 14\\[2ex]
\end{cases}
$$
容易解得：
$$
A=\frac 13 ,\; B=\frac12, \; C=\frac16, \;D=0
$$
也就是
$$
S_n = \frac13 n^3+\frac12n^2+ \frac16n =\frac {n(n+1)(2n+1)} 6
$$
回顾我们的求解过程，我们实际上是在一个滚动相消的过程中消去了等式右边的$n^2$，从而将原来的递推关系化为了一个线性常系数递推关系。

上述过程也给了我们一个启发，我们实际上可以使用此方法求解任意幂函数的前$n$项和，虽然可能最终得到的递推关系很复杂，但都会转化为解一个线性方程组的问题。



## 方法二：扰动法

扰动法是求解和式的一种技巧。它的思想是给和式加上一项，同时将和式中的某一项分离出去，然后将剩下部分尝试使用和式表示出来。

假设我们有一未知和式：
$$
T_n = \sum_{0\leqslant k \leqslant n} a_k
$$
根据扰动法的思想，给它的左右两边加上一项，同时右边分离一项。
$$
\begin{align}
T_n +a_{n+1} &= a_0 + \sum_{1\leqslant k \leqslant n+1} a_k \\[2ex]
&= a_0+\sum_{0\leqslant k \leqslant n} a_{k+1}
\end{align}
$$
比如，使用扰动法求解一个几何级数的和：
$$
T_n = \sum_{0\leqslant k \leqslant n} ax^k
$$
方程两边同时加上$a_{n+1}$：
$$
\begin{align}
T_n +ax^{n+1} &= a + \sum_{1\leqslant k \leqslant n+1} ax^k \\[2ex]
&= a+\sum_{0\leqslant k \leqslant n} ax^{k+1} \\[2ex]
&= a+ax\sum_{0\leqslant k \leqslant n} x^{k}\\[2ex]
&= a+axS_n
\end{align}
$$
注意上面的最后一步，我们尝试将右边的和式用$S_n$表示出来，即可得到
$$
T_n=\frac {a(1-x^{n+1})}{1-x}
$$
回到我们的问题中来。
$$
S_n = \sum_{0\leqslant k \leqslant n} k^2
$$
尝试使用扰动法：
$$
\begin{align}
S_n + (n+1)^2 &= \sum_{0\leqslant k \leqslant n} (k+1)^2 \\[2ex]
&= \sum_{0\leqslant k \leqslant n} k^2 + 2k + 1 \\[2ex]
&= \sum_{0\leqslant k \leqslant n} k^2 + 2\sum_{0\leqslant k \leqslant n} k + n+1 \\[2ex]
&= \sum_{0\leqslant k \leqslant n} k^2 + n^2+2 n+1 \\[2ex]
&= S_n + n^2+2 n+1 \\[2ex]
\end{align}
$$
不幸的是，我们得到的等式中左右两边相互抵消了。仔细观察一下就不难发现，此前用扰动法求几何级数的过程中，$S_n$的系数会随着和式的变化发生改变，然而对于$n^2$的和却不是这样。

虽然没能得出结果，但是这个求解过程也并非完全无用，注意到，我们在求解的过程中出现了比$k^2$低阶的$k$的和式。这给我们一个启发，如果我们使用扰动法求$n^3$的和式，是否会在过程中出现$n^2$的和式呢？

设
$$
T_n = \sum_{0\leqslant k \leqslant n} k^3
$$
再次尝试使用扰动法：
$$
\begin{align}
T_n + (n+1)^3 &= \sum_{0\leqslant k \leqslant n} (k+1)^3 \\[2ex]
&= \sum_{0\leqslant k \leqslant n} k^3 + 3k^2 +3k + 1 \\[2ex]
&= \sum_{0\leqslant k \leqslant n} k^3 + 3\sum_{0\leqslant k \leqslant n} k^2 + 3\sum_{0\leqslant k \leqslant n} k + n+1 \\[2ex]
&= T_n + 3S_n + \frac{3n(n+1)}{2} + n+1
\end{align}
$$
奇迹再次发生了！只需稍微移项整理，我们就得到了想要的$S_n$。
$$
S_n =\frac {n(n+1)(2n+1)} 6
$$
这是一个激动人心的结果。





## 方法三：离散微积分

离散微积分（又称有限微积分），是一种基于差分方程的对应与连续函数中微积分的方法。这里我们仅介绍使用它解决该问题，详细的关于离散微积分的介绍可以参考具体数学。

设$x^{\underline k}=x(x-1)\cdots(x-k+1)$，其中$x^{\underline k}$读作“$x$直降$k$次”。

对应于微分算子，我们引入差分算子$\Delta$。差分算子作用于$x^{\underline k}$上时，类似于连续微积分，有
$$
\Delta x^{\underline k} = kx^{\underline {k-1}}
$$
差分算子的逆算子为求和算子$\sum$，类似于积分算子$\int$，有
$$
\sum kx^{\underline {k-1}} \delta x =   x^{\underline k} 
$$
其中$\delta x$表示$x$的差分。根据$x^{\underline k}$的定义，我们有：
$$
x ^{\underline 2} =x (x-1)= x^2-x
$$
即：
$$
x^2 = x ^{\underline 2} +x ^{\underline 1}
$$
于是解得：
$$
\begin{align}
\sum_{0\leqslant k < n} x^2 \delta x &= \sum_{0\leqslant k < n} x ^{\underline 2} +x ^{\underline 1} \delta x \\[2ex]
&= \frac13x^{\underline 3} + \frac12 x^{\underline 2} \Big |_0^n\\[2ex]
& = \frac {n(n-1)(2n-1)} 6 \\[2ex]
\end{align}
$$
注意，此处求出的是前$n-1$项和$S_{n-1}$，我们用$n+1$替换结果中的$n$，即得：
$$
S_n =\frac {n(n+1)(2n+1)} 6
$$
求解过程异常简洁！另外，离散微积分告诉我们这样一个事实：{% label primary@从渐进分析的角度来说，所有幂方级数的和都要比它的末项高一阶。 %}



## 方法四：生成函数

生成函数实际上是解递推关系的基础，过程比直接求解地推关系要稍麻烦些，但原理是一样的，在此不做赘述。





