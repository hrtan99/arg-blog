---
# 1) 新建一篇文章（会创建 posts/my-first-post/index.md）
# hugo new --kind post-bundle posts/my-first-post

# 2) 把配图放到同目录
# posts/my-first-post/
# ├─ index.md
# └─ cover.jpg  ← featuredImage 可填 "cover.jpg"

# 基本
title: "Notes on PL 01: Introduction to Semantics"
subtitle: ""
date: 2024-08-26
lastmod:
draft: false

# URL / SEO
slug: "Notes-on-PL-01-Introduction-to-Semantics"
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



What is the meaning of a program? When we write a program, we represent it using sequences of characters. But these strings are just *concrete syntax*—they do not tell us what the program actually means. It is tempting to define meaning by executing programs--either using an interpreter or a compiler. But interpreters and compilers often have bugs! We could look in a specification manual.

But such manuals typically only offer an informal description of language constructs. A better way to define meaning is to develop a formal, mathematical definition of the semantics of the language. This approach is unambiguous, concise, and most importantly, it makes it possible to develop rigorous proofs about properties of interest. The main drawback is that the semantics itself can be quite complicated, especially if one attempts to model all of the features of a full-blown modern programming language.

There are three pedigreed ways of defining the meaning, or semantics, of a language:

- *Operational semantics*: defines meaning in terms of execution on an abstract machine.

- *Denotational semantics*: defines meaning in terms of mathematical objects such as functions.

- *Axiomatic semantics*: defines meaning in terms of logical formulas satisfied during execution.

Each of these approaches has advantages and disadvantages in terms of how mathematically so phisticated they are, how easy they are to use in proofs, and how easy it is to use them to implement an interpreter or compiler. We will discuss these tradeoffs later in this course.

### Arithmetic Expression

To understand some of the key concepts of semantics, let us consider a very simple language of integer arithmetic expressions with variable assignment. A program in this language is an expression; executing a program means evaluating the expression to an integer. To describe the syntactic structure of this language we will use variables that range over the following domains:
$$
\begin{aligned}
x,y,z&\in\mathbf{Var}\\
n,m&\in\mathbf{Int}\\
e&\in\mathbf{Exp}
\end{aligned}
$$
$\textbf{Var}$ is the set of program variables (e.g., foo, bar, baz, i, etc.). $\textbf{Int}$ is the set of constant integers (e.g., 42, 40, 7). $\textbf{Exp}$ is the domain of expressions, which we specify using a BNF (Backus–Naur Form) grammar:
$$
\begin{aligned}e::=& \;x\\
|&\;n\\
|&\;e_1+e_2\\
|&\;e_1*e_2\\
|&\;x:= e_1\,;\,e_2
\end{aligned}
$$
Informally, the expression $x := e_1 \,;\, e_2$ means that $x$ is assigned the value of $e_1$ before evaluating $e_2$. The result of the entire expression is the value described by $e_2$.

This grammar specifies the syntax for the language. An immediate problem here is that the grammar is ambiguous. Consider the expression 1 + 2 * 3. One can build two abstract syntax trees:

```
       +                        *
      / \                      / \
     1   *                    +   3
        / \                  / \
       2   3                1   2
```

There are several ways to deal with this problem. One is to rewrite the grammar for the samelanguage to make it unambiguous. But that makes the grammar more complex and harder to understand. Another possibility is to extend the syntax to require parentheses around all addition and multiplication expressions:
$$
\begin{aligned}e::=& \;x\\
|&\;n\\
|&\;(e_1+e_2)\\
|&\;(e_1*e_2)\\
|&\;x:= e_1\,;\,e_2
\end{aligned}
$$
However, this also leads to unnecessary clutter and complexity. Instead, we separate the “concrete syntax” of the language (which specifies how to unambiguously parse a string into program phrases) from its “abstract syntax” (which describes, possibly ambiguously, the structure of program phrases). In this course we will assume that the abstract syntax tree is known. When writing expressions, we will occasionally use parenthesis to indicate the structure of the abstract syntax tree, but the parentheses are not part of the language itself. (For details on parsing, grammars, and ambiguity elimination, see or take CS 4120.)



