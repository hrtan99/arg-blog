---
# 1) 新建一篇文章（会创建 posts/my-first-post/index.md）
# hugo new --kind post-bundle posts/my-first-post

# 2) 把配图放到同目录
# posts/my-first-post/
# ├─ index.md
# └─ cover.jpg  ← featuredImage 可填 "cover.jpg"

# 基本
title: "Notes on PL 02: Inductive Definitions and Proofs"
subtitle: ""
date: 2024-09-02
lastmod: 
draft: false

# URL / SEO
slug: "Notes-on-PL-02-Inductive-definitions-and-proofs"
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


In this note, we will use the semantics of our simple language of arithmetic expressions,
$$
e ::= x \mid n \mid e_1 + e_2 \mid e_1 * e_2 \mid x := e_1 \,;\, e_2
$$
to express useful program properties, and we will prove these properties by induction.

### Program Properties

There are a number of interesting questions about a language one can ask: Is it deterministic? Are there non-terminating programs? What sorts of errors can arise during evaluation? Having a formal semantics allows us to express these properties precisely.

- **Determinism**: Evaluation is deterministic,

$$
\begin{array}{c}\forall e\in\mathbf{Exp}.\mathrm{~}\forall\sigma,\sigma^{\prime},\sigma^{\prime\prime}\in\mathbf{Store}.\mathrm{~}\forall e^{\prime},e^{\prime\prime}\in\mathbf{Exp}.\\
\mathrm{if~}\langle\sigma,e\rangle\to\langle\sigma^{\prime},e^{\prime}\rangle\mathrm{~and~}\langle\sigma,e\rangle\to\langle\sigma^{\prime\prime},e^{\prime\prime}\rangle\mathrm{~then~}e^{\prime}=e^{\prime\prime}\mathrm{~and~}\sigma^{\prime}=\sigma^{\prime\prime}.\end{array}
$$

- **Termination**: Evaluation of every expression terminates,

$$
\begin{gathered}\forall e\in\mathbf{Exp}.\forall\sigma\in\mathbf{Store}.\exists\sigma^{\prime}\in\mathbf{Store}.\exists e^{\prime}\in\mathbf{Exp}.\langle\sigma,e\rangle\to^*\langle\sigma^{\prime},e^{\prime}\rangle\mathrm{~and~}\langle\sigma^{\prime},e^{\prime}\rangle\not\to,\\\mathrm{where~}\langle\sigma^{\prime},e^{\prime}\rangle\not\to\text{is shorthand for }\neg(\exists\sigma^{\prime\prime}\in\mathbf{Store}.\exists e^{\prime\prime}\in\mathbf{Exp}.\langle\sigma^{\prime},e^{\prime}\rangle\to\langle\sigma^{\prime\prime},e^{\prime\prime}\rangle).\end{gathered}
$$

It is tempting to want the following soundness property,

- **Soundness**: Evaluation of every expression yields an integer,

$$
\forall e\in\mathbf{Exp}.\forall\sigma\in\mathbf{Store}.\exists\sigma^{\prime}\in\mathbf{store}.\exists n^{\prime}\in\mathbf{Int}.\langle\sigma,e\rangle\to^{*}\langle\sigma^{\prime},n^{\prime}\rangle,
$$

but unfortunately it does not hold in our language! For example, consider the totally-undefined function $\sigma$ and the expression $i+j$. The configuration $\langle\sigma, i+j\rangle$ is *stuck*—it has no possible transitions--but $i+j$ is not an integer. The problem is that $i+j$ has free variables but $\sigma$ does not contain mappings for those variables.

To fix this problem, we can restrict our attention to well-formed configurations $\langle\sigma, i+j\rangle$, where $\sigma$ is defined on (at least) the free variables in $e$. This makes sense as evaluation typically starts with a *closed* expression. We can define the set of free variables of an expression as follows:
$$
\begin{aligned}
fvs(x)&\triangleq\{x\}\\[2ex]
fvs(n)&\triangleq\{\}\\[2ex]
fvs(e_1+e_2)&\triangleq fvs(e_1)\cup fvs(e_2)\\[2ex]
fvs(e_1*e_2)&\triangleq fvs(e_1)\cup fvs(e_2)\\[2ex]
fvs(x:=e_1;e_2)&\triangleq fvs(e_1)\cup(fvs(e_2)\setminus\{x\})[2ex]
\end{aligned}
$$
Now we can formulate two properties that imply a variant of the soundness property above:

