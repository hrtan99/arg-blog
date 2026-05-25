---
# 1) 新建一篇文章（会创建 posts/my-first-post/index.md）
# hugo new --kind post-bundle posts/my-first-post

# 2) 把配图放到同目录
# posts/my-first-post/
# ├─ index.md
# └─ cover.jpg  ← featuredImage 可填 "cover.jpg"

# 基本
title: "Adequacy and Full Abstraction"
subtitle: ""
date: 2025-08-10T12:07:11+08:00
lastmod: 2025-11-11T11:27:11+08:00
draft: false

# URL / SEO
slug: "Adequacy-and-Full-Abstraction"
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



# Adequacy and Full Abstraction (PL Theory)

In programming language theory, we often relate:

- A source language \(S\)  
- A target language or model \(T\)

together with notions of program equivalence and a translation between them.  
The notions of **Adequacy** and **Full Abstraction** characterize how well this translation relates the two worlds.

---

## Basic Setting

Let \(S\) and \(T\) be two programming languages (or models of programming languages).  
Let \(\approx_S\) and \(\approx_T\) be two notions of equivalence of programs for each of these languages respectively.  
Let \([\![ - ]\!] \) be a translation from \(S\) to \(T\).

---

# Adequacy

A translation \([\![ - ]\!] : S \to T\) is **adequate** (for \(\approx_S, \approx_T\)) if for any two \(S\)-programs \(P, Q\):

\[
P \approx_S Q \;\Longleftarrow\; [\![ P ]\!] \approx_T [\![ Q ]\!]
\]

In other words:

> Programs that are equivalent after translation were already equivalent.

The point of adequate translations is that \(T\) is often significantly easier to reason about than \(S\). It is thus simpler to translate programs to \(T\), prove them equivalent there, and then use adequacy to “lift” that into an equivalence between the original programs.

Often, \(T\) is not an actual language, but a mathematical model for the language — for example a denotational semantics.

The notion of adequacy was introduced by Robin Milner in a paper presented at the 1973 Logic Colloquium in Bristol.

---

# Full Abstraction

A translation \([\![ - ]\!] : S \to T\) is **fully abstract** (with respect to \(\approx_S\) and \(\approx_T\)) if for any two \(S\)-programs \(P, Q\):

\[
P \approx_S Q \;\Longleftrightarrow\; [\![ P ]\!] \approx_T [\![ Q ]\!]
\]

In other words:

> A translation between programming languages is fully abstract iff it preserves and reflects program equivalence.

This consists of two directions:

1. **Equivalent programs are translated to equivalent programs**  
   \[
   P \approx_S Q \Rightarrow [\![ P ]\!] \approx_T [\![ Q ]\!]
   \]

2. **Programs that are equivalent after translation were already equivalent (a.k.a. adequacy)**  
   \[
   [\![ P ]\!] \approx_T [\![ Q ]\!] \Rightarrow P \approx_S Q
   \]

Surprisingly, the second point is more often true, and in fact easier to prove — even though it looks like a sort of completeness.

---

## In Denotational Semantics

The above definition is intentionally informal, in order to cover more recent uses of the notion of full abstraction. However, the origin of the term comes from the case where \(T\) is not a programming language at all, but rather a denotational semantics of the source language \(S\). In that case, the equivalence \(\approx_T\) is mathematical equality (in the appropriate metatheory, e.g. ZFC, HOL, or type theory).

---

## References

Milner, Robin. *Processes: A Mathematical Model of Computing Agents*. In **Logic Colloquium ’73**, Studies in Logic and the Foundations of Mathematics 80:157–173. Elsevier, 1975. https://doi.org/10.1016/S0049-237X(08)71948-7

Ong, C.-H. L. “Correspondence between Operational and Denotational Semantics: The Full Abstraction Problem for PCF.” In *Handbook of Logic in Computer Science*, Vol. 4, edited by S. Abramsky, D. M. Gabbay, and T. S. E. Maibaum. Oxford University Press, 1995.