#### Representing Expressions

The syntactic structure of expressions in this language can be compactly expressed in OCaml using datatypes:

```ocaml
type exp = Var of string
    | Int of int
    | Add of exp * exp
    | Mul of exp * exp
    | Assgn of string * exp * exp
```

This closely matches the BNF grammar above. The abstract syntax tree (AST) of an expression can be obtained by applying the datatype constructors in each case. For instance, the AST of expression 2 * (foo + 1) is:

```ocaml
Mul(Int(2), Add(Var("foo"), Int(1)))
```

In OCaml, parentheses can be dropped when there is one single argument, so the above expression can be written as:

```ocaml
Mul(Int 2, Add(Var "foo", Int 1))
```

We could express the same structure in a language like Java using a class hierarchy, although it would be a little more complicated:

```java
abstract class Expr { }
class Var extends Expr { String name; .. }
class Int extends Expr { int val; ... }
class Add extends Expr { Expr exp1, exp2; ... }
class Mul extends Expr { Expr exp1, exp2; ... }
class Assgn extends Expr { String var, Expr exp1, exp2; .. }
```

### Operational Semantics

We have an intuitive notion of what expressions mean. For example, the 7 + (4 * 2) evaluates to 15, and $i := 6 + 1 ; 2 * 3 * i$ evaluates to 42. In this section, we will formalize this intuition precisely.

An operational semantics describes how a program executes on an abstract machine. A small-step operational semantics describes how such an execution proceeds in terms of successive reductions--here, of an expression--until we reach a value that represents the result of the computation. The state of the abstract machine is often referred to as a configuration. For our language a configuration must include two pieces of information:

- a store (also known as environment or state), which maps variables to integer values. During program execution, we will refer to the store to determine the values associated with variables, and also update the store to reflect assignment of new values to variables,

- the expression to evaluate. 

We will represent stores as partial functions from Var to Int and configurations as pairs of expressions and stores:
$$
\begin{equation}
\begin{aligned}
\mathbf { Store } & \triangleq \mathbf { Var } \rightharpoonup \mathbf { Int } \\
\mathbf { Config } & \triangleq \mathbf { Store } \times \mathbf { Exp }
\end{aligned}
\end{equation}
$$
We will denote configurations using angle brackets. For instance, $\langle\sigma,(f o o+2) *(b a r+2)\rangle$ is a configuration where $\sigma$ is a store and $(f o o+2) *(b a r+2)$ is an expression that uses two variables, foo and bar. The small-step operational semantics for our language is a relation $\rightarrow \subseteq \textbf{Config} \times \textbf{Config}$ that describes how one configuration transitions to a new configuration. That is, the relation $\rightarrow$ shows us how to evaluate programs one step at a time. We use infix notation for the relation $\rightarrow$. That is, given any two configurations $\left\langle\sigma_1, e_1\right\rangle$ and $\left\langle\sigma_2, e_2\right\rangle$, if the pair $\left(\left\langle\sigma_1, e_1\right\rangle,\left\langle\sigma_2, e_2\right\rangle\right)$ is in the relation $\rightarrow$, then we write $\left\langle\sigma_1, e_1\right\rangle \rightarrow\left\langle\sigma_2, e_2\right\rangle$. For example, we have $\langle\sigma,(4+2) * y\rangle \rightarrow\langle\sigma, 6 * y\rangle$. That is, we can evaluate the configuration $\langle\sigma,(4+2) * y\rangle$ one step to get the configuration $\langle\sigma, 6 * y\rangle$.

Using this approach, defining the semantics of the language boils down to to defining the relation → that describes the transitions between configurations.

