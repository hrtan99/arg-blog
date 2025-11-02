---
# 1) 新建一篇文章（会创建 posts/my-first-post/index.md）
# hugo new --kind post-bundle posts/my-first-post

# 2) 把配图放到同目录
# posts/my-first-post/
# ├─ index.md
# └─ cover.jpg  ← featuredImage 可填 "cover.jpg"

# 基本
title: "On Precision of Domain Combination (1)"
subtitle: ""
date: 2025-09-18T14:42:47+08:00
lastmod: 2025-11-02T14:42:47+08:00
draft: false

# URL / SEO
slug: "On-Precision-of-Domain-Combination-1"
aliases: []
description: ""
# 若你用自定义永久链接，可在 config 里用 :slug
# [permalinks]
# posts = "/blog/:year/:slug/"

# 分类
categories: []
tags: []
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
tikz: true

# 多语言（若你启用 i18n，可留空或按语言单独写）
# translations:
#   - language: "en"
#     title: ""
#     description: ""
---
<!-- 摘要（可选）：写在此注释上方或 summary 字段里；正文从这里开始。 -->


In static analysis based on abstract interpretation, convex abstract 
domains such as the interval, octagon, or polyhedra domains are often 
employed to capture numerical invariants.
However, these convex abstractions inherently lose precision when the  concrete semantics involve disjunctions of states. 
In many verification problems, reasoning about disjoint execution paths 
or mutually exclusive conditions requires abstract domains capable of 
representing disjunctions explicitly.

To address this limitation, several constructions have been proposed 
to extend the expressiveness of existing domains:
- Disjunctive completion
- Cardinal power and state partitioning
- Trace partitioning

These constructions can be understood as domain combinators that systematically build new abstract domains from existing ones.

## Domain Combinators

A *domain combinator* (or *domain constructor*) is a higher-order abstraction mechanism that takes one or more abstract domains as inputs and produces a new domain with well-defined semantics.
Each abstract domain $\mathbb D^\sharp$ is characterized by:
- a concrete domain $\mathbb D = (\wp(\mathbb M), \subseteq)$,
- an abstraction function $\alpha : \mathbb{D} \rightarrow \mathbb D^\sharp$ and concretization $\gamma : \mathbb D^\sharp \rightarrow \mathbb{D}$,
- and abstract operators (post, join, widening, etc.).

The advantage of defining such combinators is that they can be defined once and for all, and implemented modularly.
In ML-style pseudo-code, a domain is a module implementing a given interface:
```ocaml
module D = (struct ... end : DOMAIN)
```
and a abstract domain combinator is a functor:
```ocaml
module C = functor (D : DOMAIN) -> (struct ... end : DOMAIN)
```

### Example: Product and Coalescent Product Domains

Given two abstract domains $(\mathbb D_0^\sharp, \gamma_0, \bot_0)$
and $(\mathbb D_1^\sharp, \gamma_1, \bot_1)$, the 
**product abstraction** is \((\mathbb D^\sharp_\times, \gamma_\times, \bot_\times)\)
where
\[
\begin{align*}
  \mathbb D_\times^\sharp &= \mathbb D_0^\sharp \times \mathbb D_1^\sharp, \\
  \gamma_\times(x^\sharp_0, x^\sharp_1) &= \{ x \mid x \in \gamma_0(x^\sharp_0),\ x \in \gamma_1(x^\sharp_1) \}, \\
  \bot_\times &= (\bot_0, \bot_1)
\end{align*}
\]
That is, the product abstraction expresses *conjunctions* of elements of \(\mathbb D^\sharp_0\) and  \(\mathbb D^\sharp_1\).

However, this simple product may be imprecise:
\[
  \forall x_0^\sharp \in \mathbb D_0^\sharp, x_1^\sharp \in \mathbb D_1^\sharp ,\quad
  \gamma_\times(\bot_0^\sharp, x_1^\sharp) = \gamma_\times(\bot_0^\sharp, x_1^\sharp) = \emptyset = \gamma_\times(\bot_\times)
\]

A **reduction** or **coalescent product** is often applied to improve precision by enforcing consistency between both components.

