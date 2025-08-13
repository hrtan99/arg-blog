---
# 1) 新建一篇文章（会创建 posts/my-first-post/index.md）
# hugo new --kind post-bundle posts/my-first-post

# 2) 把配图放到同目录
# posts/my-first-post/
# ├─ index.md
# └─ cover.jpg  ← featuredImage 可填 "cover.jpg"

# 基本
title: "Notes on Algorithm Complexity"
subtitle: ""
date: 2019-01-19 
lastmod: 
draft: false

# URL / SEO
slug: "Notes-on-Algorithm-Complexity"
aliases: []
description: ""
# 若你用自定义永久链接，可在 config 里用 :slug
# [permalinks]
# posts = "/blog/:year/:slug/"

# 分类
categories: ["Computer Science"]
tags: ["Algorithm"]
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


算法是计算机科学中最重要的部分，它们可以通过使用与语言和机器无关的方式进行研究。这意味着我们需要一种技术，使我们能够在不实现算法的情况下比较算法的效率。在算法分析中，两个最重要的工具是

1. 计算的RAM模型。
2. 最坏情况下复杂性的渐近分析。

本文将讨论算法的效率分析，以及常用的分析工具和方法。


<!--more-->

## Prerequisite

为了理解本文所写的内容，你需要掌握一些基本的高等数学知识。

- 极限的定义。
- 求极限的洛必达法则。
- 级数的定义与相关概念。



## 算法的效率

一个算法主要有两种效率：时间效率和空间效率。时间效率，也称为时间复杂度，对应算法运行的速度。空间效率，也称为空间复杂度，是指算法根据其输入和输出所需的额外空间，或者说所需的内存单元数量。在计算机出现的早期，时间和空间等资源都是十分昂贵的。

半个多世纪依赖的技术创新使计算机的速度和内存大小提高了几个数量级。现在，算法所要求的额外空间通常不是人们关注的焦点。然而，算法时间效率重要性并不像空间那样降低。反而对于大多数问题，我们可以在时间上取得比在空间方面取得更令人满意的提升。因此，本文中，我们主要专注于算法的时间效率，当然但这里所提到的分析框架也适用于空间效率分析。



### 衡量输入数据的大小

在讨论算法的效率之前，我们首先要明白，算法的效率会受到输入数据的影响。

几乎所有算法在较大的输入数据上运行的时间都更长。例如，对较大的数组进行排序、较大的矩阵乘法等都需要更长的时间。因此，选择一个合适的参数$n$来指示算法输入的大小，对于研究算法的效率是非常必要的。在大多数情况下，这个参数的选择非常简单。例如，对于排序、搜索、查找列表最小元素以及处理列表的大多数其他问题，$n$可以是列表的大小。有时，参数不止一个。比如图算法中的顶点数$v$和边数$e$。

在某些情况下，指示输入大小的参数的选择很重要。一个例子是计算两个$n \times n$矩阵的乘积。此时对于输入的大小有两个选择，第一个是更常用的是矩阵维度$n$。另一个是矩阵中元素的总数$N$（后者更笼统，因为它适用于非方阵）。虽然这两个参数可以相互转换，但算法效率的计算结果将因我们使用这两种参数中的哪一种而异。

参数选择可能会受到算法的影响。比如，我们应该如何衡量拼写检查算法的输入大小？如果算法只是检查输每次入的单个字符，我们应该用字符集来衡量大小；如果算法通过处理单词来工作，那么我们应该在计算输入字符的数量。

对于解决诸如检查正整数$n$的素性等问题的算法，输入数据的大小又是另一种情况。这里的输入只是一个数字，正是这个数字的大小决定了输入的大小。在这种情况下，通常应当用$n$的二进制表示中的位数$b$来衡量输入大小。
$$
b = \lfloor \log_2 n \rfloor + 1
$$

### 运行时间与RAM模型

下一个问题涉及测量算法运行时间的单位。当然我们可以直接使用一些标准的时间测量单位，如秒或毫秒等来测量算法的运行时间。然而，这种方法有明显的缺点。

- 依赖于特定计算机的速度。
- 依赖于实现算法的程序和用于生成机器代码的编译器的质量
- 以及难以计时程序的实际运行时间。

考虑到这些问题，我们希望有一个不依赖于这些无关因素的指标。

如果我们想分析算法的运行时间，而又不通过具体的代码进行实验，我们可以直接对算法的**伪代码（pseudo code）**采取一种更具分析性的方法。

首先我们定义一组高级原始操作，这些操作并不依赖于实现算法时所使用的具体编程语言，也不依赖于任何特定的机器。仅仅在伪代码中使用，用于描述算法的各种操作。

- 为变量分配值。
- 调用方法。
- 执行算术操作。
- 比较两个数字。
- 索引数组。
- 对象引用。
- 从方法中返回。

具体而言，这些原始操作对应到特定机器上的特定低级指令，它们的执行时间取决于硬件和软件环境。我们并不是要确定每个原始操作的具体执行时间，而只是简单地计算算法执行的原始操作数，记为$C$，并将它作为算法运行时间的一个高层次估计。

计算算法的基本操作次数时，我们通常还要考虑输入的影响。如果在大小为$n$的输入上执行算法，那么执行次数记为$C(n)$。假设$t_{op}$是每个原始操作需要花费的时间，那么算法在特定机器上的运行时间$T(n)$就可以用以下公式表示。
$$
T(n) \approx t_{op} \cdot C(n)
$$
运行时间$T(n)$将与算法在特定的硬件和软件环境中的实际运行时间相关。因为每个原始操作在不同的机器上都将有不同的恒定执行时间$t_{op}$，并且根据算法的确定性，每一个算法只会执行固定数量的原始操作$C(n)$。注意，这里有一个隐含的假设，在一台机器上，不同原始操作的执行时间应当近似。如此一来，算法执行的原始操作数$C(n)$将与该算法的实际运行时间成正比。

利用这个公式，我们可以合理估计算法的运行时间。比如，它可以帮助我们可以回答以下问题：

1. 该算法在比快10倍的机器上运行的速度提升是多少倍？答案显然是10倍。

2. 假设$C(n)=\frac12 n(n-1)$，如果输入数据大小翻一番，算法将运行多久？答案是时间大约变为了四倍。

实际上，当$n$很大时，总有

$$
C(n) = \frac 12n(n-1) \approx \frac12 n^2
$$
因此
$$
\frac{T(2n)}{T(n)} = \frac{t_{op} \cdot C(2n)}{t_{op} \cdot C(n)} \approx \frac{ \frac12(2n)^2 }{\frac12 n^2} = 4
$$

这种简单计算原始操作的方法称为 {% label 随机访问机 purple %}（Random Access Machine）计算模型。“随机访问”一词是指CPU拥有通过一个原始操作访问任意存储单元的能力。算法执行的原始操作数的确界直接反映了该算法在RAM模型中的运行时间。RAM模型还具有以下假设。

- 每一个原始操作仅需一个时间步$t_{op}$。
- 循环和子程序不应被视为简单的原始操作，而是由许多原始操作组成。显然，对100万个数字进行排序肯定比对10个数字进行排序所花费的时间多得多。循环或执行子程序所需的时间取决于循环迭代的次数和子程序的功能特性。
- 每次内存访问只需一个时间步。此外，我们假设拥有足够多的内存。RAM模型并不考虑数据是在主存中还是在磁盘上。