One issue here is that the domain of integers is infinite, as is the domain of expressions. Therefore, there is an infinite number of possible machine configurations, and an infinite number of possible single-step transitions. We need a finite way of describing an infinite set of possible transitions. We can compactly describe $\rightarrow$ using inference rules:
$$
\begin{aligned}
& \frac{n=\sigma(x)}{\langle\sigma, x\rangle \rightarrow\langle\sigma, n\rangle} \mathrm{Var} \\[2ex]
&\frac{\left\langle\sigma, e_1\right\rangle \rightarrow\left\langle\sigma^{\prime}, e_1^{\prime}\right\rangle}{\left\langle\sigma, e_1+e_2\right\rangle \rightarrow\left\langle\sigma^{\prime}, e_1^{\prime}+e_2\right\rangle} \mathrm{LAdd}\\[2ex]
& \frac{\left\langle\sigma, e_2\right\rangle \rightarrow\left\langle\sigma^{\prime}, e_2^{\prime}\right\rangle}{\left\langle\sigma, n+e_2\right\rangle \rightarrow\left\langle\sigma^{\prime}, n+e_2^{\prime}\right\rangle} \mathrm{RAdd} \\[2ex]
&\frac{\left\langle\sigma, e_1\right\rangle \rightarrow\left\langle\sigma^{\prime}, e_1^{\prime}\right\rangle}{\left\langle\sigma, e_1 * e_2\right\rangle \rightarrow\left\langle\sigma^{\prime}, e_1^{\prime} * e_2\right\rangle} \text { LMul } \\[2ex]
& \frac{\left\langle\sigma, e_2\right\rangle \rightarrow\left\langle\sigma^{\prime}, e_2^{\prime}\right\rangle}{\left\langle\sigma, n * e_2\right\rangle \rightarrow\left\langle\sigma^{\prime}, n * e_2^{\prime}\right\rangle} \mathrm{RMul} \\[2ex]
&\frac{\left\langle\sigma, e_1\right\rangle \rightarrow\left\langle\sigma^{\prime}, e_1^{\prime}\right\rangle}{\left\langle\sigma, x:=e_1 ; e_2\right\rangle \rightarrow\left\langle\sigma^{\prime}, x:=e_1^{\prime} ; e_2\right\rangle} \text { Assgn1 } \\[2ex]
&\frac{p=m \times n}{\langle\sigma, m * n\rangle \rightarrow\langle\sigma, p\rangle} \mathrm{Mul} \\[2ex]
& \frac{\sigma^{\prime}=\sigma[x \mapsto n]}{\left\langle\sigma, x := n \,;\, e_2 \right\rangle \rightarrow\left\langle\sigma^{\prime}, e_2\right\rangle} \mathrm{Assgn}
\end{aligned}
$$
The meaning of an inference rule is that if the facts above the line holds, then the fact below the line holds. The fact above the line are called premises; the fact below the line is called the conclusion. The rules without premises are axioms; and the rules with premises are inductive rules. We use the notation $\sigma[x \mapsto n]$ for the store that maps the variable $x$ to integer $n$, and maps every other variable to whatever $\sigma$ maps it to. More explicitly, if $f$ is the function $\sigma[x \mapsto n]$, then we have:

$$
f(y)= \begin{cases}n & \text { if } y=x \\ \sigma(y) & \text { otherwise }\end{cases}
$$
Now let's see how we can use these rules. Suppose we want to evaluate the expression $(f o o+2) *(b a r+1)$ with a store $\sigma$ where $\sigma(f o o)=4$ and $\sigma(b a r)=3$. That is, we want to find the transition for the configuration $\langle\sigma,(f o o+2) *(b a r+1)\rangle$. For this, we look for a rule with this form of a configuration in the conclusion. By inspecting the rules, we find that the only rule that matches the form of our configuration is LMul, where $e_1=f o o+2$ and $e_2=b a r+1$ but $e_1^{\prime}$ is not yet known. We can instantiate LMul, replacing the metavariables $e_1$ and $e_2$ with appropriate expressions.

$$
\frac{\langle\sigma, f o o+2\rangle \rightarrow\left\langle e_1^{\prime}, \sigma\right\rangle}{\langle\sigma,(f o o+2) *(b a r+1)\rangle \rightarrow\left\langle\sigma, e_1^{\prime} *(b a r+1)\right\rangle} \text { LMul }
$$


