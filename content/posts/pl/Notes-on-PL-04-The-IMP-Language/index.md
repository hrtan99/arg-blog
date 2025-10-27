---
# 1) 新建一篇文章（会创建 posts/my-first-post/index.md）
# hugo new --kind post-bundle posts/my-first-post

# 2) 把配图放到同目录
# posts/my-first-post/
# ├─ index.md
# └─ cover.jpg  ← featuredImage 可填 "cover.jpg"

# 基本
title: "Notes on PL 04: the IMP Language"
subtitle: ""
date: 2024-09-16
lastmod:
draft: false

# URL / SEO
slug: "Notes-on-PL-04-The-IMP-Language"
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


### A Simple Imperative Language

We will now consider a more realistic programming language, one where we can assign values to variables and execute control constructs such as if and while. The syntax for this imperative language, called IMP, is as follows:
$$
\begin{aligned}
\begin{array}{rcl}
\texttt{Arithmetic Expressions} &a\in\mathbf{Aexp}& a::=x\mid n\mid a_{1}+a_{2}\mid a_{1}\times a_{2}\\
\texttt{Boolean Expressions} &b\in\mathbf{Bexp}& b::=\mathbf{~true}\mid \mathbf{false}\mid a_{1} < a_{2}\\
\texttt{Commands} &c\in\mathbf{Com}& c::=\mathbf{~skip}\mid x:=a\mid c_{1};c_{2}\mid\mathbf{if~}b\mathbf{~then~}c_{1}\mathbf{~else~}c_{2}\mid\mathbf{while~}b\mathbf{~do~}c
\end{array}
\end{aligned}
$$

#### Small-step Operational Semantics

We’ll first give a small-step operational semantics for IMP. The configurations in this language are of the form $\langle\sigma, c\rangle$, $\langle\sigma, b\rangle$, and $\langle\sigma, c\rangle$, where $\sigma$ is a store. The final configurations are of the form $\langle\sigma, \mathbf{skip}\rangle$ for commands, $\langle\sigma, \mathbf{true}\rangle$ and $\langle\sigma, \mathbf{false}\rangle$ for Boolean expressions, and ⟨𝜎, 𝑛⟩ for arithmetic expressions. There are three different small-step operational semantics relations: one each of the syntactic categories.
$$
\begin{aligned}&\to_{\mathbf{Com}}\subseteq(\mathbf{Store}\times\mathbf{Com})\times(\mathbf{Store}\times\mathbf{Com})\\&\to_{\mathbf{Bexp}}\subseteq(\mathbf{Store}\times\mathbf{Bexp})\times(\mathbf{Store}\times\mathbf{Bexp})\\&\to_{\mathbf{Aexp}}\subseteq(\mathbf{Store}\times\mathbf{Aexp})\times(\mathbf{Store}\times\mathbf{Aexp})\end{aligned}
$$
For brevity, we will overload the symbol $\rightarrow$ and use it to refer to all of these relations. Which relation is being used will be clear from context. The evaluation rules for arithmetic and Boolean expressions are similar to the ones we’ve seen before. However, note that since the arithmetic expressions no longer contain assignment, Arithmetic and Boolean Expressions can not update the store.

