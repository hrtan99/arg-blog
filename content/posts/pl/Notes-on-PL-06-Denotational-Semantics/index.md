---
# 1) 新建一篇文章（会创建 posts/my-first-post/index.md）
# hugo new --kind post-bundle posts/my-first-post

# 2) 把配图放到同目录
# posts/my-first-post/
# ├─ index.md
# └─ cover.jpg  ← featuredImage 可填 "cover.jpg"

# 基本
title: "Notes on PL 06: Denotational Semantics"
subtitle: ""
date: 2024-10-02
lastmod: 
draft: false

# URL / SEO
slug: "Notes-on-PL-06-Denotational-Semantics"
aliases: []
description: ""
# 若你用自定义永久链接，可在 config 里用 :slug
# [permalinks]
# posts = "/blog/:year/:slug/"

# 分类
categories: ["Computer Science"]
tags: ["Programming Languages"]
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


We have now seen two operational models for programming languages: small-step and large-step. In this nnote, we consider a different semantic model, called denotational semantics.

The idea in denotational semantics is to express the meaning of a program as the mathematical function that expresses what the program computes. We can think of an IMP program $c$ as a function from stores to stores: given an an initial store, the program produces a final store. For example, the program $foo:= bar+1$ can be thought of as a function that when given an input store $\sigma$, produces a final store $\sigma^\prime$ that is identical to $\sigma$ except that it maps foo to the integer $\sigma(bar)+1$; that is, $\sigma^{\prime}=\sigma[ foo \mapsto\sigma(bar)+1]$. We will model programs as functions from input stores to output stores. As opposed to operational models, which tell us how programs execute, the denotational model shows us  what programs compute.

### A Denotational Semantics for IMP

For each program $c$, we write $\mathcal C[\![c]\!]$ for the denotation of $c$, that is, the mathematical function that $c$ represents:
$$
\mathcal C[\![c]\!]:\mathbf{Store}\rightharpoonup\mathbf{Store}.
$$
Note that $\mathcal C[\![c]\!]$ is actually a partial function (as opposed to a total function), both because the store may not be defined on the free variables of the program and because program may not terminate for certain input stores. The function $\mathcal C[\![c]\!]$ is not defined for non-terminating programs as they have no corresponding output stores.

We will write $\mathcal C[\![c]\!]\sigma$ for the result of applying the function $\mathcal C[\![c]\!]$ to the store 𝜎. That is, if $f$ is the function that $\mathcal C[\![c]\!]$ denotes, then we write $\mathcal C[\![c]\!]\sigma$ to mean the same thing as $f(\sigma)$.

We must also model expressions as functions, this time from stores to the values they represent. We will write $\mathcal A[\![a]\!]$ for the denotation of arithmetic expression $a$, and $\mathcal B[\![b]\!]$ for the denotation of boolean expression $b$.
$$
\begin{aligned}\mathcal{A}[\![a]\!]&:\mathbf{Store}\rightharpoonup\mathbf{Int}\\\mathcal{B}[\![b]\!]&:\mathbf{Store}\rightharpoonup\{\mathbf{true},\mathbf{false}\}\end{aligned}
$$
Now we want to define these functions. To make it easier to write down these definitions, we will describe (partial) functions using sets of pairs. More precisely, we will represent a partial map $f:A\rightharpoonup B$ as a set of pairs $F=\{(a,b)\mid a\in A$ and $b={f}(a)\in B\}$ such that, for each $a\in A$,there is at most one pair of the form $(a,\_)$ in the set. Hence $(a,b)\in F$ is the same as $b=f(a)$.