- **Progress**: For each expression $e$ and store $\sigma$ such that the free variables of $e$ are contained in the domain of $\sigma$, either $e$ is an integer or there exists a possible transition for $\langle\sigma, e\rangle$,

$$
\begin{aligned}\forall e\in\mathbf{Exp}.&\,\forall\sigma\in\mathbf{Store}.\\
fvs(e)\subseteq dom(\sigma)&\Longrightarrow e\in\mathbf{Int}\mathrm{~or~}(\exists e^{\prime}\in\mathbf{Exp}.\exists\sigma^{\prime}\in\mathbf{Store}.\left\langle\sigma,e\right\rangle\to\left\langle\sigma^{\prime},e^{\prime}\right\rangle)
\end{aligned}
$$

- **Preservation**: Evaluation preserves containment of free variables in the domain of the store,

$$
\begin{aligned}\forall e,e^{\prime}\in\mathbf{Exp}.\,&\forall\sigma,\sigma^{\prime}\in\mathbf{Store}.\\fvs(e)&\subseteq dom(\sigma)\mathrm{~and~}\langle\sigma,e\rangle\to\langle\sigma^{\prime},e^{\prime}\rangle\Longrightarrow fvs(e^{\prime})\subseteq dom(\sigma^{\prime}).
\end{aligned}
$$

The rest of this lecture shows how can we prove such properties using induction.

### Inductive Sets

Induction is an important concept in programming language theory. An inductively-defined set 𝐴 is one that is described using a finite collection of axioms and inductive (inference) rules. Axioms of the form
$$
\frac{}{a\in A}
$$
indicate that $a$ is in the set $A$. Inductive rules
$$
\frac{a_1\in A,\,\cdots,\,a_n\in A}{a\in A}
$$
indicate that if $𝑎_1 , \cdots , 𝑎_𝑛$ are all elements of $A$, then $a$ is also an element of $A$.

The set $A$ is the set of all elements that can be inferred to belong to $A$ using a (finite) number of applications of these rules, starting only from axioms. In other words, for each element $a$ of $A$, we must be able to construct a finite proof tree whose final conclusion is $a \in A$.

- Example 1. The set described by a grammar is an inductive set. For instance, the set of arithmetic expressions can be described with two axioms and three inference rules:

$$
\begin{equation}
\begin{gathered}
\frac{}{x\in\mathbf{Exp}}\quad \quad\frac{}{ n\in\mathbf{Exp}} \\[2ex]
\frac{e_1\in\mathbf{Exp}\quad e_2\in\mathbf{Exp}}{e_1+e_2\in\mathbf{Exp}}\quad
\frac{e_1\in\mathbf{Exp}\quad e_2\in\mathbf{Exp}}{e_1*e_2\in\mathbf{Exp}}\quad
\frac{e_1\in\mathbf{Exp}\quad e_2\in\mathbf{Exp}}{x:=e_1;e_2\in\mathbf{Exp}}\quad
\end{gathered}
\end{equation}
$$

These axioms and rules describe the same set of expressions as the grammar:
$$
e ::= x \mid n \mid e_1 + e_2 \mid e_1 * e_2 \mid x := e_1 \,;\, e_2
$$

- Example 2. The natural numbers (expressed here in unary notation) can be inductively defined:

$$
\frac{}{0\in\mathbb{N}}\quad\frac{n\in\mathbb{N}}{succ(n)\in\mathbb{N}}
$$

- Example 3. The small-step evaluation relation $\rightarrow$ is an inductively defined set.

- Example 4. The multi-step evaluation relation can be inductively defined:

