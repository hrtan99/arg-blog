---
# 1) 新建一篇文章（会创建 posts/my-first-post/index.md）
# hugo new --kind post-bundle posts/my-first-post

# 2) 把配图放到同目录
# posts/my-first-post/
# ├─ index.md
# └─ cover.jpg  ← featuredImage 可填 "cover.jpg"

# 基本
title: "Demystifying Symbols in The Calculus of Computation"
subtitle: ""
date: 2024-08-10T12:07:11+08:00
lastmod: 2024-11-11T11:27:11+08:00
draft: true

# URL / SEO
slug: "Demystifying-Symbols-in-The-Calculus-of-Computation"
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


## abstract

In computer science—particularly logic, formal methods, programming languages and set‐theory—many symbols appear that can confuse readers: turnstiles, arrows, maps, leads, etc. This article gathers a comparative table of such symbols, gives their common pronunciation (in English and Chinese), explains their meaning in various literature contexts, and provides illustrative examples. The goal is to demystify these symbols and help the reader read and write formal texts more confidently.


## Comparative Table of Symbols

Formal notation in logic, set theory and programming language theory often uses special symbols that carry different subtle meanings.  For example, the symbols $\vdash$ (turnstile) and $\models$ (double turnstile) signify different consequence relations; in set theory and PL/FM we see arrows like $\to$, $\mapsto$, $\leadsto$ for functions, mappings or transitions.  Understanding these at a glance improves clarity when reading research papers, proofs or formal definitions.



| Symbol | Pronunciation (En / 中文) | Context / Usage | Illustrative Example |
|-------|---------------------------|-----------------|----------------------|
| `$\vdash$` | "turnstile", "yields" / “得出”, “推出” | Logic (proof theory, syntactic consequence — provability) | `$\Gamma \vdash \varphi$` means “$\varphi$ is derivable from assumptions $\Gamma$ in a proof system.” |
| `$\models$` | "models", "entails" / “语义蕴涵”, “满足” | Logic (model theory, semantic consequence — truth in all models) | `$\Gamma \models \varphi$` means “in every model where all formulas in $\Gamma$ hold, $\varphi$ also holds.”; `$\mathcal{M} \models \varphi$` means “model $\mathcal{M}$ satisfies $\varphi$.” |
| `$\to$` | "arrow", "maps to" / “映射到”, “到” | Set theory / PL type theory (total function) | `$f : A \to B$` means “$f$ is a (total) function from $A$ to $B$.” |
| `$\mapsto$` | "maps to", “映射为” | Element-wise function mapping notation | `$x \mapsto x^2$` describes how each $x$ in the domain is sent to $x^2$. |
| `$\leadsto$` | "leads to", “转化到”, “执行转移” | Operational semantics / transition systems | `$s \leadsto s'$` means “state $s$ transitions to state $s'$ in one (or a few) computation steps.” |
| `$\Rightarrow$` | "implies", “蕴含” | Logic (meta-level implication), type systems (big-step semantics) | `$\Gamma \Rightarrow \varphi$` means “$\varphi$ logically follows (meta-theoretically) from $\Gamma$.”; in big-step semantics: `$\langle c, s \rangle \Rightarrow s'$`. |
| `$\circlearrowright$` / `$\circlearrowleft$` | "loop arrow", “回路箭头”, “自环” | CFG, automata, state transition diagrams, control flow loops | A loop edge: `$q \circlearrowright$` indicates state `$q$` transitions back to itself (e.g., a loop in a CFG). |
| `$\rightharpoonup$` (`\rightharpoonup`) | "partial mapping", “偏函数” | Set theory / semantics (function not defined on every input) | `$f : A \rightharpoonup B$` means “$f$ is a *partial* function from $A$ to $B$ (not necessarily defined for all $A$).” |
| `$\hookrightarrow$` | "hook‐right arrow", “钩向右箭头” / “右挂箭头” | Category theory / Algebra / Set theory (injective embedding or inclusion) | `$f : A \hookrightarrow B$` means “$f$ is an embedding (injective homomorphism) of $A$ into $B$.” |
| `$\hookleftarrow$`  | "hook‐left arrow", “钩向左箭头” / “左挂箭头”  | Algebra / Module theory (dual embedding or restriction)                     | `$g : B \hookleftarrow A$` sometimes used to denote “$A$ embeds into $B$ via $g$ (viewed as inclusion from the right).” |


## Detailed Notes and Remarks

### Logic: $\vdash$ vs $\models$

The single turnstile symbol $\vdash$ (pronounced “turnstile” or “yields”) is traditionally used in proof theory to express syntactic derivability: i.e. from a set of premises or axioms one can derive a conclusion.
In contrast, the double turnstile symbol $\models$ (pronounced “models”, “semantic entailment”) is used in model theory to express semantic consequence: that in every model in which the premises are true, so is the conclusion. 
It is important to distinguish these two: one is about formal proofs (syntax), the other about truth in structures (semantics).

### Set Theory and PL/FM: $\to$ vs $\mapsto$

In set theory and programming‐language theory, the arrow $\to$ is used to denote a function type or a mapping between sets—i.e. $f:A \to B$ means $f$ maps the whole set $A$ into set $B$.  
The arrow with tail $\mapsto$ is more specific: it describes what *each element* does—i.e. how an element of the domain is mapped to an element of the codomain. 
For example: 
\[
f:\mathbb R \to \mathbb R,\quad x \mapsto x^2
\]
means “$f$ is a function from reals to reals, and each real $x$ is mapped to $x^2$”.

### Operational / Semantic Arrows: $\leadsto$

In programming‐language semantics, a symbol like $\leadsto$ (pronounced “leads to” / “导向”) often denotes a transition relation—one configuration leads to another.  
For instance, in an operational semantics of an imperative language we might write:
\[
\langle s,\,c\rangle \;\leadsto\; \langle s',\,c'\rangle
\]
meaning “starting in state $s$ executing command $c$ leads to state $s'$ and command $c'$”.


