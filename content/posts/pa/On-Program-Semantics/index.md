---
# 1) 新建一篇文章（会创建 posts/my-first-post/index.md）
# hugo new --kind post-bundle posts/my-first-post

# 2) 把配图放到同目录
# posts/my-first-post/
# ├─ index.md
# └─ cover.jpg  ← featuredImage 可填 "cover.jpg"

# 基本
title: "On Program Semantics"
subtitle: ""
date: 2025-09-11T21:19:40+08:00
lastmod: 2025-09-11T21:19:40+08:00
draft: false

# URL / SEO
slug: "On-Program-Semantics"
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



## Transition Systems

The semantics of a program is defined classically in a very general way in
small step operational form, as a transition system: $(\Sigma, \tau, I)$, where $\Sigma$ is a set of
states, $I \subseteq \Sigma$ is the subset of initial states, and $\tau \subseteq \Sigma \times \Sigma$ is a transition relation.
For sequential programs, the state set is defined as
\[
\Sigma \;\overset{\text{def}}{=}\; \mathcal L \times \mathbb M,
\]
where each state $\sigma = (\ell, m)$ has a control part $\ell \in L$ denoting the current program point,
and a memory part $m \in M \overset{\text{def}}{=} V \to \mathcal{V}$ mapping program variables $x \in V$ to
values $v \in \mathcal{V}$. A transition models an atomic execution step, such as executing a
machine-code instruction in a program. We denote by
\[
(\ell, m) \to (\ell',m')
\]
the fact that $((\ell,m), (\ell',m')) \in \tau$, i.e., the program can reach the state $(\ell',m')$ from the
state $(\ell,m)$ after one execution step. The transition system derives mechanically
from the program code itself, e.g., from the sequence of binary instructions or,
more conveniently, by induction on the syntax tree of the source code.

**Example 1.**
Consider the simple programming language syntax:

\[
\begin{array}{rcl}
P, P' & ::= & x := e \;\mid\; \mathbf{if}\; e \;\mathbf{then}\; P \;\mid\; \mathbf{while}\; e \;\mathbf{do}\; P \;\mid\; P ; P'
\end{array}
\]

where program points are denoted by $P$ and expressions by $e$.
The transition system $\tau[\![P]\!]$ is derived by induction on the syntax of $P$ as follows:

