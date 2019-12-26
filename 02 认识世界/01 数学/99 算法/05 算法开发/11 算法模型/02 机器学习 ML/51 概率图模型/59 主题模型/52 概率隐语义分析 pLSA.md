---
title: 52 概率隐语义分析 pLSA
toc: true
date: 2019-08-27
---

### pLSA

pLSA 是用一个生成模型来建模文章的生成过程。假设有 K 个主题，M篇文章；对语料库中的任意文章 d，假设该文章有 N 个词，则对于其中的每一个词，我们首先选择一个主题 z，然后在当前主题的基础上生成一个词 w。<span style="color:red;">？对于其中的每一个词，首先选择一个主题 z ，然后在当前主题的基础上生成一个词 w 。是什么意思？</span>

图 6.10是 pLSA 图模型：

![](http://images.iterate.site/blog/image/20190406/7IKhCUbM4sll.png?imageslim){ width=55% }

生成主题 $z$ 和词 $w$ 的过程遵照一个确定的概率分布。

设在文章 d 中生成主题 z 的概率为 $p(z | d)$，在选定主题的条件下生成词 w 的概率为 $p(w | z)$，则给定文章 d，生成词 w 的概率可以写成：$p(w | d)=\sum_{z} p(w | z, d) p(z | d)$。<span style="color:red;">嗯，看到这个，对于上面那一句就有理解了。</span>

在这里我们做一个简化，假设给定主题 z 的条件下，生成词 w 的概率是与特定的文章无关的，则公式可以简化为：$p(w | d)=\sum_{z} p(w | z) p(z | d)$。

则整个语料库中的文本生成概率可以用似然函数表示为：

$$
L=\prod_{m}^{M} \prod_{n}^{N} p\left(d_{m}, w_{n}\right)^{c\left(d_{m}, w_{n}\right)}\tag{6.26}
$$

其中 $p\left(d_{m}, w_{n}\right)$ 是在第 m 篇文章 $d_{m}$ 中，出现单词 $w_{n}$ 的概率，与上文中的 $p(w | d)$ 的含义是相同的，只是换了一种符号表达；$c\left(d_{m}, w_{n}\right)$ 是在第 $m$ 篇文章 $d_{m}$ 中，单词 $w_{n}$ 出现的次数。

于是，Log 似然函数可以写成：

$$
\begin{aligned} l &=\sum_{m}^{M} \sum_{n}^{N} c\left(d_{m}, w_{n}\right) \log p\left(d_{m}, w_{n}\right) \\ &=\sum_{m}^{M} \sum_{n}^{N} c\left(d_{m}, w_{n}\right) \log \sum_{k}^{K} p\left(d_{m}\right) p\left(z_{k} | d_{m}\right) p\left(w_{n} | z_{k}\right) \end{aligned}\tag{6.27}
$$

在上面的公式中，定义在文章上的主题分布 $p\left(z_{k} | d_{m}\right)$ 和定义在主题上的词分布 $p\left(w_{n} | z_{k}\right)$ 是待估计的参数。

我们需要找到最优的参数，使得整个语料库的 Log 似然函数最大化。由于参数中包含的 $z_{k}$ 是隐含变量（即无法直接观测到的变量），因此无法用最大似然估计直接求解，可以利用最大期望算法来解决。<span style="color:red;">再补充完善下。</span>
