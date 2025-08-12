---
# 1) 新建一篇文章（会创建 posts/my-first-post/index.md）
# hugo new --kind post-bundle posts/my-first-post

# 2) 把配图放到同目录
# posts/my-first-post/
# ├─ index.md
# └─ cover.jpg  ← featuredImage 可填 "cover.jpg"

# 基本
title: "Notes on PL: Lambda Calculus Encodings"
subtitle: ""
date: 2025-01-31T22:00:00
lastmod: 2025-01-31T22:00:00
draft: false

# URL / SEO
slug: "Notes-on-PL-Lambda-Calculus-Encodings"
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


### Lambda Calculus Encodings

#### Booleans

The pure \(\lambda\)-calculus contains only functions as values. It is not exactly easy to write large or interesting programs in the pure \(\lambda\)-calculus. We can however encode objects, such as booleans, and integers.

Let us start by encoding constants and operators for booleans. That is, we want to define functions TRUE, FALSE, AND, NOT, IF, and other operators that behave as expected. For example:

$$
\begin{aligned}
\texttt{AND~TRUE~FALSE}&=\texttt{FALSE}\\
\texttt{NOT~FALSE}&=\mathrm{TRUE}\\
\texttt{IF~TRUE~}e_1e_2&=e_1\\
\texttt{IF~FALSE~}e_1e_2&=e_2
\end{aligned}
$$

Let’s start by defining TRUE and FALSE:
$$
\begin{aligned}
\texttt{TRUE}&\triangleq\lambda x.\lambda y.x\\
\texttt{FALSE}&\triangleq\lambda x.\lambda y.y
\end{aligned}
$$
Thus, both TRUE and FALSE are functions that take two arguments; TRUE returns the first, and FALSE returns the second. We want the function IF to behave like
$$
\lambda b.\lambda t.\lambda f.\texttt{if~}b=\mathtt{TRUE~then~}t\mathtt{~else~}f.
$$
The definitions for TRUE and FALSE make this very easy.
$$
\mathtt{IF}\triangleq\lambda b.\lambda t.\lambda f.btf
$$
Definitions of other operators are also straightforward.
$$
\begin{aligned}
\texttt{NOT~}\triangleq&\lambda b.b\texttt{~FALSE~TRUE}\\
\texttt{AND~}\triangleq&\lambda b_1.\lambda b_2.b_1b_2\texttt{~FALSE}\\
\texttt{OR~}\triangleq&\lambda b_1.\lambda b_2.b_1\texttt{~TRUE~}b_2
\end{aligned}
$$

#### Church numerals

Church numerals encode a number $n$ as a function that takes $f$ and $x$, and applies $f$ to $x$ $n$ times.
$$
\begin{aligned}
\overline{0}&\triangleq\lambda f.\lambda x.x\\
\overline{1}&\triangleq\lambda f.\lambda x.fx\\
\overline{2}&\triangleq\lambda f.\lambda x.f(fx)\\\mathtt{SUCC}&\triangleq\lambda n.\lambda f.\lambda x.f(nfx)\end{aligned}
$$
In the definition for SUCC, the expression $nfx$ applies $f$ to $x$ $n$ times (assuming that variable $n$ is the Church encoding of the natural number $n$). We then apply $f$ to the result, meaning that we apply $f$ to $x$ $n + 1$ times.