\[
\begin{array}{rcl}
\tau[\![\,x := e\,]\!] & \overset{\text{def}}{=} & 
\{ (\ell,m) \to (\ell', m[x \mapsto v]) \;\mid\; m \in \mathbb M, \; e \Downarrow_m v \} \\[1ex]
\tau[\![\,\mathbf{if}\; e \;\mathbf{then}\; P\,]\!] & \overset{\text{def}}{=} & 
\{ (\ell,m) \to (\ell_P, m) \;\mid\; m \in \mathbb M, \; e \Downarrow_m \text{true} \} \;\cup\; \tau[\![P]\!] \\
&& \cup \;\{ (\ell,m) \to (\ell', m) \;\mid\; m \in \mathbb M, \; e \Downarrow_m \text{false} \} \\[1ex]
\tau[\![\,\mathbf{while}\; e \;\mathbf{do}\; P\,]\!] & \overset{\text{def}}{=} &
\{ (\ell,m) \to (\ell_P, m) \;\mid\; m \in \mathbb M, \; e \Downarrow_m \text{true} \} \;\cup\; \tau[\![P]\!] \\
&& \cup \;\{ (\ell,m) \to (\ell', m) \;\mid\; m \in \mathbb M, \; e \Downarrow_m \text{false} \} \\[1ex]
\tau[\![\,P ; P'\,]\!] & \overset{\text{def}}{=} & \tau[\![P]\!] \;\cup\; \tau[\![P']\!]
\end{array}
\]

where $m[x \mapsto v]$ denotes the function mapping $x$ to $v$ and every $y \neq x$ to $m(y)$,
and $e \Downarrow_m v$ states that expression $e$ evaluates to the value $v$ in memory $m$. $\Box$



## Trace Semantics

We are not interested in the program itself, but in the properties of its executions.
Formally, an execution trace is a finite sequence of states
\[
\sigma_0 \to \sigma_1 \to \cdots \to \sigma_n
\]
such that $\sigma_0 \in I$ and $\forall i,\; (\sigma_i,\sigma_{i+1}) \in \tau$.
The semantics we want to observe, which is called a **collecting semantics**,
is thus defined in our case as the set $T \in \mathcal{P}(\Sigma^\ast)$
of traces spawned by the program.
It can be expressed as the least fixpoint
of a continuous operator in the complete lattice of sets of traces:
\[
T = \mathrm{lfp}\; F \quad \text{where}
\]
\[
F(X) \;\overset{\text{def}}{=}\;
I \;\cup\; \{\, \sigma_0 \to \cdots \to \sigma_{i+1}
\;\mid\; \sigma_0 \to \cdots \to \sigma_i \in X \;\wedge\; \sigma_i \to \sigma_{i+1} \,\}.
\tag{1}
\]

That is, $T$ is the smallest set containing traces reduced to an initial state and closed
under the extension of traces by an additional execution step.
Note that we are observing **partial execution traces**;
informally, we allow executions to be interrupted prematurely.
Formally, $T$ is closed under prefix (it also includes the finite prefixes of non-terminating executions).
This is sufficient if we are interested in **safety properties**, i.e.,
properties of the form “no bad state is ever encountered,” which is the case here —
more general trace semantics, able to also reason about **liveness properties**.

A classic result is that $T$ can be restated as the limit of an iteration sequence:
\[
T = \bigcup_{n \in \mathbb{N}} F^n(\varnothing).
\]
It becomes clear then that computing $T$ is equivalent to testing the program on
all possible executions (albeit with an unusual exhaustive breadth-first strategy),
and that it is not adapted to the effective and efficient verification of programs:
when the program has unbounded or infinite executions, $T$ is infinite.


```c++
(1) i := 2;                             at (1): i = 0 ∧ n = 0
(2) n := input int();                   at (2): i = 2 ∧ n = 0
(3) while i < n do                      at (3): 2 ≤ i ≤ max(2,n)
(4) if input bool() then i := i + 1;    at (4): 2 ≤ i ≤ n − 1 ∧ n ≥ 3
(5) done;                               at (5): 2 ≤ i ≤ n ∧ n ≥ 3
(6) assert i >= 2;                      at (6): i = max(2,n)
```

**Example 2.**
The simple program increments $i$ in a loop,
until it reaches a user-specified value $n$.

```tikz {title="Trace Semantics"}
\begin{tikzpicture}[scale=0.8,>=stealth]
  % axes
  \draw[->] (0,0) -- (0,6) node[left] {$n$};
  \draw[->] (0,0) -- (6,0) node[below] {$i$};

  % initial state at (1): i=0, n=0
  \fill (0,0) circle (2pt) node[below left] {$(1)$};

  % after (2): i=2, n=0
  \fill (2,0) circle (2pt) node[below right] {$(2)$};
  \draw[->] (0,0) -- (2,0);

  % loop condition (3): while i<n
  % show different possible n values
  \foreach \n in {2,3,4,5} {
    \fill (2,\n) circle (2pt);
    \draw[dotted] (2,\n) -- (5.5,\n); % dotted line for possible continuations
  }
  \node at (2,5.5) {$\cdots$};

  % transitions from (2,0) to (2,n)
  \foreach \n in {2,3,4,5} {
    \draw[->,bend left=30] (2,0) to (2,\n);
  }

  % body: if input bool() then i := i+1
  % horizontal executions (i increases)
  \foreach \n in {3,4,5} {
    \fill (3,\n) circle (2pt);
    \draw[->] (2,\n) -- (3,\n);
  }
  \foreach \n in {4,5} {
    \fill (4,\n) circle (2pt);
    \draw[->] (3,\n) -- (4,\n);
  }
  \fill (5,5) circle (2pt);
  \draw[->] (4,5) -- (5,5);

  % infinite execution (i never increments, stays at 2)
  \foreach \k in {1,2,3} {
    \fill (2,-\k*1) circle (2pt);
    \draw[->,bend right=30] (2,0) to (2,-\k*1);
  }
  \node at (2,-3.5) {$\cdots$};

\end{tikzpicture}
```

Figure above presents its trace semantics starting
in the state set $I = \{ (1, [\,n \mapsto 0, i \mapsto 0\,]) \}$.
The program has infinite executions (e.g., if $i$ is never incremented). $\Box$


## State Semantics

We observe that, in order to prove safety properties, it is not necessary to compute $T$ exactly.
It is sufficient to collect the set $S \subseteq \mathcal{P}(\Sigma)$ of program states encountered,
abstracting away any information available in $T$ on the ordering of states.
We use an abstraction function
\[
\alpha_{\mathit{state}} : \mathcal{P}(\Sigma^\ast) \to \mathcal{P}(\Sigma)
\]
defined as
\[
S = \alpha_{\mathit{state}}(T) \quad \text{where} \quad
\alpha_{\mathit{state}}(X) \;\overset{\text{def}}{=}\;
\{\, \sigma \mid \forall \sigma_0 \to \cdots \to \sigma_n \in X,\;
\exists i \in [0,n],\; \sigma = \sigma_i \,\}.
\tag{2}
\]

An important result is that, as $T$, the set $S$ can be computed directly as a fixpoint:
\[
S = \mathrm{lfp}\; G \quad \text{where} \quad
G(X) \;\overset{\text{def}}{=}\; I \;\cup\; \{\, \sigma' \mid \exists \sigma \in X,\; \sigma \to \sigma' \,\}.
\]
Equivalently, $S$ can be expressed as the iteration sequence
\[
S = \bigcup_{n \in \mathbb{N}} G^n(\varnothing),
\]
which naturally expresses that $S$ is the set of states reachable from $I$ after zero,
one, or more transitions. The similarity in fixpoint characterisation of $S$ and $T$
is not a coincidence, but a general result of abstract interpretation
(although, in most cases, the abstract fixpoint only over-approximates the concrete one:
$\mathrm{lfp}\; G \;\supseteq\; \alpha_{\mathit{state}}(\mathrm{lfp}\; F)$).

Dually, given a set of states $S$, one can construct the set of traces abstracted by $S$
using a concretization function
\[
\gamma_{\mathit{state}} \;\overset{\text{def}}{=}\;
\lambda S.\;\{\, \sigma_0 \to \cdots \to \sigma_n \mid n \in \mathbb{N},\; \forall i \in [0,n],\; \sigma_i \in S \,\}.
\]
The pair $(\alpha_{\mathit{state}}, \gamma_{\mathit{state}})$ forms a Galois connection. [^1]

[^1]: That is, $\forall X \subseteq \mathcal{P}(\Sigma^\ast), \forall Y \subseteq \mathcal{P}(\Sigma),\;
\alpha_{\mathit{state}}(X) \subseteq Y \iff X \subseteq \gamma_{\mathit{state}}(Y)$.

A classic result states that the best abstraction of $F$ can be defined as
$\alpha_{\mathit{state}} \circ F \circ \gamma_{\mathit{state}}$, which in our case turns out to be exactly $G$.
When the set of states is finite (e.g., when the memory is bounded), $S$ can always be computed
by iteration in finite time, even if $T$ cannot. Obviously, $S$ may be extremely large and require
many iterations to stabilize, hence computing $S$ exactly is not a practical solution;
we will need further abstractions.

## Program Proofs and Inductive Invariants

There is a deep connection between the state semantics and the program logic
of Floyd–Hoare used to prove partial correctness.
If we interpret each logic assertion $A_\ell$ at program point $\ell$ as the set of memory states
$[\![ A_\ell ]\!] \subseteq \mathbb M$ satisfying it, and define
\[
M \;\overset{\text{def}}{=}\; \{\, (\ell,m) \mid m \in [\![ A_\ell ]\!] \,\},
\]
then the assertion family $(A_\ell)_{\ell \in L}$ is a valid (i.e., inductive) invariant
if and only if $G(M) \subseteq M$.
Moreover, the best inductive invariant stems from $\mathrm{lfp}\; G$, i.e., it is $S$.

While, in proof methods, the inductive invariants must be devised by an oracle (the user),
abstract interpretation opens the way to automatic invariant inference
by providing a constructive view on invariants (through iteration)
and allowing further abstractions.



```tikz {title="Reachable State Semantics"}
\begin{tikzpicture}[scale=0.7]
  % axes
  \draw[->] (0,0) -- (0,5) node[left] {$n$};
  \draw[->] (0,0) -- (5,0) node[below] {$i$};
  % reachable states
  \foreach \x/\y in {0/0,2/0,2/1,2/2,2/3,2/4,3/3,3/4, 4/4, 2/-1,2/-2} {
    \fill (\x,\y) circle (2pt);
  }
\end{tikzpicture}
```

**Example 3.**
The optimal invariant assertions of the program in Fig. 1 appear
in the right, and figure above presents graphically its state abstraction at point (3). $\Box$


## Memory Abstractions

In order to gain in efficiency on bounded state spaces and handle unbounded ones,
we need to abstract further. As many errors (such as overflows and divisions by
zero) are caused by invalid arguments of operators, a natural idea is to observe
only the set of values each variable can take at each program point.
Instead of concrete state sets in
\[
\mathcal{P}(\Sigma) \;\overset{\text{def}}{=}\; \mathcal{P}(L \times (\mathcal V \to \mathbb {V})),
\]
we manipulate abstract states in
\[
\Sigma^\sharp \;\overset{\text{def}}{=}\; (L \times \mathcal V) \to \mathcal{P}(\mathbb{V}).
\]
The concrete and abstract domains are linked through
the following Galois connection (so-called Cartesian Galois connection):
\[
\alpha_{\mathit{cart}}(X) \;\overset{\text{def}}{=}\;
\lambda (\ell, v).\;\{\, a \mid (\ell,m) \in X, \; m(v) = a \,\}
\]
\[
\gamma_{\mathit{cart}}(X^\sharp) \;\overset{\text{def}}{=}\;
\{\, (\ell,m) \mid \forall v,\; m(v) \in X^\sharp(\ell,v) \,\}.
\tag{3}
\]

Assuming that variables are integers or reals,
a further abstraction consists in maintaining, for each variable, its bounds
instead of all its possible values.
We compose the connection $(\alpha_{\mathit{cart}}, \gamma_{\mathit{cart}})$
with the element-wise lifting of the following connection
$(\alpha_{\mathit{itv}}, \gamma_{\mathit{itv}})$ between $\mathcal{P}(\mathbb{R})$
and intervals $(\mathbb{R} \to \{-\infty\}) \times (\mathbb{R} \to \{+\infty\})$:
\[
\alpha_{\mathit{itv}}(X) \;\overset{\text{def}}{=}\; [\min X,\; \max X]
\]
\[
\gamma_{\mathit{itv}}([a,b]) \;\overset{\text{def}}{=}\;
\{\, x \in \mathbb{R} \mid a \leq x \leq b \,\},
\tag{4}
\]
yielding the **interval abstract domain**.

Another example of memory domain is the **linear inequality domain**
that abstracts sets of points in $\mathcal{P}(\mathcal V \to \mathbb{R})$ into convex, closed polyhedra.
One abstracts $\mathcal{P}(\Sigma) \simeq \mathcal L \to \mathcal{P}( \mathcal V \to \mathbb{R})$
by associating a polyhedron to each program point,
which permits the inference of linear relations between variables.
The polyhedra domain is thus a **relational domain**,
unlike the domains deriving from the Cartesian abstraction (such as intervals).

**Example 4.**


```tikz {title="Interval Semantics"}
\begin{tikzpicture}[scale=0.9]
  % axes
  \draw[->] (0,0) -- (0,4) node[left] {$n$};
  \draw[->] (0,0) -- (4,0) node[below] {$i$};
  % filled interval box
  \fill[teal!80] (1,0) rectangle (3.5,3.5);
\end{tikzpicture}
```

Figure above presents the interval abstraction of the program in Fig. 1 at point (3).
The relational information that $i \leq n$ when $n \leq 2$ is lost.  $\Box$

Following the same method that derived the state semantics from the trace one,
we can derive an interval semantics from the state one:
it is expressed as a fixpoint $\mathrm{lfp}\; G^\sharp$ of an interval abstraction $G^\sharp$ of $G$.
While it is possible to define an optimal $G^\sharp$ as
$\alpha \circ G \circ \gamma$ — where $(\alpha,\gamma)$ combines
$(\alpha_{\mathit{itv}}, \gamma_{\mathit{itv}})$ and $(\alpha_{\mathit{cart}}, \gamma_{\mathit{cart}})$ —
this is a complex process when $G$ is large,
and it must be performed for each $G$, i.e., for each program.
To construct a general analyzer, we use the fact that $F$,
and so $G = \alpha_{\mathit{state}} \circ F \circ \gamma_{\mathit{state}}$,
are built by combining operators for atomic language constructs, as in Example 1.
It is thus possible to derive $G^\sharp$ as a combination of a small fixed set of abstract operators.
More precisely, we only have to design abstraction versions of assignments, tests, and set union.
Generally, the combination of optimal operators is not optimal, and so the resulting $G^\sharp$ is not optimal.
Additionally, due to efficiency and practicality concerns,
the base abstract operators may be chosen non-optimal to begin with.
Achieving a **sound analysis**, i.e.,
\[
\gamma(\mathrm{lfp}\; G^\sharp) \supseteq \mathrm{lfp}\; G,
\]
only requires sound abstract operators,
i.e., $\forall X^\sharp,\; \gamma(G^\sharp(X^\sharp)) \supseteq G(\gamma(X^\sharp))$.
The fact that $G^\sharp \neq \alpha \circ G \circ \gamma$,
and even the existence of an $\alpha$ function, while sometimes desirable,
is by no means required — for instance, the polyhedra domain
does not feature an abstraction function $\alpha$
as some sets, such as discs, have no best polyhedral abstraction.

**Example 5.**
The interval abstraction ${[\![x := y + z]\!]}^\sharp$ of a simple addition maps
the interval environment
\[
[x \mapsto [l_x,h_x],\; y \mapsto [l_y,h_y],\; z \mapsto [l_z,h_z]]
\]
at point $\ell$ to
\[
[x \mapsto [l_y + l_z,\; h_y + h_z],\; y \mapsto [l_y,h_y],\; z \mapsto [l_z,h_z]]
\]
at point $\ell'$, which is optimal.  $\Box$

**Example 6.**
The optimal abstraction
${[\![ \; y := \pm z;\; x := y + z \;]\!]}^\sharp$ would map $x$ to $[0,0]$.
However, the combination
${[\![ \; y := \pm z \;]\!]}^\sharp \circ
 {[\![ \; x := y + z \;]\!]}^\sharp$ maps $x$ to
$[l_z - h_z,\; h_z - l_z]$ instead, which is coarser when $l_z < h_z$.  $\Box$

**Example 7.**
We can design a sound non-optimal fall-back abstraction for arbitrary assignments
${[\![\; x := e \;]\!]}^\sharp$
by simply mapping $x$ to $[-\infty, +\infty]$.  $\Box$

We now know how to define systematically a sound abstract operator $G^\sharp$
which can be efficiently computed.
Nevertheless, the computation of $\mathrm{lfp}\; G^\sharp$ by naive iteration
may not converge fast enough (or at all).
Hence, abstract domains are generally equipped with a convergence acceleration
binary operator $\triangledown$, called **widening**.
Widenings extrapolate between two successive iterates
and ensure that any sequence
\[
X^\sharp_{i+1} \;\overset{\text{def}}{=}\; X^\sharp_i \;\cup\; G^\sharp(X^\sharp_i)
\]
converges in finite time,
possibly towards a coarse abstraction of the actual fixpoint.

**Example 8.**
The classic interval widening sets unstable bounds to infinity:
\[
[l_1,h_1] \;\triangledown\; [l_2,h_2]
\;\overset{\text{def}}{=}\;
\bigl[\, \begin{cases}
-\infty & \text{if } l_2 < l_1 \\ l_1 & \text{otherwise}
\end{cases}
,\;
\begin{cases}
+\infty & \text{if } h_2 > h_1 \\ h_1 & \text{otherwise}
\end{cases}
\,\bigr].
\tag{5}
\]
When analyzing Fig.~1, the iterations with widening at (3)
give the following intervals for $i$:
$i^\sharp_0 = [2,2]$ and $i^\sharp_1 = [2,2] \;\triangledown\; [2,3] = [2,+\infty]$,
which is stable.  $\Box$

To balance the accumulation of imprecision caused by widenings
and combining separately abstracted operators,
it is often necessary to reason on an abstract domain
strictly more expressive than the properties we want to prove —
e.g., the polyhedra domain may be necessary to infer variable bounds
that the interval domain cannot infer, although it can represent them,
such as $x=0$ in Example 6.