We can now define denotations for IMP. We start with the denotations of expressions:
$$
\begin{aligned}
\mathcal{A}[\![n]\!]=&\,\{(\sigma,n)\}\\
\mathcal{A}[\![x]=&\,\{(\sigma,\sigma(x))\}\\
\mathcal{A}[\![a_1+a_2]\!]=&\,\{(\sigma,n)\mid(\sigma,n_1)\in\mathcal{A}[\![a_1]\!]\wedge(\sigma,n_2)\in\mathcal{A}[\![a_2]\!]\wedge n=n_1+n_2\}\\\\
\mathcal B[\![true]\!]=&\,\{(\sigma,\mathbf{true})\}\\
B[\![false]\!]=&\,\{(\sigma,\mathbf{false})\}\\
\mathcal{B}[\![a_1<a_2]\!]=&\,\{(\sigma,\mathbf{true})\mid(\sigma,n_1)\in\mathcal{A}[\![a_1]\!]\wedge(\sigma,n_2)\in\mathcal{A}[\![a_2]\!]\wedge n_1<n_2\}\cup\\
&\,\{(\sigma,\mathbf{false})\mid(\sigma,n_1)\in\mathcal{A}[\![a_1]\!]\wedge(\sigma,n_2)\in\mathcal{A}[\![a_2]\!]\wedge n_1\geq n_2\}
\end{aligned}
$$
The denotations for commands are as follows:
$$
\begin{aligned}
\mathcal C[\![\mathbf{skip}]\!]&=\{(\sigma,\sigma)\}\\
\mathcal C[\![x:=a]\!]&=\{(\sigma,\sigma[x\mapsto n])\mid(\sigma,n)\in\mathcal{A}[\![a]\!]\}\\
\mathcal C[\![c_1;c_2]\!]&=\{(\sigma,\sigma^{\prime})\mid\exists\sigma^{\prime\prime}.((\sigma,\sigma^{\prime\prime})\in \mathcal C[\![c_1]\!]\wedge(\sigma^{\prime\prime},\sigma^{\prime})\in \mathcal C[\![c_2]\!])\}
\end{aligned}
$$
Note that $\mathcal C[\![c_1;c_2]\!]=\mathcal{C}[\![c_2]\!]\circ\mathcal{C}[\![c_1]\!]$, where $\circ$ is the composition of relations, defined as follows: if $R_1\subseteq A \times B$ and $R_2\subseteq B \times C$ then $R_2 \circ R_1\subsetneq A \times C$ is $R_2 \circ R_1$ $=$ $\{(a,c)\mid\exists b\in B.$ $(a,b)\in R_1\land(b,c)\in R_2\}$. If $\mathcal C[\![c_1]\!]$ and $\mathcal C[\![c_2]\!]$ are total functions, then $\circ$ is function composition.
$$
\small\begin{aligned}
\mathcal C[\![\mathbf{if~}b~\mathbf{then~}c_1~\mathbf{else~} c_2]\!]=&\,\{(\sigma,\sigma^{\prime})\mid(\sigma,\mathbf{true})\in B[\![b]\!]\wedge(\sigma,\sigma^{\prime})\in \mathcal C[\![c_1]\!]\}\cup\\
&\,\{(\sigma,\sigma^{\prime})\mid(\sigma,\mathbf{false})\in\mathcal{B}[\![b]\!]\wedge(\sigma,\sigma^{\prime})\in\mathcal{C}[\![c_2]\!]\}\\
\mathcal C[\![\mathbf{while~}b~\mathbf{do~}c]\!]=&\,\{(\sigma,\sigma)\mid(\sigma,\mathbf{false})\in\mathcal{B}[\![b]\!]\}\cup\\
&\,\{(\sigma,\sigma^{\prime})\mid(\sigma,\mathbf{true})\in\mathcal{B}[\![b]\!]\wedge\exists\sigma^{\prime\prime}.((\sigma,\sigma^{\prime\prime})\in\mathcal{C}[\![c]\!]\wedge(\sigma^{\prime\prime},\sigma^{\prime})\in\mathcal{C}[\![\mathbf{while~}b~\mathbf{do~}c]\!])\}
\end{aligned}
$$
But now we have a problem: the last “definition” is not really a definition because it expresses $\mathcal C[\![\mathbf{while~}b~\mathbf{do~}c]\!]$ in terms of itself! This is not a definition but a recursive equation. What we want is the solution to this equation.

### Fixed Points

We gave a recursive equation that the function $\mathcal C[\![\mathbf{while~}b~\mathbf{do~}c]\!]$ must satisfy. To understand some of the issues involved, let’s consider a simpler example. Consider the following equation for a function $f:\mathbb{N}\to\mathbb{N}.$
$$
f(x)=
\begin{cases}0&\mathrm{if~}x=0\\
f(x-1)+2x-1&\mathrm{otherwise}&
\end{cases}
$$
This is not a definition for $f$, but rather an equation that we want $f$ to satisfy. What function, or functions, satisfy this equation for $f$? The only solution to this equation is the function $f(x)=x^2$.

In general, there may be no solutions for a recursive equation ( e. g, there are no functions $g:\mathbb{N}\to\mathbb{N}$ In general, there may be no solutions for a recursive equation ( e. g, there are no functions ${g: }$ $\mathbb{N}\to\mathbb{N}$ that satisfy the recursive equation $g(x)=g(x)+1)$, or multiple solutions (e.g., find two functions $g: \mathbb{R} \to \mathbb{R}$ that satisfy $g(x)=4\times g(\frac x2)).$

We can compute solutions to such equations by building successive approximations. Each approximation is closer and closer to the solution. To solve the recursive equation for $f$, we start with the partial function $f_0= \emptyset$ ( i. e, $f_0$ is the empty relation; it is a partial function with the empty set the partial function $f_0= \emptyset$ ( i. e, $f_0$ is the empty relation; it is a partial function with the empty set for it's domain). We compute successive approximations using the recursive equation.
$$
\begin{aligned}
f_{0}&=\emptyset\\
f_{1}&=\begin{cases}0&\mathrm{if~}x=0\\
f_0(x-1)+2x-1&\mathrm{otherwise}&\end{cases}\\
&=\{(0,0)\}\\
f_{2}&=\begin{cases}0&\mathrm{if~}x=0\\
f_1(x-1)+2x-1&\mathrm{otherwise}&\end{cases}\\
&=\{(0,0),(1,1)\}\\
f_{3}&=\begin{cases}0&\mathrm{if~}x=0\\
f_2(x-1)+2x-1&\mathrm{otherwise}&\end{cases}\\
&=\{(0,0),(1,1),(2,4)\}\\
&\vdots
\end{aligned}
$$
This sequence of successive approximations $f_i$ gradually builds the function $f(x)=x^2$.

We can model this process of successive approximations using a higher-order function $F$ that takes one approximation $f_k$ and returns the next approximation $f_{k+1}$:
$$
F:(\mathbb{N}\rightharpoonup\mathbb{N})\to(\mathbb{N}\rightharpoonup\mathbb{N})
$$
where
$$
(F(f))(x)=\begin{cases}0&\mathrm{if~}x=0\\f(x-1)+2x-1&\mathrm{otherwise}&&\end{cases} \quad\quad(1)
$$
A solution to the recursive equation $1$ is a function $f$ such that $f=F(f)$. In general, given a function $F:A\to A$, we have that $a\in A$ is a fixed point of $F$ if $F(a)=a.$ We also write $a=fix(F)$ to indicate that $a$ is a least fixed point of $F.$

So the solution to the recursive equation $\color{blue}1$ is a fixed-point of the higher-order function $F.$ We can compute this fixed point iteratively, starting with $f_0=\emptyset$ and at each iteration computing $f_{k+1}=$ $F(f_k).$ The fixed point is the limit of this process:
$$
\begin{aligned}\mathrm{f}&=fix(F)\\&=f_0\cup f_1\cup f_2\cup f_3\cup\ldots\\&=\emptyset\cup F(\emptyset)\cup F(F(\emptyset))\cup F(F(F(\emptyset)))\cup\ldots\\&=\bigcup_{i\geq0}F^i(\emptyset)\end{aligned}
$$

### Denotation for Loops

We can now write the correct denotation case for while loops as the fixed point of a higher-order function:
$$
\begin{aligned}
\mathcal C[\![\mathbf{while~}b~\mathbf{do~}c]\!]=&\,fix(F)\\
\mathrm{where~F}(f)=&\,\{(\sigma,\sigma)\mid(\sigma,\mathbf{false})\in\mathcal{B}[\![b]\!]\}\cup\\
&\,\{(\sigma,\sigma^{\prime})\mid(\sigma,\mathbf{true})\in\mathcal{B}[\![b]\!]\wedge\exists\sigma^{\prime\prime}.(\sigma,\sigma^{\prime\prime})\in\mathcal{C}[\![c]\!]\wedge(\sigma^{\prime\prime},\sigma^{\prime})\in f\}
\end{aligned}
$$