Now we need to show that the premise actually holds and find out what $e_1^{\prime}$ is. We look for a rule whose conclusion matches $\langle\sigma, f o o+2\rangle \rightarrow\left\langle e_1^{\prime}, \sigma\right\rangle$. We find that LADD is the only matching rule:

$$
\frac{\langle\sigma, f o o\rangle \rightarrow\left\langle\sigma, e_1^{\prime \prime}\right\rangle}{\langle\sigma, f o o+2\rangle \rightarrow\left\langle\sigma, e_1^{\prime \prime}+2\right\rangle} \text { LAdd }
$$


We repeat this reasoning for $\langle\sigma, f o o\rangle \rightarrow\left\langle\sigma, e_1^{\prime \prime}\right\rangle$ and find that the only applicable rule is the axiom VAR:

$$
\frac{\sigma(f o o)=4}{\langle\sigma, f o o\rangle \rightarrow\langle\sigma, 4\rangle} \mathrm{Var}
$$
Since this is an axiom and has no premises, there is nothing left to prove. Hence, $e_1^{\prime \prime}=4$ and $e_1^{\prime}=4+2$. We can put together the above pieces and build the following proof:

$$
\begin{gathered}
\frac{
\frac
{
\frac
{\sigma(foo)=4}
{\langle\sigma, f o o\rangle \rightarrow\langle\sigma, 4\rangle}\mathrm{VAR}
}
{
\langle\sigma, f o o+2\rangle \rightarrow\langle\sigma, 4+2\rangle
} \text { LAdd }
}
{\langle\sigma,(f o o+2) *(b a r+1)\rangle \rightarrow\langle\sigma,(4+2) *(b a r+1)\rangle
}\text { LMuL } 
\end{gathered}
$$


This proves that, given our inference rules, the one-step transition

$$
\langle\sigma,(f o o+2) *(b a r+1)\rangle \rightarrow\langle\sigma,(4+2) *(b a r+1)\rangle
$$

is derivable. The structure above is called a "proof tree" or "derivation". It is important to keep in mind that proof trees must be finite for the conclusion to be valid.

We can use a similar reasoning to find out the next evaluation step:

$$
\frac{\frac{6=4+2}{\langle\sigma, 4+2\rangle \rightarrow\langle\sigma, 6\rangle} \text { Add }}{\langle\sigma,(4+2) *(b a r+1)\rangle \rightarrow\langle\sigma, 6 *(b a r+1)\rangle} \text { LMul }
$$


And we can continue this process. At the end, we can put together all of these transitions, to get a view of the entire computation:

$$
\begin{aligned}
\langle\sigma,(f o o+2) *(b a r+1)\rangle & \rightarrow\langle\sigma,(4+2) *(b a r+1)\rangle \\
& \rightarrow\langle\sigma, 6 *(b a r+1)\rangle \\
& \rightarrow\langle\sigma, 6 *(3+1)\rangle \\
& \rightarrow\langle\sigma, 6 * 4\rangle \\
& \rightarrow\langle\sigma, 24\rangle
\end{aligned}
$$


The result of the computation is a number, 24 . The machine configuration that contains the final result is the point where the evaluation stops; they are called *final configurations*. For our language of expressions, the final configurations are of the form $\langle\sigma, n\rangle$.

We write $\rightarrow^*$ for the reflexive and transitive closure of the relation $\rightarrow$. That is, if $\langle\sigma, e\rangle \rightarrow$ ${ }^*\left\langle\sigma^{\prime}, e^{\prime}\right\rangle$ using zero or more steps, we can evaluate the configuration $\langle\sigma, e\rangle$ to $\left\langle\sigma^{\prime}, e^{\prime}\right\rangle$. Thus, we have:

$$
\langle\sigma,(f o o+2) *(b a r+1)\rangle \rightarrow^*\langle\sigma, 24\rangle
$$