**Arithmetic Expressions**
$$
\begin{gathered}&\frac{n=\sigma(x)}{\langle\sigma,x\rangle\to\langle\sigma,n\rangle}\\[2ex]
&\frac{\langle\sigma,a_1\rangle\to\langle\sigma,a_1^{\prime}\rangle}{\langle\sigma,a_1+a_2\rangle\to\langle\sigma,a_1^{\prime}+a_2\rangle}\quad\frac{\langle\sigma,a_2\rangle\to\langle\sigma,a_2^{\prime}\rangle}{\langle\sigma,n+a_2\rangle\to\langle\sigma,n+a_2^{\prime}\rangle}\quad\frac{p=n+m}{\langle\sigma,n+m\rangle\to\langle\sigma,p\rangle}\\[2ex]
&\frac{\langle\sigma,a_1\rangle\to\langle\sigma,a_1^{\prime}\rangle}{\langle\sigma,a_1\times a_2\rangle\to\langle\sigma,a_1^{\prime}\times a_2\rangle}\quad\frac{\langle\sigma,a_2\rangle\to\langle\sigma,a_2^{\prime}\rangle}{\langle\sigma,n\times a_2\rangle\to\langle\sigma,n\times a_2^{\prime}\rangle}\quad\frac{p=n\times m}{\langle\sigma,n\times m\rangle\to\langle\sigma,p\rangle}\end{gathered}
$$
**Boolean Expressions**
$$
\frac{\langle\sigma,a_1\rangle\to\langle\sigma,a_1^{\prime}\rangle}{\langle\sigma,a_1 < a_2\rangle\to\langle\sigma,a_1^{\prime} < a_2\rangle}
\quad
\frac{\langle\sigma,a_2\rangle\to\langle\sigma,a_2^{\prime}\rangle}{\langle\sigma,n < a_2\rangle\to\langle\sigma,n < a_2^{\prime}\rangle}\\[2ex]
\frac{n < m}{\langle\sigma,n < m\rangle\to\langle\sigma,\mathbf{true}\rangle}
\quad
\frac{n\geq m}{\langle\sigma,n < m\rangle\to\langle\sigma,\mathbf{false}\rangle}
$$
**Commands**
$$
\frac{\langle\sigma,a\rangle\to\langle\sigma,a^{\prime}\rangle}{\langle\sigma,x:=a\rangle\to\langle\sigma,x:=a^{\prime}\rangle}
\quad
\frac{\langle\sigma,c_1\rangle\to\langle\sigma^{\prime},c_1^{\prime}\rangle}{\langle\sigma,c_1;c_2\rangle\to\langle\sigma^{\prime},c_1^{\prime};c_2\rangle}\\[2ex]
\frac{}{\langle\sigma,x:=n\rangle\to\langle\sigma[x\mapsto n],\mathbf{skip}\rangle}
\quad
\frac{}{\langle\sigma,\mathbf{skip};c_2\rangle\to\langle\sigma,c_2\rangle}
$$
For if commands, we reduce the test until we get true or false and then we execute the appropriate branch:
$$
\begin{gathered}
\frac
{\langle\sigma,b\rangle\to\langle\sigma,b^{\prime}\rangle}
{\langle\sigma,\mathbf{if~}b\mathbf{~then~}c_{1}\mathbf{~else~}c_{2}\rangle\to\langle\sigma,\mathbf{if~}b^{\prime}\mathbf{~then~}c_{1}\mathbf{~else~}c_{2}\rangle\\[2ex]
}\\[2ex]
\frac{}
{\langle\sigma,\mathbf{if}~\mathbf{false~ then~}c_{1}\mathbf{~else~}c_{2}\rangle\to\langle\sigma,c_{2}\rangle}
\quad
\frac{}{\langle\sigma,\mathbf{if~true~then~}c_{1}\mathbf{~else~}c_{2}\rangle\to\langle\sigma,c_{1}\rangle}
\end{gathered}
$$
For while loops, the above strategy doesn’t work (why?). Instead, we use the following rule, which can be thought of as “unrolling” the loop, one iteration at a time.
$$
\overline{\langle\sigma,\mathbf{while~}b\mathbf{~do~}c\rangle\to\langle\sigma,\mathbf{if~}b\mathbf{~then~}(c;\mathbf{while~}b\mathbf{~do~}c)\mathbf{~else~skip}\rangle}
$$
We can now take a concrete program and see how it executes under the above rules. Consider we execute the program
$$
\mathrm{foo}:=3;\mathbf{while}~\mathrm{foo}<4\mathbf{~do~}\mathrm{foo}:=\mathrm{foo}+5
$$
The execution works as follows:
$$
\begin{aligned}
&\langle\sigma,\mathrm{foo}:=3;\mathbf{while}\mathrm{~foo}<4\mathbf{~do}\mathrm{~foo}:=\mathrm{foo}+5\rangle\\
&\rightarrow\langle\sigma^{\prime},\mathbf{skip};\mathbf{while}\mathrm{~foo<4\mathbf{~do}\mathrm{~foo}}:=\mathrm{foo}+5\rangle&\mathrm{where~}\sigma^{\prime}=\sigma[\mathrm{foo}\mapsto3]\\
&\rightarrow\langle\sigma^{\prime},\mathbf{while}\mathrm{~foo<4\mathbf{~do}\mathrm{~foo}}:=\mathrm{foo}+5\rangle\\
&\to\langle\sigma^{\prime},\mathbf{if}\mathrm{~3<4~}\mathbf{then~}\mathrm{(foo:=foo+5;W)}\mathbf{~else~skip}\rangle\\
&\to\langle\sigma^{\prime},\mathbf{if~true~then}\mathrm{ (foo}:=\mathrm{foo}+5;W)\mathbf{~else~skip}\rangle\\
&\to\langle\sigma^{\prime},\mathrm{foo}:=\mathrm{foo}+5;\mathbf{while}\mathrm{~foo<4\mathbf{~do}\mathrm{~foo}}:=\mathrm{foo}+5\rangle\\
&\to\langle\sigma^{\prime},\mathrm{foo}:=3+5;\mathbf{while}\mathrm{~foo<4\mathbf{~do}\mathrm{~foo}}:=\mathrm{foo}+5\rangle\\
&\to\langle\sigma^{\prime},\mathrm{foo}:=8;\mathbf{while}\mathrm{~foo<4\mathbf{~do}\mathrm{~foo}}:=\mathrm{foo}+5\rangle\\
&\to\langleσ^{\prime\prime},\mathbf{skip};\mathbf{while}\mathrm{~foo<4\mathbf{~do}\mathrm{~foo}}:=\mathrm{foo}+5\rangle&\mathrm{where~}\sigma^{\prime\prime}=\sigma^{\prime}[\mathrm{foo}\mapsto8]\\
&\to\langle\sigma^{\prime\prime},\mathbf{while}\mathrm{~foo<4\mathbf{~do}\mathrm{~foo}}:=\mathrm{foo}+5\rangle\\
&\to\langle\sigma^{\prime\prime},\mathbf{if}\mathrm{~foo<4~}\mathbf{then~}\mathrm{(foo:=foo+5;W)}\mathbf{~else~skip}\rangle\\
&\to\langle\sigma^{\prime\prime},\mathbf{if}\mathrm{~8<4~}\mathbf{then~}\mathrm{(foo:=foo+5;W)}\mathbf{~else~skip}\rangle\\
&\to\langle\sigma^{\prime\prime},\mathbf{if~false~then~}\mathrm{ (foo}:=\mathrm{foo}+5;W)\mathbf{~else~skip}\rangle\\
&\to\langle\sigma^{\prime\prime},\mathbf{skip}\rangle
\end{aligned}
$$
where $W$ is an abbreviation for the while loop $\mathbf{while}\mathrm{~foo<4\mathbf{~do}\mathrm{~foo}}:=\mathrm{foo}+5\rangle$.