Given abstract domains \((\mathbb D_0^\sharp, \gamma_0, \bot_0)\) and \((\mathbb D_1^\sharp, \gamma_1, \bot_1)\), their coalescent product
\((\mathbb D^\sharp_{\otimes}, \gamma_{\otimes}, \bot_{\otimes})\) satisfies:
\[
\begin{align*}
  \mathbb D_{\otimes}^\sharp &= \{\bot_\times\} \uplus \{ (x_0^\sharp, x_1^\sharp) \in \mathbb D_0^\sharp \times \mathbb D_1^\sharp \mid x^\sharp_0 \neq \bot_0 \wedge x^\sharp_1 \neq \bot_1 \}, \\
  \gamma_{\otimes}(x_0^\sharp, x_1^\sharp) &= \gamma_0(x_0^\sharp) \cap \gamma_1(x_1^\sharp), \\
  \bot_{\otimes} &= (\bot_0, \bot_1) \\
  \gamma_{\otimes}(\bot_{\otimes}) &= \emptyset
\end{align*}
\]


Nontheless, in many cases, the product and coalescent product abstractions remain insufficient to achieve reduction.
For instance, combining the interval domain and the congruence domain:
\[
  \gamma_\otimes((x \in [3,4]), (x \equiv 0 \pmod 5)) = \emptyset,
\]
result in an empty concretization, which suggests that mere product is still producing useless elements in the abstract domain.
This leads to a question: *Can we design abstract domains that inherently capture disjunctive properties?*

## Limitations of Convex Abstractions

```tikz {title="Example Domains"}
\begin{tikzpicture}[line cap=round, scale=0.5]
% ---------- 公共宏 ----------
% 坐标轴（每幅图共用）
\newcommand{\axes}{
  \draw[very thick,->] (-2,0) -- (6,0) node[below] {$x$};
  \draw[very thick,->] (0,-1.8) -- (0,4.5) node[left] {$y$};
  \foreach \x in {1,2,3,4,5} \draw (\x,0.1)--(\x,-0.1);
  \foreach \y in {-1,1,2,3,4} \draw (0.1,\y)--(-0.1,\y);
}
% 蓝色数据点
\newcommand{\dotp}[2]{\draw[fill=blue!20,draw=blue!70, line width=0.9pt] (#1,#2) circle (2.6pt);}

% =========================================================
% 1) interval domain
% =========================================================
\begin{scope}[xshift=0cm]
  \axes
  % 绿色矩形区域（区间域）
  \path[fill=blue!35, draw=blue!50!black, line width=0.9pt, opacity=0.25]
    (-1,-1.2) -- (5,-1.2) -- (5,3.8) -- (-1,3.8) -- cycle;

  % 一些样例点
  \dotp{-1}{3} \dotp{-1}{1} \dotp{0.3}{2.2} \dotp{2.2}{2.0}
  \dotp{3.7}{3.8} \dotp{0}{-1.2}

  % 标题
  \node[font=\small\bfseries, text=violet] at (2.5,-2.2) {Interval Domain};
\end{scope}

% =========================================================
% 2) octagon domain
% =========================================================
\begin{scope}[xshift=8.9cm]
  \axes
  % 八边形近似区域（±x、±y、±x±y 约束的典型外形）
  \path[fill=blue!35, draw=blue!50!black, line width=0.9pt, opacity=0.25]
    (-1,-1) -- (-1, 1) -- (1.8, 3.8)
    -- (3.8, 3.8) -- cycle;

  % 样例点
  \dotp{-1}{-1} \dotp{0}{2} \dotp{-0.5}{0.4} \dotp{1.2}{2.2} \dotp{2.3}{3.8}

  % 标题
  \node[font=\small\bfseries, text=violet] at (2.5,-2.2) {Octagon Domain};
\end{scope}

% =========================================================
% 3) polyhedra domain
% =========================================================
\begin{scope}[xshift=17.8cm]
  \axes
  % 一般多面体（多边形）区域
  \path[fill=blue!35, draw=blue!50!black, line width=0.9pt, opacity=0.25]
    (-1,-1) -- (-1,0) -- (0,2) -- (2.5,3.8)
    -- (5.7,3.8) -- (4.5,2) -- cycle;

  % 样例点
  \dotp{-1}{-1} \dotp{-1}{0} \dotp{0}{1} \dotp{0}{2} \dotp{2.5}{3.8} \dotp{1.4}{1.3}
  \dotp{3.3}{2.3} \dotp{5.7}{3.8} \dotp{4.5}{2}

  % 标题
  \node[font=\small\bfseries, text=violet] at (2.5,-2.2) {Polyhedra Domain};
\end{scope}

\end{tikzpicture}
```


