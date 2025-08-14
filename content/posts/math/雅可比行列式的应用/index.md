---
# 1) 新建一篇文章（会创建 posts/my-first-post/index.md）
# hugo new --kind post-bundle posts/my-first-post

# 2) 把配图放到同目录
# posts/my-first-post/
# ├─ index.md
# └─ cover.jpg  ← featuredImage 可填 "cover.jpg"

# 基本
title: "雅可比行列式的应用"
subtitle: ""
date: 2019-11-05
lastmod: 
draft: false

# URL / SEO
slug: "雅可比行列式的应用"
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



雅可比行列式在多元函数微积分中的应用非常广泛，我们在很多地方都能见到它的身影。合理运用雅可比行列式，可以简化我们的计算。

本文主要总结了一些雅可比行列式在多元函数微积分的计算方面的应用。


## 什么是雅可比行列式

> 雅可比行列式通常称为雅可比式(Jacobian)，它是以n个n元函数的偏导数为元素的行列式 。事实上，在函数都连续可微（即偏导数都连续）的前提之下，它就是函数组的微分形式下的系数矩阵（即雅可比矩阵）的行列式。

雅可比行列式来源于多元函数微分的方程组，所以自然离不开函数的微分。所谓行列式，不过是一个多元函数微分方程组的系数罢了。

## 求隐函数方程组的导数

所以在引出雅可比行列式之前，我们有必要先探究一下多元函数方程组和它的偏导数的性质。

### 隐函数存在定理

根据隐函数存在定理，我们知道一个二元方程能够唯一确定一个一元函数。

> **一元隐函数存在定理** 设二元函数$F(x,y)$满足$F(x_0,y_0) = 0, F_y(x_0,y_0)\neq 0$，且$F$在点$(x_0,y_0)$的某邻域内有连续偏导数。则方程$F(x,y)=0$在点$(x_0,y_0)$的某一邻域中唯一确定了一个具有连续导数的函数$y=f(x)$。满足$y_0=f(x_0)$且$F(x,f(x))\equiv 0$。其偏导数为：
> $$
> \frac{\mathrm{d}y}{\mathrm{d}x}=-\frac{F_x}{F_y}
> $$
> 
> **二元隐函数存在定理** 设三元函数$F(x,y,z)$满足$F(x_0,y_0,z_0) = 0, F_z(x_0,y_0 ,z_0)\neq 0$，$F$在点$(x_0,y_0,z_0)$的某邻域内有连续偏导数，方程$F(x,y,z)=0$在点$(x_0,y_0,z_0)$的某一邻域中唯一确定了一个具有连续导数的二元函数$z=f(x,y)$，满足$z_0=f(x_0,y_0)$且$F(x,y,f(x,y))\equiv 0$。其偏导数为：
> $$
> \frac {\partial z}{\partial  x}
> = -\frac{F_x}{F_z}，
> \frac {\partial  z}{\partial  y}
> = -\frac{F_y}{F_z}
> $$
> 

以上定理可以不难推导至$n$元函数中去，也就是说在满足可连续偏导的条件下，任何一个$n$元隐函数$F(x_1,x_2,\cdots,x_n)$唯一确定了一个具有连续偏导的$n-1$元的函数$x_n = F(x_1,x_2,\cdots,x_{n-1})$。

但是要知道，隐函数不仅仅能够产生于一个方程中，也能产生于方程组之中。我们将以上隐函数存在定理的内容推广到方程组的情形中去。

