---
# 1) 新建一篇文章（会创建 posts/my-first-post/index.md）
# hugo new --kind post-bundle posts/my-first-post

# 2) 把配图放到同目录
# posts/my-first-post/
# ├─ index.md
# └─ cover.jpg  ← featuredImage 可填 "cover.jpg"

# 基本
title: "Notes on PL 05: IMP Properties"
subtitle: ""
date: 2024-09-25
lastmod: 
draft: false

# URL / SEO
slug: "Notes-on-PL-05-IMP-Properties"
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


### Equivalence of Semantics

The small-step and large-step semantics are equivalent as captured by the following theorem.

**Theorem.** For all commands $c$ and stores $\sigma$ and $\sigma'$ we have
$$
\langle\sigma,c\rangle\to^{*}\langle\sigma^{\prime},\mathbf{skip}\rangle \text{~iff~} \langle\sigma,c\rangle\Downarrow\sigma^{\prime}.
$$
The proof is left as an exercise.



### Non-Termination

For a given command $c$ and initial state $\sigma$, the execution of the command may terminate with some final store $\sigma^{\prime}$, or it may diverge and never yield a final state. For example, the command
$$
\textbf{while true do } \text{foo}:=\mathrm{foo}+1
$$
always diverges while
$$
\mathbf{while~}0<\text{i} \mathbf{~do~} \text{i}:=\mathrm{i}+1
$$
diverges if and only if the value of variable $i$ in the initial state is positive.

If $\langle\sigma,c\rangle$ is a diverging configuration then there is no state $\sigma$ such that
$$
\langle\sigma,c\rangle\Downarrow\sigma^{\prime}\quad\mathrm{~or~}\quad\langle\sigma,c\rangle\to^{*}\langle\sigma^{\prime},\mathrm{skip}\rangle.
$$
However, in small-step semantics, diverging computations generate an infinite sequence:
$$
\langle\sigma,c\rangle\to\langle\sigma_1,c_1\rangle\to\langle\sigma_2,c_2\rangle\to\ldots
$$
Hence, small-step semantics allow us to state and prove properties about programs that may diverge. Later in the notes, we will specify and prove properties that are of interest in potentially diverging computations.

### Determinism

The semantics of IMP (both small-step and large-step) are deterministic. For example, each IMP command $c$ and each initial store $\sigma$ evaluates to at most one final store.

**Theorem.** For all commands $c$ and stores $\sigma,\sigma_{1}$, and $\sigma_{2}$, if $\langle\sigma,c\rangle\Downarrow\sigma_{1}$ and $\langle\sigma,c\rangle\Downarrow\sigma_{2}$ then $\sigma_{1}=\sigma_{2}$.

To prove this theorem, we need an induction. But structural induction on the command $c$ will not work. (Why? Which case breaks?) Instead, we need to perform induction on the derivation of $\langle\sigma,c\rangle\Downarrow\sigma_1$. We first introduce some useful notation.

Let $\mathcal{D}$ be a derivation. We write $\mathcal{D}\Vdash y$ if $\mathcal{D}$ is a derivation of $y$, that is, if the conclusion of $\mathcal{D}$ is $y.$ For example, if $\mathcal{D}$ is the following derivation
$$
\begin{prooftree}
\AxiomC{$\langle\sigma,6\rangle\Downarrow6$}
\AxiomC{$\langle\sigma,7\rangle\Downarrow7$}
\BinaryInfC{$\langle\sigma,6\times7\rangle\Downarrow42$}
\UnaryInfC{$\langle\sigma,\mathrm{i}:=6\times7\rangle\Downarrow\sigma[\mathrm{i}\mapsto42]$}
\end{prooftree}
$$
Then we have $\mathcal{D}\Vdash\langle\sigma,\mathrm{i}:=42\rangle\Downarrow\sigma[\mathrm{i}\mapsto42]$.

Let $\mathcal D$ and $\mathcal D'$ be derivations. We say that $\mathcal D'$ is an immediate subderivation of $\mathcal D$ if $\mathcal D'$ is a derivation of one of the premises used in the final rule in the derivation $\mathcal D$. For example, the derivation
$$
\begin{prooftree}
\AxiomC{$\langle\sigma,6\rangle\Downarrow6$}
\AxiomC{$\langle\sigma,7\rangle\Downarrow7$}
\BinaryInfC{$\langle\sigma,6\times7\rangle\Downarrow42$}
\end{prooftree}
$$
is an immediate subderivation of
$$
\begin{prooftree}
\AxiomC{$\langle\sigma,6\rangle\Downarrow6$}
\AxiomC{$\langle\sigma,7\rangle\Downarrow7$}
\BinaryInfC{$\langle\sigma,6\times7\rangle\Downarrow42$}
\UnaryInfC{$\langle\sigma,\mathrm{i}:=6\times7\rangle\Downarrow\sigma[\mathrm{i}\mapsto42]$}
\end{prooftree}
$$
In a proof by induction on derivations, we assume that the property $P$ being proved holds for all immediate subderivations, and we show that it holds of the conclusion.