RAM是衡量计算性能的简单模型。也许太简单了。毕竟，在大多数处理器上执行乘法要比执行加法花费更多的时间，这违反了RAM模型的第一个假设；一些高级编译器对循环的展开优化和超线程技术很可能会违背第二个假设；而内存访问时间又因数据是位于缓存中还是磁盘上相去甚远。尽管如此，RAM仍然是了解算法在真实计算机上如何运行的绝佳模型。RAM模型的鲁棒性使我们能够以机器、语言无关的方式分析算法。



### 最好情况、最坏情况、平均情况

虽然时空效率都以算法输入数据大小的函数来衡量，对于相同大小的输入，一些算法的效率可能会有很大差异。对于此类算法，我们需要区分最坏情况、平均情况和最坏情况的效率。

接下来考虑一个简单的顺序查找算法。在一个向量中搜寻一个目标值。若查找成功返回它的下标，查找失败则返回-1。

```c++
int search(vector<int> & A, int target) {
  int i = 0;
  while(i < A.size()) {
    if (A[i++] == target) return i;
  }
  return -1;
}
```

#### 最坏情况

显然，对具有相同规模$n$的不同向量，该算法的运行时间可能大不相同。在最坏的情况下，当没有匹配元素或匹配元素恰好在向量的末尾时，该算法的比较次数将是所有大小为$n$的可能输入中最多的：$C_{worst}(n) = n$。

确定算法最坏情况下的效率的方法很简单：分析算法，看看哪种输入在所有可能的输入中能够使基本操作计数$C(n)$达到最大值，然后计算这个最坏情况下$C_{worst}(n)$的值。显然，最坏情况分析确定了算法的运行时间上界（Upper Bound），提供了关于算法效率的非常重要的信息。它保证对于任何大小为$n$的输入实例，运行时间不会超过$C_{worst}(n)$。因此，我们的算法分析主要关注最坏情况下的效率分析。



#### 最好情况

算法的最佳效率是在输入其大小为$n$时，该算法在所有可能输入中运行最快的情况。换言之，最好情况分析确定了一个算法运行时间的下界（Lower Bound）。我们可以对最好情况效率进行如下分析。首先，确定在所有可能的输入中，使得计数$C(n)$最小的输入类型，然后计算这个最好输入情况下的$C(n)$值。例如，顺序搜索的最佳情况是第一个元素就是我们要找的目标元素，此时，算法的$C_{best} (n) = 1$。

最佳情况效率的分析远不如最坏情况效率的分析重要。但它也并不是完全无用。虽然我们不应该期望能够获得最佳情况下的输入，但对于某些算法，最佳情况下的性能可以帮助我们了解到一些接近最佳情况的输入的性能。

例如，对于插入排序算法，最佳情况下的输入是已有序的向量，那么该算法运行得非常快。而对于几乎已经有序的数组，最佳情况效率只会略微降低。因此，这种算法很可能是处理几乎已经有序数组的应用程序的首选方法。

最佳情况效率分析的另一个用处是，如果一个算法连最佳情况下的效率都无法令人满意，我们可以立即放弃它，无需再进一步分析。



#### 平均情况

然而，无论是最坏情况分析还是最佳情况分析，都不能提供关于“随机”输入情况下的算法效率信息。平均情况分析可以做到这一点。为了分析算法的平均情况下的效率，我们首先必须对大小为$n$的所有可能输入做出一些假设。

回到顺序搜索算法。我们提出两个概率假设。

1. 搜索成功的概率等于$p \;(0 \leqslant p \leqslant 1)$ 

2. 第一次匹配成功发生在列表第$i$个位置的概率对于所有$i$都是相同的。

在上述假设下（假设的有效性通常很难验证），我们可以计算出比较的平均次数$C_{avg}(n)$，如下所示。在成功搜索的情况下，在向量的第$i$个位置匹配成功的概率为$p \cdot \frac 1n = \frac pn$，此时进行的比较的次数为$i$。在搜索失败的情况下，比较的次数为$n$，概率为$1− p$ 。因此$C(n)$的期望计算如下。
$$
\begin{align}
C_{avg}(n) &= \left( 1 \cdot \frac pn + 2 \cdot \frac pn + \cdots + n \cdot \frac pn  \right) + n \cdot (1-p) \\[2ex]
 &= \frac pn \cdot \frac {n(n+1)}{2} + n \cdot (1-p) \\[2ex]
 &=   \frac {p(n+1)}{2}+ n \cdot (1-p)  \\[2ex]
\end{align}
$$
根据这个期望的公式，我们可以得出一些相当合理的结论。比如，如果$p=1$（搜索一定成功），则通过顺序搜索进行的关键比较的平均次数为$(n+1)/2$，也就是说，该算法将平均搜索大约一半的元素。如果$p=0$（搜索一定失败），则比较次数的期望将为$n$，因为算法将搜索所有的元素。

显然，分析平均情况下的效率比分析最坏情况和最佳情况的效率要困难得多。因为这样的分析往往需要大量的数学和概率知识，并且还需要我们事先知道输入数据所服从的概率分布。