### Large-step Operational Semantics for IMP

We define large-step evaluation relations for arithmetic expressions, Boolean expressions, and commands. The relation for arithmetic expressions relates an arithmetic expression and store to the integer value that the expression evaluates to. For Boolean expressions, the final value is in $\mathbf{Bool} = \mathbf{true}, \mathbf{false}$. For commands, the final value is a store.
$$
\begin{aligned}&\Downarrow_{\mathbf{Aexp}}\subseteq(\mathbf{Aexp}\times\mathbf{Store})\times\mathbf{Int}\\&\Downarrow_{\mathbf{Bexp}}\subseteq(\mathbf{Bexp}\times\mathbf{Store})\times\mathbf{Bool}\\&\Downarrow_{\mathbf{Com}}\subseteq(\mathbf{Com}\times\mathbf{Store})\times\mathbf{Store}\end{aligned}
$$
Again, we overload the symbol $\Downarrow$ and use it for any of these three relations; which relation is intended will be clear from context. We also use nfix notation, for example writing $\langle\sigma,c\rangle\Downarrow\sigma^{\prime}$ if $(\langle\sigma,c\rangle,\sigma^{\prime})\in\Downarrow_{\mathbf{Com}}$.

**Arithmetic Expressions.**
$$
\begin{gathered}
\frac{}{\langle\sigma,n\rangle\Downarrow n}
\quad\quad\quad\quad\quad\quad\quad\quad\quad\quad\quad\quad\quad\quad
\frac{\sigma(x)=n}{\langle\sigma,x\rangle\Downarrow n}\\[2ex]
\frac
{\langle\sigma,a_1\rangle\Downarrow n_1
\quad
\langle\sigma,a_2\rangle\Downarrow n_2
\quad
n=n_1+n_2}
{\langle\sigma,a_1+a_2\rangle\Downarrow n}
\quad
\frac{
\langle\sigma,a_1\rangle\Downarrow n_1
\quad
\langle\sigma,a_2\rangle\Downarrow n_2
\quad
n=n_1\times n_2}
{\langle\sigma,a_1\times a_2\rangle\Downarrow n}\\[2ex]
\end{gathered}
$$
**Boolean Expressions.**
$$
\begin{gathered}
\frac{}{\langle\sigma,\mathbf{true}\rangle\Downarrow\mathbf{true}}
\quad\quad\quad\quad\quad\quad\quad\quad
\frac{}{\langle\sigma,\mathbf{false}\rangle\Downarrow\mathbf{false}}\\[2ex]
\frac{}{\langle\sigma,a_{1}\rangle\Downarrow n_{1}\quad\langle\sigma,a_{2}\rangle\Downarrow n_{2}\quad n_{1}< n_{2}}
\quad
\frac{\langle\sigma,a_{1} \rangle\Downarrow n_{1}\quad\langle\sigma,a_{2}\rangle\Downarrow n_{2}\quad n_{1}\geq n_{2}}{\langle\sigma,a_{1} < a_{2}\rangle\Downarrow\mathbf{false}}
\end{gathered}
$$