Convex domains such as intervals, octagons, and polyhedra represent convex sets of numerical states. This convexity implies that the abstract join $\sqcup$—an over-approximation of concrete union—can introduce spurious states.

```tikz {title="Imprecision in Product Domain"}
\begin{tikzpicture}[scale=0.62, line cap=round]
% --- axes ---
\draw[very thick, ->] (-0.6,0) -- (13,0) node[below] {$x$};
\draw[very thick, ->] (0,-1.6) -- (0,5.2) node[left] {$y$};
\foreach \x in {1,3,5,7,9,11,13} \draw (\x,0.12)--(\x,-0.12);
\foreach \y in {1,2,3,4} \draw (0.12,\y)--(-0.12,\y);

% --- left green poly: x0^# ---
\coordinate (L1) at (0,-1.00);
\coordinate (L2) at (0, 1.00);
\coordinate (L3) at (2, 3.00);
\coordinate (L4) at (4, 3.00);
\path[fill=blue!35, draw=blue!50!black, line width=0.9pt, opacity=0.95]
      (L1)--(L2)--(L3)--(L4)--cycle;
\node[blue!35!black] at (1.5,1.5) {$x_0^\sharp$};

% --- right green poly: x1^# ---
\coordinate (R1) at (10,1.0);
\coordinate (R2) at (12,1.0);
\coordinate (R3) at (12,3.0);
% \coordinate (R4) at (13,3.2);
\path[fill=blue!35, draw=blue!50!black, line width=0.9pt, opacity=0.95]
      (R1)--(R2)--(R3)--cycle;
\node[blue!35!black] at (11.5,1.5) {$x_1^\sharp$};

% --- red imprecise join region ---
\path[fill=red!60, fill opacity=0.25, draw=red!70!black, line width=0pt]
      (0,-1) -- (4,3) -- (12,3) --(10,1) -- (12,1) -- cycle;

\node[red!70!black] at (6.0,1.5) {$x_0^\sharp \;\sqcup\; x_1^\sharp$};
\node[red!70!black, scale=1.2] at (7.0,2.5) {Imprecision};

\end{tikzpicture}
```


Such imprecisions may cause verification to fail, as the analyzer may infer spurious states that do not correspond to any concrete execution.
Non-convex abstractions aim to overcome this limitation.
For instance:
- The **congruence domain** expresses sets such as $\{ n + k p \mid k \in \mathbb{Z} \}$, which are non-convex.
- The **sign domain** includes an element $[=0]$ representing $\{0\}$, which is not convex, and $0\not\in\gamma([\neq 0])$, so $[\neq 0]$ also describes a non convex set, even though other elements like $[+]$ or $[-]$ correspond to convex sets.


### Example 1: Verification Problem with Boolean Guards

Consider the following C program:

```c
bool b0, b1;
int x, y;  // uninitialized
b0 = (x > 0);
b1 = (x < 0);
if (b0 && b1)
    y = 0;
else
    y = 100 / x;
```

At the division point, the operation is safe because:
- if $b_0$ holds, then $x > 0$;
- if $b_1$ holds, then $x < 0$;
- if either guard fails, then $x = 0$ cannot occur in a concrete execution reaching the division.

However, a non-relational abstraction such as intervals may represent $x \in [-\infty, +\infty]$ at this point, thus failing to verify safety. 
If we annotate the program with the invariant:

```c
bool b0, b1;
int x, y;   // uninitialized

b0 = x >= 0;
(b0 && x >= 0) || (!b0 && x < 0)

b1 = x <= 0;
(b0 && b1 && x == 0) || (b0 && !b1 && x > 0) || (!b0 && b1 && x < 0)

if (b0 && b1) {
  (b0 && b1 && x == 0)
  y = 0;
  (b0 && b1 && x == 0 && y == 0)
} else {
  (b0 && !b1 && x > 0) || (!b0 && b1 && x < 0)
  y = 100 / x;
  (b0 && !b1 && x > 0) || (!b0 && b1 && x < 0)
}
```

This shows the necessity of symbolic disjunctions to reason about disjoint cases.


### Example 2: Relation Between Variables

Consider another example:

```c
int x, s, y;
if (x > 0)
    s = 1;
else
    s = -1;
y = x * s;
assert(y >= 0);
```

Here, $s$ is always non-null, and has the same sign as $x$, implying $y \ge 0$.
Convex abstractions (such as intervals) cannot establish that $s \neq 0$, while the congruence domain cannot express the correlation between $s$ and $x$, capturing the correlation “$s$ has the same sign as $x$”.
If we annotate the program with the invariant:

```c
int x;    // x in Z
int s;
int y;
if (x >= 0){
  (x >= 0)
  s = 1;
  (x >= 0 && s == 1)
} else {
  (x < 0)
  s = -1;
  (x < 0 && s == -1)
}
(x >= 0 && s == 1) || (x < 0 && s == -1)

y = x / s;
(x >= 0 && s == 1 && y >= 0) || (x < 0 && s == -1 && y > 0)

assert(y >= 0);
```


Thus, expressing such relations requires a non-trivial relational and possibly disjunctive numerical abstraction.


## Disjunctive Completion

### Motivation

As discussed previously, convex abstract domains such as intervals or polyhedra fail to represent disjunctive properties.
A natural idea to address this limitation is to extend a given abstract domain so that it can represent disjunctions of its own elements.
This leads to the notion of the **disjunctive completion** (also called the *distributive completion*) of an abstract domain.

### Distributive Abstract Domains

Let $(\mathbb D, \sqsubseteq_{\mathbb D}, \sqcup_{\mathbb D})$ be a concrete lattice and $(\mathbb D^\sharp, \sqsubseteq_{\mathbb D^\sharp}, \sqcup_{\mathbb D^\sharp})$ an abstract domain with a concretization function $\gamma : \mathbb D^\sharp \to \wp(\mathbb D)$.
An abstract domain is said to be *distributive* (or *disjunctive*) if and only if:
\[
\forall X \subseteq \mathbb D^\sharp, \quad \gamma\Big(\bigsqcup_{x \in X} x\Big) = \bigsqcup_{x \in X} \gamma(x).
\]
That is, abstract joins correspond exactly to concrete unions.

**Examples.**
- The flat lattice of constants extended with $\top$ and $\bot$, i.e. $(\mathbb{Z}_0, \sqsubseteq)$, is distributive.
- The lattice $\{ \bot, [<0], [=0], [>0], [\leq 0], [\geq 0], [\neq 0], \top \}$ of signs is distributive.
- The lattice of intervals is not distributive. For example, there is no single abstract interval whose concretization equals the union of $\gamma([0,10])$ and $\gamma([12,20])$.


### Definition of Disjunctive Completion

**Disjunctive Completion.**
Given an abstract domain $(\mathbb D^\sharp, \sqsubseteq^\sharp)$ with concretization function $\gamma: \mathbb D^\sharp \to \wp(\mathbb D)$, the **disjunctive completion** $(\mathbb D^\sharp_{\mathrm{disj}}, \sqsubseteq^\sharp_{\mathrm{disj}})$ with a concretization function $\gamma_{\mathrm{disj}}: \mathbb D^\sharp_{\mathrm{disj}} \to \wp(\mathbb D)$ is the smallest distributive abstract domain that extended from $\mathbb D^\sharp$ such that:
\[
\begin{align*}
  &\mathbb D^\sharp \subseteq \mathbb D^\sharp_{\mathrm{disj}} \\
  &\forall x^\sharp \in \mathbb D^\sharp, \quad \gamma_{\mathrm{disj}}(x^\sharp) = \gamma(x^\sharp) \\
  &(\mathbb D^\sharp_{\mathrm{disj}}, \sqsubseteq^\sharp_{\mathrm{disj}}) \text{ with } \gamma_{\mathrm{disj}} \text{ is distributive}
\end{align*}
\]