$$
\frac{}{\langle\sigma,e\rangle\to^*\langle\sigma,e\rangle}\mathrm{~Refl~}\quad\frac{\langle\sigma,e\rangle\to\langle\sigma^{\prime},e^{\prime}\rangle\quad\langle\sigma^{\prime},e^{\prime}\rangle\to^*\langle\sigma^{\prime\prime},e^{\prime\prime}\rangle}{\langle\sigma,e\rangle\to^*\langle\sigma^{\prime\prime},e^{\prime\prime}\rangle}\mathrm{~Trans}
$$

- Example 5. The set of free variables of an expression 𝑒 can be inductively defined:

$$
\begin{equation}
\begin{gathered}
\frac{}{y \in f v s(y)} \quad \frac{y \in f v s\left(e_1\right)}{y \in f v s(e_1+e_2)} \quad \frac{y \in f v s\left(e_2\right)}{y \in f v s\left(e_1+e_2\right)} \quad \frac{y \in f v s\left(e_1\right)}{y \in f v s\left(e_1 * e_2\right)} \quad \frac{y \in f v s\left(e_2\right)}{y \in f v s\left(e_1 * e_2\right)} \\[2ex]
\frac{y \in f v s\left(e_1\right)}{y \in f v s\left(x:=e_1 \,;\, e_2\right)} \quad \frac{y \neq x \quad y \in f v s\left(e_2\right)}{y \in f v s\left(x:=e_1 ; e_2\right)}
\end{gathered}
\end{equation}
$$

### Inductive Proofs

We can prove facts about elements of an inductive set using an inductive reasoning that follows the structure of the set definition.

#### Mathematical Induction

You have probably seen proofs by induction over the natural numbers, called mathematical induction. In such proofs, we typically want to prove that some property $P$ holds for all natural numbers, that is, $\forall n\in\mathbb{N}.\,P(n).$ A proof by induction works by first proving that $P(0)$ holds, and then proving for all $m\in\mathbb{N}$,  if $P(m)$, then $P(m+1)$. The principle of mathematical induction can be stated succinctly as
$$
P(0)\mathrm{~and~}(\forall m\in\mathbb{N}.P(m)\Longrightarrow P(m+1))\Longrightarrow\forall n\in\mathbb{N}.P(n).
$$
The proposition $P(0)$ is the *basis* of the induction (also called the *base case*) while $P(m)\Longrightarrow P(m+1)$ is called *induction step* (or the *inductive case*). While proving the induction step, the assumption that $P(m)$ holds is called the induction hypothesis.

#### Structural Induction

Given an inductively defined set 𝐴, to prove that a property 𝑃 holds for all elements of 𝐴, we need to show:

1. **Base cases**: For each axiom

$$
\frac{}{a\in A}
$$

$P(a)$ holds.

2. **Inductive cases**: For each inference rule

$$
\frac{a_1\in A,\,\cdots,\,a_n\in A}{a\in A}
$$

if $P(a_1)$ and $\cdots$ and $P(a_n)$ then $P(a)$.

Note that if the set $A$ is the set of natural numbers from Example 2 above, then the requirements for proving that $P$ holds for all elements of $A$ is equivalent to mathematical induction. If $A$ describes a syntactic set, then we refer to induction following the requirements above as structural induction. If $A$ is an operational semantics relation (such as the small-step operational semantics relation $\rightarrow$) then such an induction is called *induction on derivations*. We will see examples of structural induction and induction on derivations throughout the notes

#### Example: Progress

Let’s consider the progress property defined above, and repeated here:

**Progress**: For each expression $e$ and store $\sigma$ such that the free variables of $e$ are contained in the domain of $\sigma$, either $e$ is an integer or there exists a possible transition for $\langle\sigma, e\rangle$,
$$
\begin{aligned}\forall e\in\mathbf{Exp}.&\,\forall\sigma\in\mathbf{Store}.\\
fvs(e)\subseteq dom(\sigma)&\Longrightarrow e\in\mathbf{Int}\mathrm{~or~}(\exists e^{\prime}\in\mathbf{Exp}.\exists\sigma^{\prime}\in\mathbf{Store}.\left\langle\sigma,e\right\rangle\to\left\langle\sigma^{\prime},e^{\prime}\right\rangle)
\end{aligned}
$$
Let’s rephrase this property in terms of an explicit predicate on expressions:
$$
P(e)\triangleq\forall\sigma\in\mathbf{Store}.\mathrm{~}fvs(e)\subseteq dom(\sigma)\Longrightarrow e\in\mathbf{Int}\mathrm{~or~}(\exists e^{\prime},\sigma^{\prime}.\langle\sigma,e\rangle\to\langle\sigma^{\prime},e^{\prime}\rangle)
$$
This technique is called “structural induction on $e$.” We analyze each case in the grammar and show that $P(e)$ holds for that case. Since the grammar productions $e_1 + e_2$ and $e_1*e_2$ and $x:=e_1\,;\,e_2$ are inductive, they are inductive steps in the proof; the cases for $x$ and $n$ are base cases. The proof proceeds as follows.

*Proof*. Let $e$ be an expression. We will prove that
$$
\forall \sigma \in \text { Store. } f v s(e) \subseteq \operatorname{dom}(\sigma) \Longrightarrow e \in \operatorname{Int} \text { or }\left(\exists e^{\prime}, \sigma^{\prime} .\langle\sigma, e\rangle \rightarrow\left\langle\sigma^{\prime}, e^{\prime}\right\rangle\right)
$$

by structural induction on $e$. We analyze several cases, one for each case in the grammar for expressions:

**CASE** $e=x$: Let $\sigma$ be an arbitrary store, and assume that ${fvs}(e) \subseteq \operatorname{dom}(\sigma)$. By the definition of fvs we have ${fvs}(x)=\{x\}$. By assumption we have $\{x\} \subseteq \operatorname{dom}(\sigma)$ and so $x \in \operatorname{dom}(\sigma)$. Let $n=\sigma(x)$. By the $Var$ axiom we have $\langle\sigma, x\rangle \rightarrow\langle\sigma, n\rangle$, which finishes the case.

**CASE** $e=n$: We immediately have $e \in$ Int, which finishes the case.

**CASE** $e=e_1+e_2$ : Let $\sigma$ be an arbitrary store, and assume that $f v s(e) \subseteq \operatorname{dom}(\sigma)$. We will assume that $P\left(e_1\right)$ and $P\left(e_2\right)$ hold and show that $P(e)$ holds. Let's expand these properties. We have
$$
\begin{aligned}
& P\left(e_1\right)=\forall \sigma \in \mathbf{Store.} f v s\left(e_1\right) \subseteq \operatorname{dom}(\sigma) \Longrightarrow e_1 \in \mathbf{Int} \text { or }\left(\exists e^{\prime}, \sigma^{\prime} .\left\langle\sigma, e_1\right\rangle \rightarrow\left\langle\sigma^{\prime}, e^{\prime}\right\rangle\right) \\
& P\left(e_2\right)=\forall \sigma \in \mathbf { Store. } f v s\left(e_2\right) \subseteq \operatorname{dom}(\sigma) \Longrightarrow e_2 \in \mathbf{Int} \text { or }\left(\exists e^{\prime}, \sigma^{\prime} .\left\langle\sigma, e_2\right\rangle \rightarrow\left\langle\sigma^{\prime}, e^{\prime}\right\rangle\right)
\end{aligned}
$$

and want to prove:

$$
P\left(e_1+e_2\right)=\forall \sigma \in \mathbf { Store.  } \\fvs\left(e_1+e_2\right) \subseteq \operatorname{dom}(\sigma) \Longrightarrow e_1+e_2 \in \mathbf{Int} \text { or }\left(\exists e^{\prime}, \sigma^{\prime} .\left\langle\sigma, e_1+e_2\right\rangle \rightarrow\left\langle\sigma^{\prime}, e^{\prime}\right\rangle\right)
$$

We analyze several subcases.