**Commands.**
$$
\small
\begin{gathered}
\mathrm{Skip}\frac{\langle\sigma,a\rangle\Downarrow n}{\langle\sigma,\mathbf{skip}\rangle\Downarrow\sigma}
\quad
\mathrm{Assgn}\frac{\langle\sigma,a\rangle\Downarrow n}{\langle\sigma,x:=a\rangle\Downarrow\sigma[x\mapsto n]}
\quad
\mathrm{Seq}\frac{\langle\sigma,c_1\rangle\Downarrow\sigma^{\prime}\quad\langle\sigma^{\prime},c_2\rangle\Downarrow\sigma^{\prime\prime}}{\langle\sigma,c_1;c_2\rangle\Downarrow\sigma^{\prime\prime}}\\[2ex]
\mathrm{IfT~}\frac{\langle\sigma,b\rangle\Downarrow\mathbf{true}\quad\langle\sigma,c_1\rangle\Downarrow\sigma^{\prime}}{\langle\sigma,\mathbf{if~}b\mathbf{~then~}c_1\mathbf{~else~}c_2\rangle\Downarrow\sigma^{\prime}}
\quad
\mathrm{IfF~}\frac{\langle\sigma,b\rangle\Downarrow\mathrm{false}\quad\langle\sigma,c_2\rangle\Downarrow\sigma^{\prime}}{\langle\sigma,\mathbf{if~}b\mathbf{~then~}c_1\mathbf{~else~}c_2\rangle\Downarrow\sigma^{\prime}}\\[2ex]
\mathrm{WhileF}\frac{\langle\sigma,b\rangle\Downarrow\mathbf{false}}{\langle\sigma,\mathbf{while~}b\mathbf{~do~}c\rangle\Downarrow\sigma}
\quad
\mathrm{~WhileT~}\frac{\langle\sigma,b\rangle\Downarrow\mathbf{true~}\quad\langle\sigma,c\rangle\Downarrow\sigma^{\prime}\quad\langle\sigma^{\prime},\mathbf{while~}b\mathbf{~do~}c\rangle\Downarrow\sigma^{\prime\prime}}{\langle\sigma,\mathbf{while~}b\mathbf{~do~}c\rangle\Downarrow\sigma^{\prime\prime}}\end{gathered}
$$

#### Command equivalence

The small-step operational semantics suggests that the loop $\mathbf{while}~b~ \mathbf{do}~c$ should be equivalent to the command $\mathbf{if}~b~\mathbf{then}~(c; ~\mathbf{while}~b~\mathbf{do}~c)~ \mathbf{else~skip}$. Can we show that this indeed the case that the language is defined using the above large-step evaluation? First, we need to to be more precise about what "equivalent commands" mean. Our formal model allows us to define this concept using large- step evaluations as follows. ( One can write a similar definition using $\to ^*$ in small-step semantics.)

Definition (Equivalence of commands). Two commands $c$ and $c'$ are equivalent (written $c\sim c'$) if, for any stores $\sigma$ and $\sigma'$, we have
$$
\langle\sigma,c\rangle\Downarrow\sigma^{\prime}\Longleftrightarrow\langle\sigma,c^{\prime}\rangle\Downarrow\sigma^{\prime}.
$$
We can now state and prove the claim that $\mathbf{while}~b~ \mathbf{do}~c$ and $\mathbf{if}$ $b$ $\mathbf{then}$ $(c; $ $\mathbf{while}$ $b$ $\mathbf{do}$ $c)$ $\mathbf{else~skip}$ are equivalent.

