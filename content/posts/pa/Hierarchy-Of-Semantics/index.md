---
# 1) 新建一篇文章（会创建 posts/my-first-post/index.md）
# hugo new --kind post-bundle posts/my-first-post

# 2) 把配图放到同目录
# posts/my-first-post/
# ├─ index.md
# └─ cover.jpg  ← featuredImage 可填 "cover.jpg"

# 基本
title: "Hierarchy of Semantics"
subtitle: ""
date: 2025-09-16T20:08:05+08:00
lastmod: 2025-09-16T20:08:05+08:00
draft: true

# URL / SEO
slug: "Hierarchy-Of-Semantics"
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
tikz: false

# 多语言（若你启用 i18n，可留空或按语言单独写）
# translations:
#   - language: "en"
#     title: ""
#     description: ""
---
<!-- 摘要（可选）：写在此注释上方或 summary 字段里；正文从这里开始。 -->


\section{Introduction}
The theory of abstract interpretation provides a unifying framework for deriving multiple program semantics from one another through systematic abstraction. 
Starting from the \emph{maximal trace semantics}, which captures all finite and infinite execution traces of a transition system, we can derive a hierarchy of increasingly abstract semantics, such as relational semantics, denotational semantics, predicate transformer semantics, and state reachability semantics. 
Each semantics is formally related to others by Galois connections, forming a semantic lattice [oai_citation:2‡Constructive Design of a Hierarchy of Semantics of a Transition System by Abstract Interpretation.pdf](file-service://file-N8TYcGD5rqy8tqm8B1mLUe).

\section{Transition Systems and Executions}
Let a transition system be given by $\langle \Sigma, \tau \rangle$:
\begin{itemize}
  \item $\Sigma$: the set of states,
  \item $\tau \subseteq \Sigma \times \Sigma$: the transition relation,
  \item $I \subseteq \Sigma$: initial states,
  \item $F \subseteq \Sigma$: final or blocking states.
\end{itemize}
A program execution is represented as a sequence (finite or infinite) of states $\sigma_0, \sigma_1, \dots$ such that $\sigma_i \to_\tau \sigma_{i+1}$ [oai_citation:3‡02-sem_ok.pdf](file-service://file-GQRDJE8RnXagke3qcT17Dq).

\section{Maximal Trace Semantics}
\subsection{Definition}
The \emph{maximal trace semantics} $\tau^{\infty}$ is defined as [oai_citation:4‡Constructive Design of a Hierarchy of Semantics of a Transition System by Abstract Interpretation.pdf](file-service://file-N8TYcGD5rqy8tqm8B1mLUe):
\[
\tau^{\infty} = \tau^+ \cup \tau^\omega
\]
where
\begin{align*}
  \tau^+ &= \bigcup_{n > 0} \{ \sigma \in \Sigma^n \mid \forall i<n-1, \ \sigma_i \to \sigma_{i+1}, \ \sigma_{n-1} \in F \}, \\
  \tau^\omega &= \{ \sigma \in \Sigma^\omega \mid \forall i \in \mathbb{N}, \ \sigma_i \to \sigma_{i+1} \}.
\end{align*}
This semantics captures both terminating executions (finite maximal traces) and non-terminating executions (infinite traces).

\subsection{Fixpoint Characterization}
It can be equivalently defined as a fixpoint of a monotone transformer $F$ on the powerset of traces, using Kleene iteration [oai_citation:5‡Constructive Design of a Hierarchy of Semantics of a Transition System by Abstract Interpretation.pdf](file-service://file-N8TYcGD5rqy8tqm8B1mLUe).

\section{Finite Prefix and Collecting Semantics}
\subsection{Finite Prefix Semantics}
Finite prefix semantics collects all finite execution prefixes:
\[
T_p(I) = \{ \sigma_0, \ldots, \sigma_n \mid \sigma_0 \in I, \forall i, \ \sigma_i \to \sigma_{i+1} \}.
\]
It corresponds to the least fixpoint:
\[
T_p(I) = \text{lfp} \ (T \mapsto I \cup (T \concat \tau)).
\]

\subsection{Collecting Semantics}
The \emph{collecting semantics} gathers all traces of a program:
\[
\text{Col}(P) = J P K \subseteq \Sigma^*.
\]
It serves as the strongest property, against which verification reduces to inclusion checking [oai_citation:6‡02-sem_ok.pdf](file-service://file-GQRDJE8RnXagke3qcT17Dq).

\section{Relational Semantics}
Relational semantics abstract away the ordering of states and represent executions as input-output pairs. Examples include:
\begin{itemize}
  \item Angelic semantics (Hoare-style): favors successful termination paths,
  \item Demonic semantics (Smyth-style): assumes worst-case nondeterminism,
  \item Natural semantics (Plotkin-style): inductive big-step relation between initial and final states [oai_citation:7‡Constructive Design of a Hierarchy of Semantics of a Transition System by Abstract Interpretation.pdf](file-service://file-N8TYcGD5rqy8tqm8B1mLUe).
\end{itemize}

\section{Denotational and Predicate Transformer Semantics}
\subsection{Denotational Semantics}
Denotational semantics maps programs to mathematical functions over domains. 
It arises from abstracting traces into functional mappings, e.g. Smyth, Egli-Milner, Hoare powerdomains [oai_citation:8‡Constructive Design of a Hierarchy of Semantics of a Transition System by Abstract Interpretation.pdf](file-service://file-N8TYcGD5rqy8tqm8B1mLUe).

\subsection{Predicate Transformer Semantics}
Predicate transformer semantics (Dijkstra) relate postconditions to weakest preconditions:
\[
\text{wp}(C, Q) = \{ s \in \Sigma \mid \text{all executions of } C \text{ from } s \text{ terminate in } Q \}.
\]

\section{Reachability Semantics}
The \emph{state reachability semantics} abstracts away ordering of traces and keeps only reachable states:
\[
R(I) = \bigcup_{n \geq 0} \text{post}_\tau^n(I),
\]
where $\text{post}_\tau(S) = \{ \sigma' \mid \exists \sigma \in S: \sigma \to_\tau \sigma' \}$.
This is sufficient for proving safety properties [oai_citation:9‡02-sem_ok.pdf](file-service://file-GQRDJE8RnXagke3qcT17Dq).

\section{Abstract Interpretation and Semantic Hierarchy}
Each semantics can be derived from more concrete ones by abstraction. 
For instance:
\[
\text{Maximal Trace Semantics} \quad \Rightarrow \quad \text{Prefix/Collecting Semantics} \quad \Rightarrow \quad \text{Relational Semantics} \quad \Rightarrow \quad \text{Denotational / Predicate Transformer} \quad \Rightarrow \quad \text{Reachability Semantics}.
\]

\section{Hierarchy of Semantics (Graphical View)}
\begin{center}
\begin{tikzpicture}[node distance=1.5cm, every node/.style={draw, rectangle, rounded corners, align=center}]
  \node (mts) {Maximal Trace \\ Semantics};
  \node (fps) [below left=1.2cm and 2cm of mts] {Finite Prefix \\ Semantics};
  \node (cs)  [below right=1.2cm and 2cm of mts] {Collecting \\ Semantics};
  \node (rs)  [below=1.5cm of fps] {Relational \\ Semantics};
  \node (ds)  [below=1.5cm of cs] {Denotational \\ Semantics};
  \node (pts) [below=1.5cm of rs] {Predicate Transformer \\ Semantics};
  \node (reach) [below=1.5cm of pts] {Reachability \\ Semantics};

  \draw[-{Latex}] (mts) -- (fps);
  \draw[-{Latex}] (mts) -- (cs);
  \draw[-{Latex}] (fps) -- (rs);
  \draw[-{Latex}] (cs) -- (ds);
  \draw[-{Latex}] (rs) -- (pts);
  \draw[-{Latex}] (ds) -- (pts);
  \draw[-{Latex}] (pts) -- (reach);
\end{tikzpicture}
\end{center}

\section{Conclusion}
We have surveyed a hierarchy of semantics starting from maximal trace semantics down to reachable semantics. 
Through abstract interpretation, each semantics can be formally derived from a more concrete one, ensuring correctness-preserving abstraction. 
This unified view highlights how different semantics are suited for verifying different classes of program properties [oai_citation:10‡Constructive Design of a Hierarchy of Semantics of a Transition System by Abstract Interpretation.pdf](file-service://file-N8TYcGD5rqy8tqm8B1mLUe).