- **Subcase** $e_1= n_1$ and $e_2=n_2$: By rule $Add$, we immediately have$\langle\sigma,n_1+n_2\rangle\to\langle\sigma,p\rangle$, where

$p=n_1+n_2.$

- **Subcase** $ e_1\notin \textbf{Int}$: By assumption and the definition of $fvs$ we have 

$$
fvs(e_1)\subseteq fvs(e_1+e_2)\subseteq dom(\sigma)
$$

​	Hence, by the induction hypothesis $P(e_1)$ we also have $\langle\sigma,e_1\rangle\to\langle\sigma^{\prime},e^{\prime}\rangle$ for some $e^\prime$ and $\sigma^{\prime}.$ By rule $LAdd$ we have $\langle\sigma,e_1+e_2\rangle\to\langle\sigma^{\prime},e^{\prime}+e_2\rangle.$

- **Subcase** $e_1=n_1$ and $e_2\notin \textbf{Int}$: By assumption and the definition of $fvs$ we have 

$$
fvs(e_2)\subseteq fvs(e_1+e_2)\subseteq dom(\sigma)
$$

​	Hence, by the induction hypothesis $P( e_2)$ we also have $\langle\sigma,e_2\rangle\to\langle\sigma^{\prime},e^{\prime}\rangle$ for some $e^\prime$ and

$\sigma^{\prime}$.  By rule $RAdd$ we have ${\langle\sigma,e_1+e_2\rangle}\rightarrow\langle\sigma^{\prime},e_1+e^{\prime}\rangle$, which finishes the case.

**CASE** $e= e_1* e_2$:  Analogous to the previous case.

**CASE** $e=x:=e_1;e_2$ : Let $\sigma$ be an arbitrary store, and assume that $fvs(e)\subseteq dom(\sigma).$ As above, we assume that $P(e_1)$ and $P( e_2)$ hold and show that $P( e)$ holds. Let's expand these properties

We have

$$
P(e_1)=\forall\sigma.\:fvs(e_1)\subseteq dom(\sigma)\Longrightarrow e_1\in\mathbf{Int}\mathrm{~or~}(\exists e^{\prime},\sigma^{\prime}.\:\langle\sigma,e_1\rangle\to\langle\sigma^{\prime},e^{\prime}\rangle)\\P(e_2)=\forall\sigma.\:fvs(e_2)\subseteq dom(\sigma)\Longrightarrow e_2\in\mathbf{Int}\mathrm{~or~}(\exists e^{\prime},\sigma^{\prime}.\:\langle\sigma,e_2\rangle\to\langle\sigma^{\prime},e^{\prime}\rangle)
$$
and want to prove:

$$
\begin{aligned}P(x:=e_1;e_2)=&\forall\sigma.\:fvs(x:=e_1;\:e_2)\subseteq dom(\sigma)\Longrightarrow\\&x:=e_1\:;\:e_2\in\mathbf{Int}\:\mathrm{or}\:(\exists e^{\prime},\sigma^{\prime}.\:\langle\sigma,x:=e_1\:;\:e_2\rangle\to\langle\sigma^{\prime},e^{\prime}\rangle)\end{aligned}
$$
We analyze several subcases.

- **Subcase** $e_1= n_1{}$: By rule $Assgn$ we have $\langle\sigma,x:=n_1;e_2\rangle\to\langle\sigma^\prime,e_2\rangle$ where $\sigma^\prime=\sigma[x\mapsto n_1].$

- **Subcase** $e_1\notin$Int: By assumption and the definition of fvs we have

$$
fvs(e_1)\subseteq fvs(x:=e_1;e_2)\subseteq dom(\sigma)
$$

​	Hence, by induction hypothesis we also have $\langle\sigma,e_1\rangle\to\langle\sigma^{\prime},e^{\prime}\rangle$ for some $e^\prime$ and $\sigma^\prime.$ By the rule $Assgn1$ we have $\langle\sigma,x:=e_1;e_2\rangle\to\langle\sigma^{\prime},x:=e_1^{\prime};e_2\rangle$, which finishes the case and the inductive proof. $\square$.