Proof. As $\langle\sigma,c\rangle\Downarrow\sigma_1$, there is a derivation $\mathcal{D}_1$ such that $\mathcal{D}_1\Vdash\langle\sigma,c\rangle\Downarrow\sigma_1$. Similarly, as $\langle\sigma,c\rangle\Downarrow\sigma_2$, there is a derivation $\mathcal{D}_2$ such that $\mathcal{D}_2\Vdash\langle\sigma,c\rangle\Downarrow\sigma_2$.

We proceed by induction on the derivation $\mathcal{D}_1\Vdash\langle\sigma,c\rangle\Downarrow\sigma_1$. We assume that the induction hypothesis holds for immediate subderivations of $\mathcal{D}_1$. In this case, the induction hypothesis $P$ is:
$$
P(\mathcal{D})=\forall c\in\mathbf{Com}.\forall\sigma,\sigma^{\prime},\sigma^{\prime\prime}\in\mathbf{Store},\mathrm{~if~}\mathcal{D}\Vdash\langle\sigma,c\rangle\Downarrow\sigma^{\prime}\mathrm{~and~}\langle\sigma,c\rangle\Downarrow\sigma^{\prime\prime}\mathrm{~then~}\sigma^{\prime}=\sigma^{\prime\prime}.
$$
We analyze the possible cases for the last rule used in $\mathcal{D}_1$.

**Case SKIP**: In this case
$$
\begin{prooftree}
\AxiomC{$\vdots$}
\LeftLabel{$\mathcal D_1=\mathrm{Skip}$}
\UnaryInfC{$\langle\sigma,\mathbf{skip}\rangle\Downarrow\sigma$}
\end{prooftree}
$$
and we have $c = \mathbf{skip}$ and $\sigma_1=\sigma$. Since the rule $\mathrm{Skip}$ is the only rule that has the command $\mathbf{skip}$ in its conclusion, the last rule used in 𝒟2 must also be $\mathrm{Skip}$, and so we have $\sigma_2=\sigma$ and the result holds.

**Case ASSGN**: In this case
$$
\begin{prooftree}
\AxiomC{$\vdots$}
\UnaryInfC{$\langle\sigma,a\rangle\Downarrow n$}
\LeftLabel{$\mathcal D_1=\mathrm{Assgn}$}
\UnaryInfC{$\langle\sigma,x:=a\rangle\Downarrow\sigma[x\mapsto n]$}
\end{prooftree}
$$
and we have $c=x:=a$ and $\sigma_1=\sigma[x\mapsto n]$. The last rule used in $\mathcal{D}_2$ must also be $\mathrm{Assgn}$, and so we have $\sigma_2=\sigma[x\mapsto n]$ and the result holds. Strictly speaking, we also need to argue that the evaluation of $a$ is deterministic. In this proof we will tacitly assume deterministic evaluation of arithmetic and boolean expressions.