![三种情况下算法效率增长示意图](http://blog-cdn.arg.pub/count.svg)

#### 分摊效率

还有一种效率称为分摊效率。它并不适用于算法单次运行，而是适用于在同一数据结构上执行的一系列操作。在某些情况下，一次操作的代价可能会很昂贵，但是，连续进行$n$次这样的操作的总时间总是比单次操作的时间乘以$n$要少得多。因此，我们可以在整个操作序列中“摊销”单次操作的高成本。这种复杂的方法是由美国计算机科学家罗伯特·塔扬（Robert Tarjan）发现的，他在研究一些经典二叉搜索树的变体，如伸展树的连续插入操作时使用了这种方法。

具体的分摊分析方法并不是本文讨论的重点。感兴趣的话可以参考M. T. Goodrich的著作^[7]^。



### 小结

- 时空效率都以算法输入大小的函数来衡量。

- 时间效率是通过计算算法基本操作的执行次数来衡量的。空间效率则是通过计算算法消耗的额外内存单元数量来衡量。

- 对于相同大小的输入，一些算法的效率可能会有很大差异。对于此类算法，我们需要区分最坏情况、平均情况和最好情况的效率。

- 我们主要关注随着输入大小不断增加，算法运行时间（消耗的额外内存单元）的增长速度。







## 函数的增长性

我们注意到，实际上完全可以在不知道实际$t_{op}$的值的情况下回答上文提出的关于[运行时间](# 运行时间与RAM模型)的第二个问题，因为它已经被抵消了。另外，计数$C(n)$公式中的常数乘法因子$\frac12$也被抵消了。因此，我们在算法分析中常常忽略这些常数因子，**随着输入大小$n$的不断增长，对运行时间起主导作用的主要是函数$C(n)$增长的阶。**

所以，接下来我们将进入数学的世界，讨论分析函数的增长性的一般方法。



### 渐进符号

如上一节所述，效率分析框架以算法基本操作数的增长顺序为中心，作为算法效率的主要指标。为了比较和排名这些增长顺序，计算机科学家借用了数学中的三个符号：$\mathcal O$、$\Omega$和$\Theta$。渐进一词则是来源于数学上函数的渐近线。

首先我们定义两种渐进函数

**定义 2.1.1** $\mathbb N \mapsto\mathbb R$（或$\mathbb R \mapsto\mathbb R$）中的函数$f$称为**渐近非负函数**，若存在$n_0 \in \mathbb N$，对任意的$n\geqslant n_0$，总有$f(n) \geqslant 0$。

**定义2.1.2** $\mathbb N \mapsto\mathbb R$（或$\mathbb R \mapsto\mathbb R$）中的函数$f$称为**渐近正函数**，若存在$n_0 \in \mathbb N$，对任意的$n \geqslant n_0$，总有$f(n) \gt 0$。

在下面的讨论中，$t(n)$、$f(n)$和$g(n)$可以是在自然数集上定义的任何函数。但在算法分析中，我们的计数函数总是正的，所以有关的函数都将加上绝对值。$t(n)$将表示算法的运行时间（通常与基本操作计数$C(n)$成正比），$g(n)$是一个用来和$t(n)$进行比较的简单函数。



#### Big-O Notation

直观上来讲，$\mathcal O(g(n))$是一个函数集合，集合中所有元素的增长速度小于等于$g(n)$的增长速度（当$n$趋向于无穷时，增速之比为常数倍）。因此，举几个例子，以下断言都是正确的：




> [!Note]
> **定义 2.2.1** $\mathcal O(g(n))$是一个函数集合，满足$\mathcal O(g(n))=\{f(n)\;|\;$存在实数$c>0$和正整数$n_0$，对任意的$n \geqslant n_0$，总有$|f(n)| \leqslant c\cdot |g(n)|\}$。若$t(n) \in \mathcal O (g(n))$，则称$t(n)$是$\mathcal O(g(n))$，记作$t(n) = \mathcal O(g(n))$。


尽管上述定义已经足够严谨，我们还是给出它的另一个等价定义。


> [!Note]
> **定义 2.2.2** $\mathcal O(g(n))$是一个函数集合，满足
> $$
> \mathcal O(g(n))=\{ f(n)\;|\;  h(n) = \left| \frac{f(n)}{g(n)}\right|,\;h(n)上有界 \}。
> $$
> 若$t(n) \in \mathcal O (g(n))$，则称$t(n)$是$\mathcal O(g(n))$，记作$t(n) = \mathcal O(g(n))$。

这个定义通常读作“$t(n)$是大$\mathcal O $ $g(n)$”或者“$t(n)$是$g(n)$阶”。大$\mathcal O$记号给我们提供了一个当$n$充分大时，$t(n)$的{% label 渐进意义下的上界 purple %}（Asymptotic Upper Bound）。

**例 2.1** $2n = \mathcal O(n)$。$2n = \mathcal O(n^2)$同样正确✅。我们通常希望表示的是更紧的上界。



#### Big-Omega Notation

第二个符号，$\Omega(g(n))$，表示与$g(n)$具有相同或更高增长速度的所有函数的集合（当$n$趋向于无穷时，增速之比为常数倍）。

{% note info no-icon %}

**定义 2.3.1** $\Omega(g(n))$是一个函数集合，满足$\Omega(g(n))=\{f(n)\;|\;$存在实数$c>0$和正整数$n_0$，对任意的$n \geqslant n_0$，总有$c\cdot |g(n)| \leqslant|f(n)|\}$。若$t(n) \in \Omega(g(n))$，则称$t(n)$是$\Omega(g(n))$，记作$t(n) = \Omega(g(n))$。

{% endnote %}

同样给出一个$\Omega(g(n))$集合的等价定义。

{% note info no-icon %}

**定义 2.3.2** $\Omega(g(n))$是一个函数集合，满足
$$
\Omega(g(n))=\{ f(n)\;|\;  h(n) = \left| \frac{g(n)}{f(n)}\right|,\;h(n)上有界 \}。
$$
若$t(n) \in \Omega (g(n))$，则称$t(n)$是$\Omega(g(n))$。记作$t(n) = \Omega(g(n))$。

{% endnote %}

也就是说，大$\Omega$记号与大$\mathcal O$记号表示的意义恰好相反。假设$t(n)$是$\mathcal O(g(n))$，当n充分大时，根据大$\mathcal O$记号的定义，有$|t(n)| \leqslant c\cdot |g(n)|$。我们将常数$c$除到左边，得到$\frac 1c \cdot |t(n)| \leqslant |g(n)|$，根据大$\Omega$记号的定义，这意味着$g(n)$是$\mathcal \Omega(t(n))$。

类似大$\mathcal O$记号的读法，大$\Omega$记号通常读作“$t(n)$是大$\Omega$ $g(n)$”。大$\Omega$记号表示当$n$充分大时，$t(n)$在{% label 渐进意义下的下界 purple %}（Asymptotic Lower Bound）。

**例 2.2** $3n+4 = \Omega(n)$。$3n+4 = \Omega(1)$同样正确✅。但请注意，我们通常希望表示的是更紧的下界。



#### Big-Theta Notation

最后一个记号，$\Theta(g(n))$也是一个函数的集合，集合中函数的增长速度与$g(n)$相同当$n$趋向于无穷时，增速之比为常数倍）。

{% note info no-icon %}

**定义 2.4.1** 我们称$t(n)$是$\Theta(g(n))$，当且仅当$t(n)$是$O(g(n))$且$t(n)$是$\Omega(g(n)$。记作$t(n) = \Theta(g(n))$。

{% endnote %}

实际上，$\Theta(g(n))$就是$\mathcal O(g(n))$和$\Omega(g(n))$的交集。并且我们仍然能够通过另一种方式判断$t(n)$是否是$\Theta(n)$。

{% note info no-icon %}

**定义 2.4.2** $\Theta(g(n))$是一个函数集合，满足
$$
\Theta(g(n))=\{ f(n)\;|\;  h(n) = \left| \frac{g(n)}{f(n)}\right|,\;h(n)有界,\;且\inf h(n)大于0 \}。
$$
若$t(n) \in \Theta(g(n))$，则称$t(n)$是$\mathcal \Theta(g(n))$。记作$t(n) = \Theta(g(n))$。

{% endnote %}

类似地，读作“$f(n)$是大$\Theta$ $g(n)$”。大$\Theta$记号允许我们说两个函数在渐近意义上是相等的，他们可以相差最高可达一个常数因子。我们考虑以下一些这些符号的例子。



#### Little-O Notation And Little-Omega Notation

除了上文提到的三种常用的渐进记号之外，还有两个不怎么常用的记号，分别是小$o$和小$\omega$。

我们知道，大$\mathcal O$记号是用于描述一个函数的渐进上界的，然而这个上界并不一定是严格的上界，可能是{% label 渐进紧 purple %}（Asymptotically Tight）的上界。比如，$2n^2+3=\mathcal O(n^2)$。当$n$趋向于无穷时，两个函数值之比为$2$。此时，$n^2$是一个渐进紧的上界，因为你不能再找到一个比$n^2$更小，而又渐进意义上大于等于$2n^2+3$的函数了。从数学上来说，他们是**同阶**的无穷大，增速相同。而$O(n^3)$就是一个严格的宽松的上界。

回忆一下我们在数学上使用的小$o$记号，$o(f(x))$表示的是$f(x)$的{% label 高阶无穷小 purple %}，也就是
$$
\lim_{x\to \infty} \frac{o(f(x))}{f(x)} = 0
$$
这里的$o(f(x))$表示的就是比$f(x)$严格的高阶的的无穷小。我们从数学中借用小$o$符号，得出以下定义。

{% note info no-icon %}

**定义 2.5** $o(g(n))$是一个函数集合，满足$o(g(n))=\{f(n) \;|\;$对**任意**的实数$c > 0$，**存在**整数$n_0$，当$n\geqslant n_0$时，总有$|f(n)|\leqslant c\cdot |g(n)|$。即
$$
\lim_{n\to \infty} \frac{|f(n)|}{|g(n)|} = 0
$$
{% endnote %}

注意，这里的$o(g(n))$表示的是所有比$g(n)$**低阶**的函数集合，也就是$g(n)$是一个严格的上界，或者说{% label 非渐进紧 purple %}（Non-Asymptotically Tight）的上界。比如，$3n+1=o(n^2)$而$3n+1\neq o(n)$。



**例 2.3** 讨论$\sqrt n$与$n^{\sin n}$的关系。

解：设$f(n) =\sqrt n$，$g(n)=n^{\sin n}$。
$$
\lim_{n\to \infty}  \left | \frac {f(n)}{g(n)}\right| = \lim_{n\to \infty}  \left | \frac {n^\frac 12}{n^{\sin n}}\right| = \lim_{n\to \infty} n^{\frac12 - \sin n}
$$
对任意实数$M$，总有$n_0 > M$且$n_0 = \frac { k \pi} 2$，使得${n_0}^{\frac12 - \sin {n_0}} \geqslant M$，即$n^{\frac12 - \sin n}$无界。故$\sqrt n \notin \mathcal O(n^{\sin n})$，从而$\sqrt n \notin o(n^{\sin n})$。



**练习 2.1** 试证明$\frac {1}{2+\lg n} = o(1)$。



**练习 2.2** 试证明$\frac{1}{1+\cos n} = \mathcal O(1)$而$\frac{1}{1+\cos n} \neq o(1)$。



类似小$o$记号，小$\omega$记号表示一个函数是严格的、非渐进紧的下界。

{% note info no-icon %}

**定义 2.6** $\omega(g(n))$是一个函数集合，满足$\omega(g(n))=\{f(n) \;|\;$对**任意**的实数$c > 0$，**存在**整数$n_0$，当$n\geqslant n_0$时，总有$c\cdot |g(n)|\leqslant|f(n)|$。即
$$
\lim_{n\to \infty} \frac{|g(n)|}{|f(n)|} = 0
$$
{% endnote %}



除此之外，还有一个不常用的符号$\sim$，表示两个函数相似。

{% note info no-icon %}

**定义 2.7** $f(n) \sim g(n)$当且仅当
$$
\lim_{n\to \infty} \frac{|f(n)|}{|g(n)|} = 1
$$
{% endnote %}

总结一下，$\mathcal O$和$o$提供了表达上限的方法（$o$是更严格的上界），$\sim$提供了一种表达渐近等价性的方法。



### 进一步使用渐进符号

有时，对于一些比较复杂的函数，我们只关心它的主要部分。比如$f(n) = 2n^3 - n^2 + 1$，我们只关心对$f(n)$的增长起到主导作用的$2n^3$。在引入了渐进符号后，我们便可以使用渐进符号简化该函数的表达式了。

假如我们使用小$o$记号，那么$f(n)$可以表示为：
$$
f(n) = 2n^3 + o(n^3)
$$
请不要忘记，小$o$记号表示的是严格的上界。如果我们想使用$-n^2+1$的渐进紧的上界，那么可以使用大$\mathcal O$记号，如此一来，$f(n)$就可以表示为：
$$
f(n) = 2n^3 + \mathcal O(n^2)
$$
{% note info no-icon %}

**定义 2.8 Big-Oh近似** 我们说 $f(n) = g(n) + \mathcal O(h(n))$，以表明我们可以通过计算 $f(n)$来近似$g(n)$，并且误差将以$h(n)$的常数倍为界。正如在大 $\mathcal O$ 符号的定义中提到的一样，所涉及的常数是不确定的，但我们通常假设它是不大合理的。如下文所述，我们通常使用 $h(n) = o(f(n))$ 的符号。

**定义 2.9 Little-Oh近似** 一个更强的表示是$g(n) = f(n) + o(h(n))$，同样表明我们可以通过计算$f(n)$来近似$g(n)$。但随着$n$的变大，误差与$h(n)$相比将变得越来越小。这个非特定函数与下降率有关，它在数值上永远不会很大。

**定义 2.10 Similarity近似** $g(N)\sim f(N)$用于表达最弱的非平凡$o$近似：$g(n) = f (n) + o(f (n))$。

{% endnote %}

**例 2.4** 试证明
$$
\frac{n}{n+1} = 1+ \mathcal O(\frac 1n),\;\frac{n}{n+1} \sim 1 - \frac 1n
$$



**练习 2.2** 试证明
$$
\binom{n}{r} = \frac{n!}{r!} + \mathcal O(n^{r-1})
$$


这些符号很有用，因为它们可以在不失数学严谨性和精确结果的情况下忽略函数中不重要的部分。

当涉及对数和指数时，我们应该认识到“指数差异”。例如，如果我们知道一个数量的值是$2n + \mathcal O(\log n)$，那么我们可以合理地确定，当$n$大到$1000$甚至$100,000,000$时，$\log n$在真实值的百分之几或几千分之一，那么我们不值得计算$\log n$的系数，甚至可以直接将其缩减到$\mathcal O(1)$以内。同样，$2^N + \mathcal O(n^2)$的渐近估计显而易见。

为了强调指数差异，如果一个函数$f(n)$小于$n$的任何负数次幂，即$f(n) \in O(n^{-m})$，那么我们称它是**指数级小（Exponentially Small）**的。典型的指数小的量是$e^{-n}$、$e^{-\log ^2 n}$和$\log n ^{-\log n}$（你能证明吗？）。



这种简便的表示方法确实方便了我们快速分辨$f(n)$的主要部分，由此引申出另一个问题，假如我们已知$f(n)$，如何判断这种简化表示的等式是否真的成立？比如，等式$\lg(2^n + n) = n + o(1)$是否成立？

{% note info no-icon %}

**定义 2.11** 我们记$f(n) = g(n) + o(h(n))$当且仅当$f(n) - g(n) = o(h(n))$。

{% endnote %}

所以。只需判断$\lg(2^n + n) -n = o(1)$是否正确即可。
$$
\lim_{n\to \infty} \frac{\lg(2^n + n) - n}{1} = \lim_{n\to \infty} \lg(2^n + n) - \lg 2^n = \lim_{n\to \infty} \lg \left(1+\frac{n}{2^n} \right )= 0
$$
故该等式成立。



### 渐进符号的运算性质

以下的运算性质都以大$\mathcal O$记号为例，但这些性质对我们已经引入的所有的渐进符号都成立。

{% note success no-icon %}

**定理 2.1**

1. **传递性** 若$f(n) = \mathcal O (g(n))$且$g(n) = \mathcal O(h(n))$，那么$\mathcal f(n) = \mathcal O(h(n))$。
2. **数乘** $c \cdot \mathcal O(f(n)) = \mathcal O(c\cdot f(n)) = \mathcal O(f(n))$
3. **乘法** $\mathcal O(f(n)) \cdot \mathcal O(g(n)) = \mathcal O(f(n)\cdot g(n))$
4. **加法** $\mathcal O(f(n)) + \mathcal O(g(n)) = \mathcal O(\max\{f(n), g(n)\})$

{% endnote %}

使用好这些运算性质，可以在很多时候帮助我们简化计算。

**例** 试计算调和级数$H_n$的平方的渐进函数，要求给出误差项。
$$
\begin{aligned}
\left(H_{N}\right)^{2}=&\left(\ln N+\gamma+ \mathcal O\left(\frac{1}{N}\right)\right)\left(\ln N+\gamma+\mathcal O\left(\frac{1}{N}\right)\right) \\
=&\left((\ln N)^{2}+\gamma \ln N+\mathcal O\left(\frac{\log N}{N}\right)\right) \\
&+\left(\gamma \ln N+\gamma^{2}+\mathcal O\left(\frac{1}{N}\right)\right) \\
&+\left(\mathcal O\left(\frac{\log N}{N}\right)+\mathcal O\left(\frac{1}{N}\right)+\mathcal O\left(\frac{1}{N^{2}}\right)\right) \\
=&(\ln N)^{2}+2 \gamma \ln N+\gamma^{2}+\mathcal O\left(\frac{\log N}{N}\right) .
\end{aligned}
$$





### 使用极限计算

虽然$\mathcal O$、$\Theta$和$\Omega$的正式定义对于证明其抽象性质不可或缺，但它们很少直接用于比较两个特定函数的增长速度。根据渐进符号的定义，我们发现一个更便捷的方法是利用极限作为判断两个函数增长顺序的公式。但请注意，两个函数比值的极限存在是个充分非必要条件。

{% note success no-icon %}

**定理 2.2**
$$
\lim_{n\to \infty} \frac{f(n)}{g(n)} =
\left\{\begin{array}{l}
0\implies f(n) \in \mathcal O (n)\\[2ex]
c\implies f(n) \in \Theta(n)\\[2ex]
\infty\implies f(n) \in \Omega(n) \\[2ex]
\end{array}\right.
$$
{% endnote %}

**例 2.5** 比较$\lg n$与$\sqrt n$的增长顺序。

解 计算$\lg n$与$\sqrt n$之比的极限。如果读者有一定高等数学基础的话，应该立即知道它们之比的极限为$0$。
$$
\lim_{n\to \infty} \frac{\lg n}{\sqrt n} = \lim_{n\to \infty} \frac{c \cdot\frac 1n}{\frac12 n^{-\frac12}}= \lim_{n\to \infty}  c\cdot \frac 1 {\sqrt n} = 0
$$
**例 2.6** 比较$n!$与$2^n$的增长顺序。

解 计算$n!$与$2^n$之比的极限。
$$
\lim_{n\to \infty} \frac{n!}{2^n} \geqslant \lim_{n\to \infty} \frac 12 \cdot \frac 22 \cdot \frac 33 \cdots \frac {n-1}{n-1} \cdot \frac{n}{2} = \infty
$$
故$n! = \Omega(2^n)$。



**例 2.7** 请比较$\lg(\lg^* n)$与$\lg^* (\lg n)$的增长顺序。

尝试计算它们之比的极限。注意到，$\lg^*2^n = 1 + \lg^* n$，于是由复合函数的极限可得:
$$
\begin{aligned}
\lim_{n\to \infty}  \frac{\lg(\lg^* n)}{\lg^* (\lg n)}& = \lim_{n\to \infty} \frac{\lg(\lg^* 2^n)}{\lg^* (\lg 2^n)} \\[2ex]
&=  \lim_{n\to \infty} \frac{\lg(1+\lg^{*} n)}{\lg^* n} \\[2ex]
&= \lim_{t\to \infty} \frac{\lg(1+t)}{t} \\[2ex]
&=0
\end{aligned}
$$
故$\lg(\lg^* n)=\mathcal o(\lg^* (\lg n))$。



**例 2.8** 请比较以下函数的增长顺序。即求出满足$f_1=\mathcal O(f_2),\;f_2=\mathcal O(f_3)\cdots$的一种排列。请将你的排列按等价类形式划分，即$f(n)$与$g(n)$在相同的类别中，当且仅当$f(n) = \Theta(g(n))$。
$$
\begin{array}{cccccc}
\lg \left(\lg ^{*} n\right) & 2^{\lg ^{*} n} & (\sqrt{2})^{\lg n} & n^{2} & n ! & (\lg n) ! \\
\left(\frac{3}{2}\right)^{n} & n^{3} & \lg ^{2} n & \lg (n !) & 2^{2^{n}} & n^{1 / \lg n} \\
\ln \ln n & \lg ^{*} n & n \cdot 2^{n} & n^{\lg \lg n} & \ln n & \lfloor \lg n \rfloor! \\
2^{\lg n} & (\lg n)^{\lg n} & e^{n} & 4^{\lg n} & (n+1) ! & \sqrt{\lg n} \\
\lg ^{*}(\lg n) & 2^{\sqrt{2} \lg n} & n & 2^{n} & n \lg n & 2^{2^{n+1}}
\end{array}
$$
解：顺序如下表所示，增长速度由上至下变大。

| 阶                         | 函数                               |
| -------------------------- | ---------------------------------- |
| 常数                       | $n^{\frac {1}{\lg n}}$             |
| $\lg(\lg^* n)$             | $\lg(\lg^* n)$                     |
| $\lg^* (\lg n)$            | $\lg^* (\lg n)$                    |
| $\lg^* n$                  | $\lg^* n$                          |
| $2^{\log^* n}$             | $2^{\lg^* n}$                      |
| $\log \log n$              | $\ln \ln n$                        |
| $\log n$                   | $\sqrt{\lg n}$、$\ln n$            |
| $\log^2n$                  | $\lg^2n$                           |
| $n^{\frac12}$              | $(\sqrt 2)^{\lg n}$                |
| $n$                        | $2^{\lg n}$、$n$                   |
| $n^\sqrt 2$                | $2^{\sqrt 2 \lg n}$                |
| $n\log n$                  | $\lg (n!)$、$n\lg n$               |
| $n^2$                      | $n^2$、$4^{\lg n}$                 |
| $n^3$                      | $n^3$                              |
| $\lfloor \lg n \rfloor!$   | $\lfloor \lg n \rfloor!$           |
| $n^{\lg \lg n}$            | $n^{\lg \lg n}$、$(\lg n)^{\lg n}$ |
| $\left( \frac 32\right)^n$ | $\left( \frac 32\right)^n$         |
| $2^n$                      | $2^n$                              |
| $n\cdot 2^n$               | $n\cdot 2^n$                       |
| $e^n$                      | $e^n$                              |
| $4^n$                      | $4^n$                              |
| $n!$                       | $n!$                               |
| $(n+1)!$                   | $(n+1)!$                           |
| $2^{2^n}$                  | $2^{2^n}$                          |
| $2^{2^{n+1}}$              | $2^{2^{n+1}}$                      |

这里给出一些重要的函数之间的比较过程。

- $(\lg n)!$与$a^n$。

$$
\begin{aligned}
\lim_{n\to \infty} \frac{(\lg n)!}{a^n} &= \lim_{n\to \infty}  \frac{\sqrt{2\pi \lg n} \cdot \left( \frac {\lg n}{e} \right)^{\lg n}} {a^n}\\[2ex]
&=\lim_{n\to \infty} \frac {\exp \left(\ln \sqrt{2 \pi \lg n} + \lg n \cdot (\lg n - \lg e )   \right)}{a^n} \\[2ex]
&= \lim_{n\to \infty} \frac {\exp \left( \lg \lg n + o(\lg \lg n )   \right)}{\exp (n\ln a )} \\[2ex]
&=0 \\[2ex]
\end{aligned}
$$

- $n^{\lg \lg n}$与$a^n$。

$$
\begin{aligned}
\lim_{n\to \infty} \frac{a^n}{n^{\lg \lg n}} &= \lim_{n\to \infty}\frac {\exp\left( n \ln a \right) }{\exp (\lg n \cdot \lg\lg n )}\\[2ex]
&=\lim_{t\to \infty} \frac {\exp\left( 2^t \ln a \right) }{\exp (t \cdot \lg t )} \\[2ex]
&=\infty \\[2ex]
\end{aligned}
$$



- $n^{\lg \lg n}$与$(\lg n)!$。

$$
\begin{aligned}
\lim_{n\to \infty} \frac{(\lg n)!}{n^{\lg \lg n}} &= \lim_{n\to \infty}  \frac{\sqrt{2\pi \lg n} \cdot \left( \frac {\lg n}{e} \right)^{\lg n}} {n^{\lg \lg n}}\\[2ex]
&=\lim_{n\to \infty} \frac {\exp \left(\ln \sqrt{2 \pi \lg n} + \lg n \cdot (\lg n - \lg e )   \right)}{n^{\lg \lg n}} \\[2ex]
&= \lim_{n\to \infty} \frac {\exp \left( \lg \lg n + o(\lg \lg n )   \right)}{\exp (\lg n \cdot \lg\lg n )} \\[2ex]
&=0 \\[2ex]
\end{aligned}
$$

- $2^{\lg^* n}$与$\lg \lg n$。

$$
\begin{aligned}
\lim_{n\to \infty} \frac{2^{\lg^* n}} {\lg \lg n} &= \lim_{n\to \infty} \frac{2^{\lg^* 2^n}} {\lg n} \\[2ex]
&=\lim_{n\to \infty}\frac{2^{1+\lg^* n}} {\lg n} \\[2ex]
&=\lim_{n\to \infty}\frac{2\cdot2^{\lg^* n}} {\lg n} \\[2ex]
&= \lim_{n\to \infty} \frac{2\cdot(\lg ^* n -1)} {\lg n} \\[2ex]
&=0
\end{aligned}
$$



### 多项式时间

在计算复杂性里，所谓多项式时间复杂度，就是指算法的复杂度可以控制在$\mathcal O(n^k)$以内，其中$k$是任意常数。限制于当前计算机硬件的速度，如果一个算法是指数级复杂度，那么意味着它的效率是及其地下的，人们往往对那些多项式时间复杂度的算法更感兴趣。

为此，人们提出了P问题和NP问题。其中P问题就指的是可以在多项式时间内解决的问题，NP问题指的是可以在多项式时间内验证的问题。NP问题又包括了NPC问题，感兴趣的读者可以查阅计算理论相关的书籍。

接下来回到主题，继续讨论我们的时间复杂度。我们很关心的一个问题是，一个函数$f(n)$，不论它的表达式多么复杂，它的上界是否可以是多项式？

{% note info no-icon %}

**定义 2.12** 我们称$f(n)$是多项式有界的，若$f(n) \in\mathcal O(n^k)$。

{% endnote %}	

**例 2.9** 函数$\lceil \lg n \rceil!$是多项式有界的吗？函数$\lceil \lg \lg n !\rceil$呢？

解：对于$\lceil \lg n \rceil!$，根据Stiring公式有：
$$
\begin{aligned}
\lim_{n\to \infty} \frac{ (\lg n) !} {n^k} &= \lim_{n\to \infty}  \frac{\sqrt{2\pi \lg n} \cdot \left( \frac {\lg n}{e} \right)^{\lg n}} {n^{k}} \\[2ex]
&= \lim_{n\to \infty}  \frac{\exp(\frac12 \ln 2\pi + \frac12 \ln \lg n + \lg n \ln \lg n - \lg n) } {\exp(k \ln n)} \\[2ex]
&= \lim_{n\to \infty}  \frac{\exp( \lg n \ln \lg n + \mathcal O(\lg n) ) } {\exp(k \ln n)}  \\[2ex]
&= \infty
\end{aligned}
$$
所以$\lceil \lg n \rceil!$不是多项式有界的。

也可以用反证法，假设$\lceil \lg n \rceil!$是多项式有界的，那么存在一个常数$c$和正整数$n_0$，当$n\geqslant n_0$时，总有$\lceil \lg n \rceil! \geqslant c \cdot n^k$。若取$n=2^t$，即$t! \geqslant c\cdot 2^{kt}$。这显然是矛盾的，因为$t!$不是指数有界的。

对于$\lceil \lg \lg n\rceil!$，同样根据Stiring公式有：
$$
\begin{aligned}
\lim_{n\to \infty} \frac{ (\lg\lg n) !} {n^k} &= \lim_{n\to \infty}  \frac{\sqrt{2\pi \lg\lg n} \cdot \left( \frac {\lg \lg n}{e} \right)^{\lg \lg n}} {n^{k}} \\[2ex]
&= \lim_{t\to \infty} \frac{\sqrt{2\pi \lg t} \cdot \left( \frac {\lg t}{e} \right)^{\lg t}} {2^{kt}} \\[2ex]
&= \lim_{t\to \infty} \frac{\exp(\frac 12 \ln\lg t +\frac12\ln{2\pi}+ \lg t\ln \left( \frac {\lg t}{e} \right) )} {\exp({kt\ln2})}\\[2ex]
&= \lim_{t\to \infty} \frac{\exp(\ln t \ln \lg t +\mathcal O(\lg t))  } {\exp({kt\ln2})}\\[2ex]
&=0
\end{aligned}
$$
所以，$\lceil \lg \lg n\rceil!$是多项式有界的。



## 求解复杂度

根据算法策略的不同，算法可以分为迭代和递归两种。这两种策略有着不同的递推关系，下面我们讨论如何根据算法的递推关系求解算法的复杂度。

### 减而治之

减而治之的问题的递推关系通常如下所示
$$
T(n)=c_1T(n-1)+c_2T(n-2)+\cdots+c_kT(n-k) + f(n)
$$
其中$c_k$为一常数，$f(n)$通常是幂函数。

这类的递推关系又称为线性递推关系，若$f(n)=0$，我们称上式为常系数齐次线性递推关系，否则称为常系数非齐次线性递推关系。关于线性递推关系的求解，在各种组合数学的参考书中都能找到，这里我们不再赘述。我们主要关注一下如何求解分而治之问题的递推关系。



### 分而治之

分而治之的问题通常具有以下形式的递推式。
$$
T(n) = aT(\frac nb) + f(n)
$$
对于这种类型的递推式，最基本的方法就是不断将它进行替换，直至等式右边出现$T(1)$。
$$
\begin{align}
T(n) &= aT\left(\frac nb \right) + f(n)\\
&=a\left(aT\left(\frac{n}{b^2} \right)+ f\left(\frac nb\right)\right) + f(n) = a^2T\left(\frac n{b^2}\right) + af\left(\frac nb\right) + f(n) \\
&\vdots \\
&=a^kT\left(\frac n{b^k}\right) + \sum_{i=0}^{k-1} a^i f\left(\frac n{b^i}\right)\\
\end{align}
$$
注意到，实际上当递归到递归基时，有$n=b^k$，即$k=\log_b n$。所以上式变为
$$
\begin{align}
T(n)&=a^{log_b n}T\left(1\right) + \sum_{i=0}^{log_b n-1} a^i f\left(\frac n{b^i}\right) \\
&=n^{\log_b a}T(1)+ \sum_{i=0}^{log_b n-1} a^i f\left(\frac n{b^i}\right) \\
\end{align}
$$
等式右边的第一项是一个幂函数，第二项是一个几何级数。我们只需考虑这两项的增长性孰大孰小，即可表示出$T(n)$。于是，产生了{% label 主定理 purple %}。

{% note success no-icon %}

**定理 3.1（主定理）**

设$f(n)$与$T(n)$如上述定义。

1. 若存在一个任意小的常数$\epsilon>0$，使得$f(n) = \mathcal O (n^{\log _ba - \epsilon})$。则$T(n) = \mathcal \Theta(n ^{\log _ba})$。
2. 若存在常数$k\geqslant0$，使得$f(n) = \mathcal \Theta (n ^{\log_b a} \cdot \log^k n)$，则$T(n) = \mathcal \Theta(n ^{\log _ba} \cdot \log^{k+1} n)$。
3. 若存在一个任意小的常数$\epsilon>0$和$\delta <1$，使得$f(n) = \mathcal\Omega (n^{\log _ba+ \epsilon} )$，则$T(n) = \mathcal \Theta(f(n))$

{% endnote %}

请注意，主定理只是给出$T(n)$渐进意义上的增长性，而并没有给出$T(n)$的真正解。

要理解主定理并不难，首先回忆我们上述得出的$T(n)$的表达式。
$$
T(n)=n^{\log_b a}T(1)+ \sum_{i=0}^{log_b n-1} a^i f\left(\frac n{b^i}\right)
$$
不难发现，决定$T(n)$的增长性无外乎以下三种情况：

- $f(n)$比较小，那么第一项占主导地位，于是$T(n) = \mathcal \Theta(n ^{\log _ba})$。
- 当上述和式中的每一项都相互成比例时，那么$T(n)$就是$f(n)$与一个对数因子的乘积。
- 当$f(n)$大于$n^{\log_ba}$时，第二项是以$f(n)$为首项的递减的几何级数，所以$T(n)$与$f(n)$成正比。

下面我们简单证明一下第二种情况。设$f(n)=n^{\log_ba}\log^kn$，我们说明当$n$很大时，和式中的任意两项是成比例的。
$$
\begin{align}
\lim_{n \to \infty}\frac{a^i f\left(\frac n{b^i}\right)}{a^{i+1} f\left(\frac n{b^{i+1}}\right)} 
&= \lim_{n \to \infty}\frac {a^i \left(\frac n{b^i} \right)^{\log_ba} \log^k \left( \frac n{b^i}\right)}{a^{i+1} \left(\frac n{b^{i+1}} \right)^{\log_ba} \log^k \left( \frac n{b^{i+1}}\right)} \\[2ex]
&=\lim_{n \to \infty}\left(\frac {\log n - i\log b}{\log n - (i+1) \log b}\right)^k\\[2ex]
&=1
\end{align}
$$
接下来来看一些主定理的应用。

**例 3.1** 设递推关系$T(n)=4T(\frac n2)+n$，试求$T(n)$的增长性。

解：$n^{\log_ba}=n^2$，所以此时$f(n)$属于第一种情况。$T(n)=\Theta(n^2)$。



**例 3.2** 设递推关系$T(n)=2T(\frac n2)+n\log n$，试求$T(n)$的增长性。

解：$n^{\log_ba}=n$，$f(n)=\Theta(n\log n)$，属于第二种情况。所以$T(n)=\Theta(n \log^2 n)$。



**例 3.3** 设递推关系$T(n)=T(\frac n3)+n$，试求$T(n)$的增长性。

解：$n^{\log_ba}=1$，$f(n)=\Omega(1)$，属于第三种情况。$T(n)=\Theta(n)$。



**例 3.4** 设递推关系$T(n)=9T(\frac n3)+n^{2.5}$，试求$T(n)$的增长性。

解：$n^{\log_ba}=n^2$，$f(n)=\Omega(n^2)$，属于第三种情况。$T(n)=\Theta(n^{2.5})$。



**例 3.5** 设递推关系$T(n)=2T(n^{\frac12})+\log n$，试求$T(n)$的增长性。

解：这种情况比较棘手，不适用与主定理的情况。我们考虑进行变量替换。设$t=\log n$，则原式变为
$$
T(2^t) = 2T(2^{\frac t2})+t
$$
再稍加变换，令$S(t)=T(2^t)$，得
$$
S(t)=2S(\frac t2) + t
$$
$t^{\log_ba}=t$，$f(t)=\Theta(t)$，属于第二种情况，根据主定理有$S(t)=\Theta(t \log t)$。

所以$T(2^t)=\Theta(t\log t)$，将$t=\log n$回代，得：
$$
T(n)=\Theta(\log n \log \log n)
$$


### 分摊分析









## 附录

附录中给出了一些有用的数学公式。

### 对数函数

对于对数函数，通常有下面这些省略性的记号。

- $\lg n = \log _2 n$（在计算机科学中，我们默认底数为$2$，在数学上则是默认为$10$）
- $\ln n = \log _e n$（自然对数）
- $\lg^kn = (\lg n)^k$（取幂）
- $\lg\lg n = \lg(\lg n)$（复合运算）

除了以上的记号外，还有一个重要的记号是$\lg^*n$，表示**多重对数函数**。它来源于**多重函数**。

{% note info no-icon %}

**定义** 记号$f^{(i)}(n)$表示函数$f(n)$的$i$重映射。
$$
f^{(i)}(n) =
\left\{
\begin {array}{l}
n,\; i = 0\\[2ex]
f(f^{(i - 1)}(n)),\; i>0\\[2ex]
\end {array} \right.
$$
{% endnote %}

请不要将它与数学上的高阶导数和函数的$i$次幂弄混。

由于只有在$\log^{(i-1)}(n)>0$时$\lg^{i}n$才有定义，而$\lg 1=0$，所以我们只能连续取对数直到函数值小于等于1。于是多重对数函数的定义为：
$$
\lg^{*}n = \min\{ i \;|\; i \geqslant 0,\; \lg^{(i)}(n) \leqslant 1 \}
$$
多重对数函数的增长非常缓慢。
$$
\begin{aligned}
\lg^* 2 &= \min\{ 1,\; 2 \} = 1 \\
\lg^* 4 &= \min\{  2  ,\; 3 \} = 2 \\
\vdots \\
\lg^* 65536 &= \min\{  4  ,\; 5 \} = 4 \\
\end{aligned}
$$
下面是一些对数函数的主要性质。

1. $\log_ba\cdot c=\log_ba+\log_bc$
2. $\log_b \frac ac=\log_b a− \log_bc$
3. $\log_b a^c = c \log_b a$
4. $\log_ba=\frac {\log_c a}{\log_c b}$
5. $b ^{ \log _c a }= a^{\log_c b}$

我们仅简单证明第$5$条性质。
$$
\begin{aligned}
b ^{ \log _c a } = c^{(\log_c a) \cdot (\log_cb )} = a^{\log_c b}
\end{aligned}
$$
使用这些性质可以帮助我们化简一些函数。

**例** $\lg\lg\sqrt n = \lg \lg n−1$

**例** $n^{\lg \lg n} = (\lg n)^{\lg n} = \lg ^{\lg n} n$



### Stiring公式

Stiring公式是逼近发散序列最著名的一个例子之一。它被用来逼近$n!$。
$$
n!=\sqrt {2\pi n} \left(\frac ne \right)^n \left(1+ \frac {1}{12n} + \frac {1}{288n^2} + \mathcal O\left(\frac {1}{n^3} \right) \right)
$$
在实际的使用中，我们常常忽略后面的误差项。
$$
n!=\sqrt {2\pi n} \left(\frac ne \right)^n
$$





### 常用级数

此处介绍一些算法复杂度分析中常用的级数。其中，算术级数、幂方级数和几何级数是收敛的，调和级数和对数级数是发散的。

#### 算术级数

算术级数与末项平方同阶。
$$
f(n)=1+2+\cdots +n
=\frac{n(n+1)}{2}
=\Theta\left(n^{2}\right)
$$


#### 幂方级数

幂方级数通常比末项高一阶。
$$
\sum_{k=0}^{n} k^{d} \approx \int_{0}^{n} x^{d} d x=\left.\frac{x^{d+1}}{d+1}\right|_{0} ^{n}=\frac{n^{d+1} }{d+1}=\Theta\left(n^{d+1}\right)
$$
平方求和公式。
$$
f(n)=\sum_{k=1}^{n} k^{2}=1^{2}+2^{2}+3^{2}+\ldots+n^{2}=\frac{n(n+1)(2 n+1) }{ 6} = \Theta\left(n^{3}\right)
$$
立方求和公式。
$$
f(n)=\sum_{k=1}^{n} k^{3}=1^{3}+2^{3}+3^{3}+\ldots+n^{3}=\frac {n^{2}(n+1)^{2} } {4}=\Theta\left(n^{4}\right)
$$
四次方求和公式。
$$
f(n)=\sum_{k=1}^{n} k^{4}=1^{4}+2^{4}+3^{4}+\ldots+n^{4}=\frac {n(n+1)(2 n+1)\left(3 n^{2}+3 n-1\right) }{ 3} =\Theta\left(n^{5}\right)
$$

#### 几何级数

几何级数与末项同阶。

$$
f_{a}(n)=\sum_{k=0}^{n} a^{k}=a^{0}+a^{1}+a^{2}+a^{3}+\ldots+a^{n}=\frac{a^{n+1}-1}{a-1}=\Theta\left(a^{n}\right), \quad 1 < a.
$$

以$2$为公比的几何级数。

$$
f_{2}(n)=\sum_{k-0}^{n} 2^{k}=1+2+4+8+\ldots+2^{n}=2^{n+1}-1=\mathcal{O}\left(2^{n+1}\right)=\Theta\left(2^{n}\right)
$$

#### 调和级数

调和级数是发散的，但是发散得非常缓慢。因此，可以使用一个公式逼近调和级数的值。
$$
H_n = \ln n + \gamma + \frac{1}{2n} + \mathcal O \left(\frac {1}{n^2} \right) = \Theta(\log n)
$$
其中$\gamma$为欧拉常数，它的值大约为$0.5772156649$。



#### 对数级数

对数级数同样是发散的，但我们可以通过Stiring公式来逼近它。
$$
\sum_{k=1}^{n} \ln k=\ln \prod_{k=1}^{n} k=\ln n ! \approx(n+ \frac12) \cdot \ln n-n + \ln \sqrt{2\pi} + \mathcal O(\frac 1n)=\Theta(n \cdot \log n)
$$


### 范德蒙德卷积

范德蒙德卷积公式来源于组合数学中的选取问题。可以理解为从数量为$n$和$m$的两个堆中一共选择$k$个物品。
$$
\sum_{i=0}^k \binom{n}{i}\binom{m}{k-i} = \binom{n+m}{k}
$$






## 参考文献

[1] L. R. Vermani &  S. Vermani. *An Elementary Approach to Design and Analysis of Algorithms [M].* New Jersey: World Scientific, 2019. ISBN:978-1-78634-675-9.

[2] A. Levitin. *Introduction to The Design & Analysis of Algorithms 3rd edition [M].* Pearson Education.  2012. ISBN:978-0-13-231681-1.

[3] M. H. Alsuwaiyel. *Algorithms: Design Techniques and Analysis [M].* New Jersey: World Scientific. Ltd. 2016. ISBN: 9789814723640.

[4] T. H. Cormen & C. E. Leiserson & R. L. Rivest & C. Stein. *Introduction to Algorithms 3rd edition [M].* The MIT Press. 2009. ISBN: 978-0-262-53305-8.

[5] N. Karumanchi. *Data Structures And Algorithms Made Easy [M].* CareerMonk Publications. 2017. ISBN: 9788193245279.

[6] S. S. Skiena. *The Algorithm Design Manual 2nd edition [M].* London: Springer - Verlag. 2008. ISBN: 978-1-84800-069-8.

[7] M. T. Goodrich & R. Tamassia. *Algorithm Design And Application [M].* Jhon Willey & Sons. 2015. iSBN: 978-1-118-33591-8.

[8] R. Sedgewick. P. Flajolet. *An Introduction to the Analysis of Algorithms 2nd edition [M].* Pearson Education. 2013. ISBN: 978-0-321-90575-8.

[9] D. Vrajitoru & W.*Knight. Practical Analysis of Algorithms [M].* Switzerland: Springer International Publishing. 2014. ISBN: 978-3-319-09887-6.