考虑一个四元函数方程组：
$$
\left\{\begin{array}{l}F(x,y,u,v)=0 \\[2ex] 
G(x,y,u,v)=0\end{array}\right.
$$
由于这个方程组有2个方程，我们猜想它应当确定了2个二元函数。不妨假设他们分别是$u(x,y),v(x,y)$。

于是上述方程就是：
$$
\left\{\begin{array}{l}F(x,y,u(x,y),v(x,y))=0 \\[2ex] 
G(x,y,v(x,y),v(x,y))=0\end{array}\right.
$$

我们对方程组的两边同时关于$x$求偏导数，得到：

$$
\left\{\begin{array}{l}
F_x+F_u \frac{\partial u}{\partial x} + F_v \frac{\partial v}{\partial x}=0 \\[2ex] 
G_x+G_u \frac{\partial u}{\partial x} + G_v \frac{\partial v}{\partial x}=0 \\[2ex] 
\end{array}\right.
$$

稍加整理，得到以下形式：

$$
\left\{\begin{array}{l}
F_u \frac{\partial u}{\partial x} + F_v \frac{\partial v}{\partial x}= -F_x\\[2ex] 
G_u \frac{\partial u}{\partial x} + G_v \frac{\partial v}{\partial x}= -G_x \\[2ex] 
\end{array}\right.
$$

上式就是一个线性方程组的标准形式。我们还可以写成矩阵乘法：

$$
\begin{aligned}
\begin{bmatrix}F_u&F_v\\ G_u&G_v\end{bmatrix}
\begin{bmatrix}\dfrac{\partial u}{\partial x}\\ \dfrac{\partial v}{\partial x}\end{bmatrix}
&=
\begin{bmatrix}-F_x\\ -G_x\end{bmatrix}
\end{aligned}
$$

于是乎，我们引出雅可比行列式的概念。

我们称上式的系数行列式 $J=\begin{vmatrix}F_u&F_v\\G_u&G_v \end{vmatrix}$ 为**雅可比行列式**。通常我们还可以将它记为$\frac{\partial (F, G)}{\partial(u, v)}$，意思是分别由$F$对$u,v$求偏导，$G$对$u,v$求偏导，然后将偏导数逐行排成一个行列式。

根据解线性方程组的克拉默法则，我们知道当雅可比行列式不为零时，该线性方程组有解。此时我们就能解出函数$u(x,y)$和$v(x,y)$。

于是，就可引出如下的方程组情形下的隐函数存在定理：

> **方程组的隐函数存在定理** 设函数$ F(x, y, u, v), G(x,y, u, v)$ 满足条件：
>
> 1. 在点 $P_{0}\left(x_{0}, y_{0}, u_{0}, v_{0}\right)$的某一邻域内，$F(x, y, u, v), G(x, y, u, v)$具有一阶连续偏导数
> 2. 且$F\left(x_{0}, y_{0}, u_{0}, v_{0}\right)=0, G\left(x_{0}, y_{0}, u_{0}, v_{0}\right)=0$
> 3. 雅可比行列式$J=\frac{\partial (F, G)}{\partial(u, v)}=\begin{vmatrix}F_u&F_v\\G_u&G_v \end{vmatrix} \neq0$
>
> 那么该方程组$F(x, y, u, v)=0, G(x, y, u, v)=0$ 在点$P_{0}\left(x_{0}, y_{0}, u_{0}, v_{0}\right)$的某邻域内惟一确定了两个具有连续偏导数的二元隐函数$u=u(x, y), v=v(x, y)$

根据克拉默法则，解得得：
$$
\begin{array}{l}
\frac{\partial u}{\partial x}=-\frac{1}{J} \frac{\partial(F, G)}{\partial(x, v)}\\
\frac{\partial v}{\partial x}=-\frac{1}{J} \frac{\partial(F, G)}{\partial(u, x)}
\end{array}
$$
同理，我们还可以求出$u,v$对$y$的偏导数：
$$
\begin{array}{l}
\frac{\partial u}{\partial y}=-\frac{1}{J} \frac{\partial(F, G)}{\partial(y, v)} \\
\frac{\partial v}{\partial y}=-\frac{1}{J} \frac{\partial(F, G)}{\partial(u, y)}
\end{array}
$$
实际上，上述结论可以直接推广到$n$个$m+n$元方程所组成的方程组的情形中去（确定了$n$个$m$元函数）。

至此，我们虽然总结出了一个求解多元函数偏导的通式，但直接记忆公式反而不方便，应该掌握上述方法的**构思过程**，从方程组出发，结合复合函数的求导法则，直接求解方程组，而不是硬套公式。

## 求两平面相交曲线的切线

雅可比行列式的身影无处不在，包括在多元函数微分几何学的应用上。

我们知道，空间曲线的方程可以有两种形式，其一是由参数方程确定：$\left\{\begin{array}{l}x=\varphi(t)\\ y=\psi(t)\\z=\omega(t)\end{array}\right.$，其二是由两个曲面的方程所确定：$\left\{\begin{array}{l}F(x,y,z)=0 \\G(x,y,z)=0\end{array}\right.$。

当曲线的由它的参数方程给出时，我们想要求该曲线在某点处的切线很简单，只需要将$x,y,z$分别对$t$求导，然后代入定点的参数$t_o$即可。

但若曲线由两个曲面的方程所确定，我们想要求该曲线在定点$P_0(x_0,y_0,z_0)$处的切线方程，此时情况会稍微麻烦一些。注意到前述所提到的方程组的隐函数存在定理，我们知道此时方程组$\left\{\begin{array}{l}F(x,y,z)=0 \\G(x,y,z)=0\end{array}\right.$可以唯一确定两个函数$\left\{\begin{array}{l}y=\varphi(x)\\z=\psi(x)\end{array}\right.$。若我们取$x$为参数，实际上得到该曲线的参数方程：$\left\{\begin{array}{l}x=x \\y=\varphi(x)\\z=\psi(x)\end{array}\right.$。于是我们只需要求出$\frac{\mathrm dy}{\mathrm dx}$与$\frac{\mathrm dz}{\mathrm dx}$的表达式，就得到切向量$(1,\frac{\mathrm dy}{\mathrm dx},\frac{\mathrm dz}{\mathrm dx})$。然后将定点$P_0$的坐标$(x_0,y_0,z_0)$代入，便可求出曲线的切线方程。

我们考虑一个由两个曲面方程所确定的曲线：$\left\{\begin{array}{l}x^2+y^2 + z^2=2 \\ x+y+z=0\end{array}\right.$。考虑求此切线位于点$(1,-1,0)$处的切线方程。

考虑对之前确定隐函数的偏导数的过程，方程两端同时对$x$求偏导，并移项整理：
$$
\left\{
\begin{array}{l}
y \frac{\mathrm{d} y}{\mathrm{~d} x}+z \frac{\mathrm{d} z}{\mathrm{~d} x}=-x, \\[2ex]
\frac{\mathrm{d} y}{\mathrm{~d} x}+\frac{\mathrm{d} z}{\mathrm{~d} x}=-1 \\
\end{array}\right.
$$
根据克拉默法则：
$$
\frac{\mathrm{d} y}{\mathrm{~d} x}=\frac{\left|
\begin{array}{ll}
-x & z \\
-1 & 1
\end{array}\right|}{\left|\begin{array}{ll}
y & z \\
1 & 1
\end{array}\right|}=\frac{z-x}{y-z} \\[2ex]
\frac{\mathrm{d} z}{\mathrm{~d} x}=\frac{\left|\begin{array}{ll}
y & -x \\
1 & -1
\end{array}\right|}{\left|\begin{array}{ll}
y & z \\
1 & 1
\end{array}\right|}=\frac{x-y}{y-z}  \\
$$
再将定点值$(1,-1,0)$代入：
$$
\left.\frac{\mathrm{d} y}{\mathrm{~d} x}\right|_{((1,-1,0))}=1,\left.\frac{\mathrm{d} z}{\mathrm{~d} x}\right|_{((1,-1,0))}=-2
$$
所以得到切向量：$(1,1,-2)$，所求切线方程为：$\frac{x-1}{1} = \frac{y+1}{1} = \frac{z}{2}$。

## 利用坐标变换巧妙计算重积分

在求解二重积分时，我们常用到各种坐标系变换，如球面坐标变换，极坐标变换等等。实际上，坐标系变换的本质是一种函数空间上的映射。从代数中的知识我们可以知道，任何一个映射都可以用矩阵表示出来，而该系数矩阵所对应的行列式实际上就是变换的缩放因子。当映射为线性时，缩放因子应当为一个常数。

### 二重积分的换元法

设函数 $f(x, y)$ 在 $x O y$ 平面上的有界闭区域 $D$ 上连续, 变换
$$
\left\{\begin{array}{l}
x=x(u, v), \\[2ex]
y=y(u, v),
\end{array} \quad(u, v) \in D^{\prime}\right.
$$
将 $u O v$ 面上的平面区域 $D^{\prime} $ 一 一对应地映射为 $x O y$ 平面的区域 $D$。函数 $x=$ $x(u, v), y=y(u, v)$ 在 $D^{\prime}$ 上有一阶连续偏导数, 且雅可比行列式 $J(u, v)=$ $\frac{\partial(x, y)}{\partial(u, v)} \neq 0,(u, v) \in D^{\prime}$ 时, 则有二重积分的换元公式：
$$
\iint_{D} f(x, y) \mathrm{d} \sigma=\iint_{D^{\prime}} f[x(u, v), y(u, v)]\left|\frac{\partial(x, y)}{\partial(u, v)}\right| \mathrm{d} u \mathrm{~d} v
$$
如果雅可比式 $J(u, v)$ 只在 $D^{\prime}$ 内个别点上,或一条曲线上为 零，而在其他点上不为零，那么上述换元公式仍成立。
考虑变换为极坐标 $x=\rho \cos \theta, y=\rho \sin \theta$ 的情形，雅可比式
$$
J=\left|\begin{array}{ll}
\frac{\partial x}{\partial \rho} & \frac{\partial x}{\partial \theta} \\
\frac{\partial y}{\partial \rho} & \frac{\partial y}{\partial \theta}
\end{array}\right|=\left|\begin{array}{cc}
\cos \theta & -\rho \sin \theta \\
\sin \theta & \rho \cos \theta
\end{array}\right|=\rho
$$
它仅在 $\rho=0$ 处为零, 故不论闭区域 $D^{\prime}$ 是否含有极点，换元公式仍成立。即有
$$
\iint_{D} f(x, y) \mathrm{d} x \mathrm{~d} y=\iint_{D^{\prime}} f(\rho \cos \theta, \rho \sin \theta) \rho \mathrm{d} \rho \mathrm{d} \theta
$$
这就是我们所熟知的二重积分的极坐标变换。

### 三重积分的换元法则

设函数 $f(x, y, z)$ 在空间有界闭区域 $\Omega$ 上连续, 定义在 $u v w$ 空间上的函数组
$$
\left\{\begin{array}{l}
x=x(u, v, w)\\[2ex] y=y(u, v, w) \\[2ex]  z=z(u, v, w) [2ex] 
\end{array} \quad (u, v, w) \in \Omega^{\prime} \right.
$$
在$\Omega^{\prime}$上有连续偏导数, 且将$u v w$中的区域$\Omega^{\prime}$一一对应地变换为 $x y z$ 空间的区域 $\Omega$，若函数组的雅可比行列式 $J=\frac{\partial(x, y, z)}{\partial(u, v, w)} \neq 0$，则有三重积分的换元公式
$$
\iiint_{\Omega} f(x, y, z) \mathrm{d} V=\iiint_{\Omega^{\prime}} f[x(u, v, w), y(u, v, w), z(u, v, w)]|J| \mathrm{d} u \mathrm{~d} v \mathrm{~d} w
$$
如球面坐标变换: $x=r \cos \theta \sin \varphi, y=r \sin \theta \sin \varphi, z=r \cos \varphi$，则
$$
J=\frac{\partial(x, y, z)}{\partial(r, \theta, \varphi)}=\left|\begin{array}{ccc}
\cos \theta \sin \varphi & -r \sin \theta \sin \varphi & r \cos \theta \cos \varphi \\
\sin \theta \sin \varphi & r \cos \theta \sin \varphi & r \sin \theta \cos \varphi \\
\cos \varphi & 0 & -r \sin \varphi
\end{array}\right|=-r^{2} \sin \varphi
$$
故
$$
\iiint_{\Omega} f(x, y, z) \mathrm{d} V=\iiint_{\Omega^{\prime}} f(r\sin \varphi \cos \theta , r \sin \varphi \sin \theta , r \cos \varphi) r^{2} \sin \varphi \mathrm{d} \theta \mathrm{d} \varphi \mathrm{d} r
$$
与之前推导出的球面坐标的变换公式一致。

### 应用实例

考虑求曲线 $\left(\frac{x}{a}+\frac{y}{b}\right)^{2}=\frac{x}{a}-\frac{y}{b}(a>0, b>0)$ 与 $y=0$ 所围的区域的面积。

所求面积实际上就是$S=\iint_{D} \mathrm{~d} \sigma$，但在直角坐标系下计算显然很复杂。

我们考虑作变换： 令 $u=\frac{x}{a}+ \frac{y}{b}, v=\frac{x}{a}-\frac{y}{b}$，则边界曲线 $\left(\frac{x}{a}+\frac{y}{b}\right)^{2}=\frac{x}{a}-\frac{y}{b}$ 转化为 $u^{2}=v$, 而 由 $y=0$ 得 $u=\frac{x}{a}, v=\frac{x}{a}$, 即 $u=v$。

故所求的面积转化为求在 $u O v$ 平面上由 $u^{2}=v$ 及 $u=v$ 所围的 区域的面积。

因由变换可得$x=\frac{a}{2}(u+v), y=\frac{b}{2}(u-v)$，故雅可比行列式为
$$
\frac{\partial(x, y)}{\partial(u, v)}=\left|\begin{array}{cc}
\frac{a}{2} & \frac{a}{2} \\
\frac{b}{2} & -\frac{b}{2}
\end{array}\right|=-\frac{a b}{2}
$$
根据换元公式，得
$$
S=\iint_{D} \mathrm{~d} \sigma=\iint_{D^{\prime}}\left|\frac{\partial(x, y)}{\partial(u, v)}\right| \mathrm{d} \sigma=\iint_{D^{\prime}} \frac{a b}{2} \mathrm{~d} \sigma=\int_{0}^{1} \mathrm{~d} u \int_{u^{2}}^{u} \frac{a b}{2} \mathrm{~d} v=\frac{a b}{12}
$$