**Case SEQ**: In this case
$$
\begin{prooftree}
\AxiomC{$\vdots$}
\UnaryInfC{$\langle\sigma,c_1\rangle\Downarrow \sigma_1'$}
\AxiomC{$\vdots$}
\UnaryInfC{$\langle\sigma_1',c_2\rangle\Downarrow \sigma_1$}
\LeftLabel{$\mathcal D_1=\mathrm{Seq}$}
\BinaryInfC{$\langle\sigma,c_1;c_2\rangle\Downarrow\sigma_1$}
\end{prooftree}
$$
and we have $c = c_1; c_2$. The last rule used in $\mathcal D_2$ must also be $\mathrm{Seq}$, and so we have
$$
\begin{prooftree}
\AxiomC{$\vdots$}
\UnaryInfC{$\langle\sigma,c_1\rangle\Downarrow \sigma_2'$}
\AxiomC{$\vdots$}
\UnaryInfC{$\langle\sigma_1',c_2\rangle\Downarrow \sigma_2$}
\LeftLabel{$\mathcal D_2=\mathrm{Seq}$}
\BinaryInfC{$\langle\sigma,c_1;c_2\rangle\Downarrow\sigma_2$}
\end{prooftree}
$$
By the inductive hypothesis applied to the derivation  $\Large\frac{\vdots}{\langle\sigma,c_1\rangle~\Downarrow~\sigma_1^{\prime}}$, we have $\sigma_1'=\sigma_2'$. By another application of the inductive hypothesis to $\Large\frac{\vdots}{\langle\sigma,c_1\rangle~\Downarrow~\sigma_1}$, we have $\sigma_1=\sigma_2$ and the result holds.

**Case IF-T**: Here we have
$$
\begin{prooftree}
\AxiomC{$\vdots$}
\UnaryInfC{$\langle\sigma,b\rangle\Downarrow\mathbf{true}$}
\AxiomC{$\vdots$}
\UnaryInfC{$\langle\sigma,c_1\rangle\Downarrow \sigma_1$}
\LeftLabel{$\mathcal D_1=\mathrm{IfT}$}
\BinaryInfC{$\langle\sigma,\mathbf{if~}b\mathbf{~then~}c_{1}\mathbf{~else~}c_{2}\rangle\Downarrow\sigma_{1}$}
\end{prooftree}
$$
and we have $c=\mathbf{if~}b\mathbf{~then~}c_{1}\mathbf{~else~}c_{2}$. The last rule used in $\mathcal D_2$ must also be IF-T and so we have
$$
\begin{prooftree}
\AxiomC{$\vdots$}
\UnaryInfC{$\langle\sigma,b\rangle\Downarrow\mathbf{true}$}
\AxiomC{$\vdots$}
\UnaryInfC{$\langle\sigma,c_1\rangle\Downarrow \sigma_2$}
\LeftLabel{$\mathcal D_2=\mathrm{IfT}$}
\BinaryInfC{$\langle\sigma,\mathbf{if~}b\mathbf{~then~}c_{1}\mathbf{~else~}c_{2}\rangle\Downarrow\sigma_{2}$}
\end{prooftree}
$$
The result holds by the inductive hypothesis applied to the derivation $\Large\frac{\vdots}{\langle\sigma,c_1\rangle~\Downarrow~\sigma_1}$.

**Case IF-F**: Similar to the case for IF-T.

**Case WHILE-F**: Straightforward, similar to the case for SKIP.

**Case WHILE-T**: Here we have
$$
\begin{prooftree}
\AxiomC{$\vdots$}
\UnaryInfC{$\langle\sigma,b\rangle\Downarrow\mathbf{true}$}
\AxiomC{$\vdots$}
\UnaryInfC{$\langle\sigma,c_1\rangle\Downarrow \sigma_1'$}
\AxiomC{$\vdots$}
\UnaryInfC{$\langle\sigma_1',c\rangle\Downarrow \sigma_1$}
\LeftLabel{$\mathcal D_1=\mathrm{IfT}$}
\TrinaryInfC{$\langle\sigma,\mathbf{while~}b\mathbf{~do~}c_1\rangle\Downarrow\sigma_{1}$}
\end{prooftree}
$$
and we have $c=\mathbf{while~}b\mathbf{~do~}c_1$. The last rule used in $\mathcal D_2$ must also be WHILE-T, and so we have
$$
\begin{prooftree}
\AxiomC{$\vdots$}
\UnaryInfC{$\langle\sigma,b\rangle\Downarrow\mathbf{true}$}
\AxiomC{$\vdots$}
\UnaryInfC{$\langle\sigma,c_1\rangle\Downarrow \sigma_2'$}
\AxiomC{$\vdots$}
\UnaryInfC{$\langle\sigma_2',c\rangle\Downarrow \sigma_2$}
\LeftLabel{$\mathcal D_2=\mathrm{IfT}$}
\TrinaryInfC{$\langle\sigma,\mathbf{while~}b\mathbf{~do~}c_1\rangle\Downarrow\sigma_{2}$}
\end{prooftree}
$$
By the inductive hypothesis applied to the derivation  $\Large\frac{\vdots}{\langle\sigma,c_1\rangle~\Downarrow~\sigma_1^{\prime}}$, we have $\sigma_1'=\sigma_2'$. By another application of the inductive hypothesis to the derivation $\Large\frac{\vdots}{\langle\sigma_1',c_1\rangle~\Downarrow~\sigma_1}$, we have $\sigma_1=\sigma_2$ and the result holds.

Note that even though $c=\mathbf{while~}b\mathbf{~do~}c_1$ appears in the derivation of $\langle\sigma,$ $\mathbf{while}$ $b$ $\mathbf{do}$ $c_1\rangle$ $\Downarrow$ $\sigma_{1}$, we do not run in to problems, as the induction is over the derivation, not over the structure of the command.