To build the disjunctive completion domain, we follow these steps:
- include in $\mathbb D^\sharp_{\mathrm{disj}}$ all elements of the base abstract domain $\mathbb D^\sharp$,
- for all finite subsets $S \subseteq \mathbb D^\sharp$, such that there is no $x^\sharp \in \mathbb D^\sharp$ and $\gamma(x^\sharp) = \bigsqcup_{y \in S} \gamma(y)$, add the element $\bigsqcup S$ to $\mathbb D^\sharp_{\mathrm{disj}}$ with $\gamma_{\mathrm{disj}}(\bigsqcup S) = \bigcup_{y \in S} \gamma(y)$.


**Theorem.**
The process of adding all distinct disjunctions of abstract elements yields a sound and distributive abstract domain.


### Example 1: Completion of the Sign Domain

Consider the sign domain with elements \(\{[\!-\!], [0], [\!+\!], [\!-\!0], [0\!+], [\!-\!0\!+]\}\):
```tikz
\begin{tikzpicture}[line cap=round, line width=1pt, scale=0.8]
  % 中间三元素
  \node (m0) at (0,0) {$[0]$};
  \node (mn) at (-2,0) {$[-]$};
  \node (mp) at ( 2,0) {$[+]$};

  \node(top) at (0,1.6) {$\top$};
  \node(bot) at (0,-1.6) {$\bot$};

  % 覆盖边：⊤ → 三元素
  \draw (top) -- (mn);
  \draw (top) -- (m0);
  \draw (top) -- (mp);

  % 覆盖边：三元素 → ⊥
  \draw (mn) -- (bot);
  \draw (m0) -- (bot);
  \draw (mp) -- (bot);
\end{tikzpicture}
```

representing respectively $\{x<0\}$, $\{x=0\}$, $\{x>0\}$, $\{x\le 0\}$, $\{x\ge 0\}$, and $\mathbb{Z}$.

The disjunctive completion $\mathbb D^\sharp_{\mathrm{disj}}$ adds new abstract elements corresponding to all unions of disjoint sign classes. For example:
\[
[\!-\!] \sqcup [0], \quad [\!-\!] \sqcup [\!+\!], \quad [0] \sqcup [\!+\!]
\]

```tikz
\begin{tikzpicture}[line cap=round, line width=1pt, node distance=2cm, scale=0.8]
  % 结点
  \node (Top) at (0,3.2) {$\top$};

  \node (le0) at (-3,1.6) {$[\le 0]$};
  \node (ne0) at ( 0,1.6) {$[\neq 0]$};
  \node (ge0) at ( 3,1.6) {$[\ge 0]$};

  \node (neg) at (-3,0.0) {$[-]$};
  \node (zero)at ( 0,0.0) {$[0]$};
  \node (pos) at ( 3,0.0) {$[+]$};

  \node (Bot) at (0,-1.6) {$\bot$};

  % 边：顶到中层
  \draw (Top)--(le0);
  \draw (Top)--(ne0);
  \draw (Top)--(ge0);

  % 边：中层到底层（交叉连线）
  \draw (le0)--(neg);
  \draw (le0)--(zero);

  \draw (ne0)--(neg);
  \draw (ne0)--(pos);

  \draw (ge0)--(zero);
  \draw (ge0)--(pos);

  % 边：底层到 ⊥
  \draw (neg)--(Bot);
  \draw (zero)--(Bot);
  \draw (pos)--(Bot);
\end{tikzpicture}
```

This completion allows representing non-convex sets such as $\{x < 0\} \cup \{x > 0\}$, which corresponds to $x \neq 0$—impossible to express in the base sign domain.

