---
# 1) 新建一篇文章（会创建 posts/my-first-post/index.md）
# hugo new --kind post-bundle posts/my-first-post

# 2) 把配图放到同目录
# posts/my-first-post/
# ├─ index.md
# └─ cover.jpg  ← featuredImage 可填 "cover.jpg"

# 基本
title: "Notes on PL 03 Large Step Semantics"
subtitle: ""
date: 2024-09-09
lastmod:
draft: false

# URL / SEO
slug: "Notes-on-PL-03-Large-Step-Semantics"
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


### Large-Step Operational Semantics

In the last lecture we defined a semantics for our language of arithmetic expressions using a small-step evaluation relation $\rightarrow \subseteq \mathbf{Config}\times \mathbf{Config}$ (and its reflexive and transitive closure $\rightarrow$). In this note we will explore an alternative approach--large-step operational semantics--which yields the final result of evaluating an expression directly.

Defining a large-step semantics boils down to specifying a relation $\Downarrow$ that captures the evaluation of an expression. The $\Downarrow$ relation has the following type:
$$
\Downarrow \,\subseteq(\mathbf{Store\times Exp})\times(\mathbf{Store\times Int}).
$$
We write $\langle\sigma,e\rangle\Downarrow\langle\sigma^\prime,n\rangle$ to indicate that $(\langle\sigma,e\rangle,\langle\sigma',n\rangle)\in\Downarrow.$ In other words, the expression $e$ with store $\sigma$ evaluates in one big step to the final store $\sigma^\prime$ and integer $n$.

We define the relation $\Downarrow$ inductively, using inference rules:
$$
\begin{gathered}
\frac{}{\langle\sigma,n\rangle\Downarrow\langle\sigma,n\rangle}\mathrm{~Int}\quad
\frac{n=\sigma(x)}{\langle\sigma,x\rangle\Downarrow\langle\sigma,n\rangle}\mathrm{~Var}\\[2ex]
\frac{\langle\sigma,e_1\rangle\Downarrow\langle\sigma^{\prime},n_1\rangle\quad\langle\sigma^{\prime},e_2\rangle\Downarrow\langle\sigma^{\prime\prime},n_2\rangle\quad n=n_1+n_2}{\langle\sigma,e_1+e_2\rangle\Downarrow\langle\sigma^{\prime\prime},n\rangle}\mathrm{~Add}\\[2ex]
\frac{\langle\sigma,e_1\rangle\Downarrow\langle\sigma^{\prime},n_1\rangle\quad\langle\sigma^{\prime},e_2\rangle\Downarrow\langle\sigma^{\prime\prime},n_2\rangle\quad n=n_1\times n_2}{\langle\sigma,e_1*e_2\rangle\Downarrow\langle\sigma^{\prime\prime},n\rangle}\mathrm{~Мul}\\[2ex]
\frac{\langle\sigma,e_1\rangle\Downarrow\langle\sigma^{\prime},n_1\rangle\quad\langle\sigma^{\prime}[x\mapsto n_1],e_2\rangle\downarrow\langle\sigma^{\prime\prime},n_2\rangle}{\langle\sigma,x:=e_1;e_2\rangle\Downarrow\langle\sigma^{\prime\prime},n_2\rangle}\mathrm{~Assgn}
\end{gathered}
$$
To illustrate the use of these rules, consider the following proof tree, which shows that evaluating $\langle\sigma,foo:=3\mathrm{~;~}foo*bar\rangle$ using a store $\sigma$ such that $\sigma(bar)=7$ yields $\sigma^{\prime}=\sigma[foo\mapsto3]$ and $21$ as a result:
$$
\frac{
\large\frac{}{\langle\sigma,3\rangle\Downarrow\langle\sigma,3\rangle}\mathrm{~Int~}
\quad
\large
\frac{
  \Large\frac{}
	{\langle\sigma^{\prime},foo\rangle\Downarrow\langle\sigma^{\prime},3\rangle}
	{\mathrm{Var}}
}{\langle\sigma^{\prime},foo*bar\rangle\Downarrow\langle\sigma^{\prime},21\rangle}\mathrm{~Var}}{\langle\sigma,foo:=3;foo*bar\rangle\Downarrow\langle\sigma^{\prime},21\rangle}\mathrm{~Assgn}
$$
A closer look to this structure reveals the relation between small step and large-step evaluation: a depth-first traversal of the large-step proof tree yields the sequence of one-step transitions in small-step evaluation.

### Equivalence of Semantics

A natural question to ask is whether the small-step and large-step semantics are equivalent. The next theorem answers this question affirmatively.

Theorem (Equivalence of semantics). For all expressions $e$, stores $\sigma$ and $\sigma'$, and integers $n$ we have:
$$
\langle\sigma,e\rangle\Downarrow\langle\sigma^{\prime},n\rangle\text{ if and only if }\langle\sigma,e\rangle\to^*\langle\sigma^{\prime},n\rangle
$$
To streamline the proof, we will work with the following definition of the multi-step relation:
$$
\begin{gathered}
\frac{}{\langle\sigma,e\rangle\to^*\langle\sigma,e\rangle}\mathrm{~Refl} \\[2ex]
\frac{\langle\sigma,e\rangle\to\langle\sigma^{\prime},e^{\prime}\rangle\quad\langle\sigma^{\prime},e^{\prime}\rangle\to^*\langle\sigma^{\prime\prime},e^{\prime\prime}\rangle}{\langle\sigma,e\rangle\to^*\langle\sigma^{\prime\prime},e^{\prime\prime}\rangle}\mathrm{~Trans}
\end{gathered}
$$
Proof sketch. We show each direction separately.

$\Longrightarrow$: We want to prove that the following property $P$ holds for all expressions $e\in\mathbf{Exp}$:
$$
P(e)\triangleq\forall\sigma,\sigma^{\prime}\in\mathbf{Store}.\forall n\in\mathbf{Int}.\langle\sigma,e\rangle\Downarrow\langle\sigma^{\prime},n\rangle\Longrightarrow\langle\sigma,e\rangle\to^*\langle\sigma^{\prime},n\rangle
$$
We proceed by structural induction on $e$. We have to consider each of the possible axioms and inference rules for constructing an expression.

**CASE** $e=x{:}$ Assume that $\langle\sigma,x\rangle\Downarrow\langle\sigma^\prime,n\rangle.$ That is, there is some derivation in the large-step operational semantics whose conclusion is $\langle\sigma,x\rangle\Downarrow\langle\sigma,n\rangle.$ There is only one rule whose conclusion matches the configuration $\langle\sigma,x\rangle{:}$ the large-step rule $Var$. Thus, we have $n=\sigma(x)$ and $\sigma^\prime=\sigma.$ By the small-step rule $Var$, we also have $\langle\sigma,x\rangle\to\langle\sigma,n\rangle.$ By the $Refl$ and $Trans$ rules, we conclude that $\langle\sigma,x\rangle\to^*\langle\sigma,n\rangle$, which finishes the case.

**CASE** $e=n$: Assume that $\left\langle\sigma,n\right\rangle\Downarrow\left\langle\sigma^{\prime},n^{\prime}\right\rangle.$ There is only one rule whose conclusion matches $\langle\sigma,n\rangle$ the large-step rule $Int$. Thus, we have $n^\prime=n$ and $\sigma^\prime=\sigma$ and so $\langle\sigma,n\rangle\to^*\langle\sigma,n\rangle$ by the $Refl$ rule.

**CASE** $e = e_1 + e_2$: This is an inductive case. We want to prove that if $P(e_1)$ and $P(e_2)$ hold, then

$P(e)$ also holds. Let’s write out $P(e_1)$, $P(e_2)$, and $P(e)$ explicitly.
$$
\begin{array}{rcl}P(e_{1})&=&\forall n,\sigma,\sigma^{\prime}.\langle\sigma,e_{1}\rangle\Downarrow\langle\sigma^{\prime},n\rangle\Longrightarrow\langle\sigma,e_{1}\rangle\to^{*}\langle\sigma^{\prime},n\rangle\\P(e_{2})&=&\forall n,\sigma,\sigma^{\prime}.\langle\sigma,e_{2}\rangle\Downarrow\langle\sigma^{\prime},n\rangle\Longrightarrow\langle\sigma,e_{2}\rangle\to^{*}\langle\sigma^{\prime},n\rangle\\P(e)&=&\forall n,\sigma,\sigma^{\prime}.\langle\sigma,e_{1}+e_{2}\rangle\Downarrow\langle\sigma^{\prime},n\rangle\Longrightarrow\langle\sigma,e_{1}+e_{2}\rangle\to^{*}\langle\sigma^{\prime},n\rangle
\end{array}
$$
Assume that $P(e_1)$ and $P(e_2)$ hold. Also assume that there exist $\sigma,\sigma^\prime$ and $n$ such that $\langle\sigma,e_1+e_2\rangle\Downarrow\langle\sigma^{\prime},n\rangle.$ We need to show that $\langle\sigma,e_1+e_2\rangle\to^*\langle\sigma^{\prime},n\rangle.$
We assumed that $\langle\sigma,e_1+e_2\rangle\Downarrow\langle\sigma^{\prime},n\rangle.$ This means that there is some derivation whose conclusion is $\langle\sigma,e_1+e_2\rangle\Downarrow\langle\sigma^{\prime},n\rangle.$ By inspection, we see that only one rule has a conclusion of this form: the $App$ rule. Thus, the last rule used in the derivation was $App$ and it must be the case that $\langle\sigma,e_1\rangle\Downarrow\langle\sigma^{\prime\prime},n_1\rangle$ and $\langle\sigma'',e_2\rangle\Downarrow\langle\sigma^{\prime},n_2\rangle$ hold for some $n_1$ and $n_2$ with $n= n_1+ n_2$.

By the induction hypothesis $P( e_1)$, as $\left\langle\sigma,e_1\right\rangle\Downarrow\left\langle\sigma^{\prime\prime},n_1\right\rangle$, we must have $\left\langle\sigma,e_1\right\rangle\to^*\left\langle\sigma^{\prime\prime},n_1\right\rangle$. Likewise, by induction hypothesis $P( e_2)$, we have $\langle \sigma ^{\prime \prime }, e_2\rangle \to ^* \langle \sigma ^{\prime }, n_2\rangle .$ By Lemma 1 below, we have 
$$
\langle\sigma,e_1+e_2\rangle\to^*\langle\sigma^{\prime\prime},n_1+e_2\rangle,
$$
and by another application of Lemma 1 we have:
$$
\langle\sigma^{\prime\prime},n_1+e_2\rangle\to^*\langle\sigma^{\prime},n_1+n_2\rangle
$$
Then, using the small-step $Add$ rule and the multi-step $Trans$ rule, we have:
$$
\frac
{\large\frac
	{n=n_1+n_2}
  {\langle\sigma',n_1+n_2\rangle\to\langle\sigma',n\rangle}
  \mathrm{~Add}
\quad
  \frac{}{\langle\sigma',n\rangle\to^*\langle\sigma',n\rangle}\mathrm{~Refl}
}
{\langle\sigma',n_1+n_2\rangle\to^* \langle\sigma',n\rangle}
\mathrm{~TRANS}
$$
Finally, by two applications of Lemma 2, we obtain $\langle\sigma,e_1+e_2\rangle\to^* \langle\sigma',n\rangle$, which finishes the case.

$\Longleftarrow$: We proceed by induction on the derivation of $\langle\sigma,e\rangle\to^* \langle\sigma',n\rangle$ with a case analysis on the last rule used.

**CASE** $\mathbf{Refl}$: Then $e=n$ and $\sigma^{\prime}=\sigma$. We immediately have $\langle\sigma,n\rangle\Downarrow\langle\sigma,n\rangle$ by the large-step rule $Imt$. 

**CASE** $\mathbf{Trans}$: Then $\langle\sigma,e\rangle\to\langle\sigma^{\prime\prime},e^{\prime\prime}\rangle$ and $\langle\sigma^{\prime\prime},e^{\prime\prime}\rangle\to^{*}\langle\sigma^{\prime},n\rangle$. In this case, the induction hypothesis gives $\langle\sigma^{\prime\prime},e^{\prime\prime}\rangle\Downarrow\langle\sigma^{\prime},n\rangle$. The result follows from Lemma 3 below.



**Lemma 1.** If $\left \langle \sigma , e\right \rangle \to ^* \left \langle \sigma ^{\prime }, n\right \rangle$, then the following hold:

- $\langle\sigma,e+e_2\rangle\to^*\langle\sigma^{\prime},n+e_2\rangle$
- $\langle\sigma,e*e_2\rangle\to^*\langle\sigma^{\prime},n*e_2\rangle$
- $\langle\sigma,n_1+e\rangle\to^*\langle\sigma^{\prime},n_1+n\rangle$
- $\langle\sigma,n_1*e\rangle\to^*\langle\sigma^{\prime},n_1*n\rangle$
- $\langle\sigma,x:=e\:;\:e_2\rangle\to^*\langle\sigma^{\prime},x:=n\:;\:e_2\rangle$


Proof.  Omitted;  try it as an exercise. 

**Lemma2.**  If $\left \langle \sigma , e\right \rangle \to ^* \left \langle \sigma ^{\prime }, e^{\prime }\right \rangle$ and $\left\langle\sigma^{\prime},e^{\prime}\right\rangle\to^*\left\langle\sigma^{\prime\prime},e^{\prime\prime}\right\rangle$, then $\left\langle\sigma,e\right\rangle\to^*\left\langle\sigma^{\prime\prime},e^{\prime\prime}\right\rangle$.
Proof.  Omitted;  try it as an exercise

**Lemma 3.** If $\langle \sigma , e\rangle \to \langle \sigma ^{\prime \prime }, e''\rangle$ and $\langle\sigma'',e^{\prime\prime}\rangle\Downarrow\langle\sigma^{\prime},n\rangle$, then $\left\langle\sigma,e\right\rangle\Downarrow\langle\sigma^{\prime},n\rangle$.
Proof.  Omitted;  try it as an exercise.