**Theorem**.
$$
\begin{aligned}
\forall b\in\mathbf{Bexp}. \forall c\in\mathbf{Com}.&\\
\mathbf{~while~}b\mathbf{~do~}c\sim~ &\mathbf{if}~b\mathbf{~then}\left(c;\mathbf{while~}b\mathbf{~do~}c\right)\mathbf{~else~skip}.
\end{aligned}
$$
Proof. Let $W$ be an abbreviation for $\mathbf{while}$ $b$ $\mathbf{do}$ $c$. We want to show that for all stores $\sigma$ and $\sigma'$, we have:
$$
\langle\sigma,W\rangle\Downarrow\sigma^{\prime}\text{ iff }\langle\sigma,\mathbf{if~}b\mathbf{~then~}(c;W)\mathbf{~else~skip}\rangle\Downarrow\sigma^{\prime}
$$
For this, we must show that both directions ($\Longrightarrow$ and $\Longleftarrow$) hold. We’ll show only direction $\Longrightarrow$, the other is similar.

Assume that $\sigma$ and $\sigma'$ are stores such that $\langle\sigma,W\rangle\Downarrow\sigma^{\prime}$. It means that there is some derivation that proves for this fact. Inspecting the evaluation rules, we see that there are two possible rules whose conclusions match this fact: $\mathrm{WhileF}$ and $\mathrm{WhileT}$. We analyze each of them in turn.

$\mathbf{WhileF}$. The derivation must look like the following.
$$
\begin{prooftree}
\mathrm{WhileF~}
\AxiomC{$\vdots^1$}
\UnaryInfC{$\langle\sigma,b\rangle\Downarrow\mathbf{false}$}
\UnaryInfC{$\langle\sigma,W\rangle\Downarrow\sigma$}
\end{prooftree}
$$
Here, we use $\vdots^1$ to refer to the derivation of $\langle \sigma , b\rangle \Downarrow \textbf{false}$. Note that in this case, $\sigma ^{\prime }= \sigma .$ We can use $\vdots^1$ to derive a proof tree showing that the evaluation of $\mathbf{if}$ $b$ $\mathbf{then}$ $(c;W)$ $\mathbf{else~skip}$ yields the same final state $\sigma$:
$$
\begin{prooftree}
\AxiomC{$\vdots^1$}
\UnaryInfC{$\langle\sigma,b\rangle\Downarrow\mathbf{false}$}
\AxiomC{}
\LeftLabel{Skip}
\UnaryInfC{$\langle\sigma,\mathbf{skip}\rangle\Downarrow\sigma$}
\LeftLabel{WhileF~}
\BinaryInfC{$\langle\sigma,\mathbf{if~}b\mathbf{~then~}(c;W)\mathbf{~else~skip}\rangle\Downarrow\sigma$}
\end{prooftree}
$$
$\mathbf{WhileT}$. In this case, the derivation has the following form.
$$
\begin{prooftree}
\AxiomC{$\vdots^2$}
\UnaryInfC{$\langle\sigma,b\rangle\Downarrow\mathbf{True}$}
\AxiomC{$\vdots^3$}
\UnaryInfC{$\langle\sigma,c\rangle\Downarrow\sigma''$}
\AxiomC{$\vdots^4$}
\UnaryInfC{$\langle\sigma,W\rangle\Downarrow\sigma$}
\LeftLabel{WhileF~}
\TrinaryInfC{$\langle\sigma,W\rangle\Downarrow\sigma$}
\end{prooftree}
$$
We can use $\vdots^2$, $\vdots^3$, and $\vdots^4$ to show that the evaluation of $\mathbf{if}$ $b$ $\mathbf{then}$ $(c;W)$ $\mathbf{else~skip}$ yields the same final state $\sigma$
$$
\begin{prooftree}
\AxiomC{$\vdots^2$}
\UnaryInfC{$\langle\sigma,b\rangle\Downarrow\mathbf{True}$}
\AxiomC{$\vdots^3$}
\UnaryInfC{$\langle\sigma,c\rangle\Downarrow\sigma''$}
\AxiomC{$\vdots^4$}
\UnaryInfC{$\langle\sigma'',W\rangle\Downarrow\sigma'$}
\LeftLabel{Seq~}
\BinaryInfC{$\langle\sigma,c;W\rangle\Downarrow\sigma'$}
\LeftLabel{WhileF~}
\BinaryInfC{$\langle\sigma,W\rangle\Downarrow\sigma$}
\end{prooftree}
$$
Hence, we showed that in each of the two possible cases, the command $\mathbf{if}$ $b$ $\mathbf{then}$ $(c;W)$ $\mathbf{else~skip}$ evaluates to the same final state as the command $W$.