### Example 2: Completion of the Constant Domain

Let the concrete lattice $\mathbb D = (\mathbb{Z}, \subseteq)$ and the abstract domain $\mathbb D^\sharp = \{ [k] \mid k \in \mathbb{Z}\} \cup \{\top\}$ with $\gamma([k]) = \{k\}$.  
The disjunctive completion of $\mathbb D^\sharp$ is isomorphic to the powerset lattice $\wp(\mathbb{Z})$:
\[
\mathbb D^{\sharp}_\mathrm{disj} \equiv \wp(\mathbb{Z}), \qquad \gamma_{\mathrm{disj}}([S]) = S.
\]

```tikz
\begin{tikzpicture}[line cap=round, line width=.5pt, scale=.5]

% 坐标参数
\def\yTop{2}
\def\yMid{0}
\def\yBot{-2}

% 顶与底
\node (TopLabel) at (0,\yTop+0.55) {$\top$};

\node (BotLabel) at (0,\yBot-0.55) {$\bot$};

% 中间节点
\node (m-2) at (-4,\yMid) {$[-2]$};
\node (m-1) at (-2,\yMid) {$[-1]$};
\node (m0)  at ( 0,\yMid) {$[0]$};
\node (m1)  at ( 2,\yMid) {$[1]$};
\node (m2)  at ( 4,\yMid) {$[2]$};

% 省略号
\node (dotsL) at (-6.2,\yMid) {$\cdots$};
\node (dotsR) at ( 6.2,\yMid) {$\cdots$};


% --- 自动连线 ---
% 顶到中层
\foreach \n in {dotsL,m-2,m-1,m0,m1,m2,dotsR} {
  \draw (TopLabel) -- (\n);
}

% 中层到底
\foreach \n in {dotsL,m-2,m-1,m0,m1,m2,dotsR} {
  \draw (\n) -- (BotLabel);
}

\end{tikzpicture}
```

This completion is exact—no loss of information—but it is often infinite and computationally intractable.

A intermediate solution is to define **$k$-bounded disjunctive completion**, which restricts disjunctions to at most $k$ elements:
\[
\mathbb D^\sharp_{\mathrm{disj}(k)} = \{ [S] \mid S \subseteq A,\, |S| \le k \}.
\]
For $k=2$, the abstraction precisely represents all binary disjunctions (pairs of constants) while approximating larger disjunctions by $\top$.

### Example 3: Completion of the Interval Domain
We still consider the concrete domain $\mathbb D = (\mathbb{Z}, \subseteq)$, but now let the abstract domain $\mathbb D^\sharp$ be the standard interval domain:
\[
\mathbb D^\sharp = \{ [a,b] \mid a,b \in \mathbb{Z} \cup \{-\infty,+\infty\}, a \le b \},
\quad
\gamma([a,b]) = \{x \mid a \le x \le b\}.
\]
Its disjunctive completion $\mathbb D^{\sharp}_\mathrm{disj}$ collects all finite unions of disjoint intervals:
\[
\mathbb D^{\sharp}_\mathrm{disj} = \left\{ [I_1 \cup \cdots \cup I_n] \;\middle|\; I_i = [a_i,b_i],\ b_i < a_{i+1} \right\}.
\]

This representation is expressive and efficient compared to the powerset of constants, though potentially infinite.

**Expressiveness.**
Note that $(\mathbb D^\sharp)^n_{\mathrm{disj}} \neq (\mathbb D^{\sharp}_\mathrm{disj})^n$.
The former represents disjunctions across the entire tuple, while the latter represents independent disjunctions per component.

### Application to Verification (Example Revisited)

Using the disjunctive completion of $(\text{intervals})^3$, we can express the invariant in Example~2:
\[
(x \geq 0 \wedge s = 1 \wedge y \ge 0) \;\lor\; (x < 0 \wedge s = -1 \wedge y \ge 0),
\]
which is impossible in convex domains but naturally expressible here.
This demonstrates the gain in precision obtained through disjunctive completion.

### Static Analysis with Disjunctive Completion

We recall that for a program semantics defined by a concrete post-condition operator:
\[
\text{post} : \wp(\mathbb S) \to \wp(\mathbb S),
\]
where $\mathbb S$ is the set of program states,
its abstraction should satisfy $\alpha \circ \text{post} \sqsubseteq \text{post}^{\sharp} \circ \alpha$.
In the disjunctive completion domain, we can define:
\[
\text{post}^{\sharp}_{\mathrm{disj}}([S]) = [ \{ \text{post}^{\sharp}(x) \mid x \in S \} ],
\]
which applies the abstract transfer function pointwise to each disjunct.

The disjunctive completion also provides an *exact* join operator:
\[
[S_1] \sqcup^{\mathrm{disj}} [S_2] = [S_1 \cup S_2].
\]
However, it does not admit a general widening operator due to the combinatorial explosion of disjuncts.

### Static Analysis with Disjunctive Completion

To carry out the analysis of a basic imperative language, we define the following components.

**Operations for the Computation of Post-Conditions.**
We require a sound over-approximation for basic program steps.
- The **concrete** post-condition:
  \[
    post : \wp(\mathbb{S}) \to \wp(\mathbb{S})
  \]
  where $\mathbb{S}$ is the set of concrete states.
- The **abstract** post-condition:
  \[
    post^{\sharp} : \mathbb{D}^{\sharp} \to \mathbb{D}^{\sharp}
  \]
  should satisfy the soundness property:
  \[
    post \circ \gamma \subseteq \gamma \circ post^{\#}.
  \]


For example, when the post operation is the following cases:
- For assignments:
    \[
      post^{\sharp} = assign
    \]
    which inputs a variable, an expression, and an abstract pre-condition, and outputs an abstract post-condition.
- For conditional tests:
    \[
      post^{\sharp} = test
    \]
    which inputs a boolean expression, an abstract pre-condition, and outputs an abstract post-condition.

**Additional Operators.**
- An operator **join** for over-approximation of concrete unions.
- A **widening** operator $\nabla$ for the analysis of loops.
- A conservative inclusion checking operator.


### Transfer Functions for the Computation of Abstract Post-Conditions

We assume a monotone concrete post-condition operation
\(
  post : \mathbb{D} \to \mathbb{D},
\)
and an abstract operation
\(
  post^{\sharp} : \mathbb{D}^{\sharp} \to \mathbb{D}^{\sharp}
\)
such that
\(
  post \circ \gamma \subseteq \gamma \circ post^{\sharp}.
\)

**Convention.**
If
\(
  \gamma(y^{\sharp}) = \bigsqcup \{ \gamma(z^{\sharp}) \mid z^{\sharp} \in \mathcal{E} \},
\)
we write
\(
  y^{\sharp} = \bigsqcup\mathcal{E}.
\)

**Disjunctive Completion.**
For the disjunctive completion domain, we can simply define:
\(
  post^{\sharp}_{disj}\bigl(\left[\sqcup \mathcal{E}\right]\bigr)
  = \{ \sqcup \, post^{\sharp}(x^{\sharp}) \mid x^{\sharp} \in \mathcal{E} \,\}.
\)
Note that the result may be an element of the initial domain.


### Abstract Join, Inclusion, and Widening
- Abstract join: the disjunctive completion provides an *exact join* (exercise).
- Inclusion check: left as exercise.
- Widening: there is no general definition or solution to the disjunct explosion problem.


### Limitations
While theoretically elegant, disjunctive completion suffers from severe practical issues:
- **Combinatorial explosion**: if $A$ has $n$ elements, then $A^{\mathrm{disj}}$ may have up to $2^n$ elements.
- **Representation cost**: for numerical domains such as intervals, $A^{\mathrm{disj}}$ can describe arbitrary finite unions of numbers.
- **Lack of general widening**: no universal strategy exists to ensure convergence during fixpoint computation.


A common mitigation technique is *$k$-limiting*, which bounds the number of disjuncts allowed in any element.  
When merging disjunctions beyond this limit, the join operator must heuristically decide which disjuncts to retain or merge.

