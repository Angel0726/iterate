---
title: 53 隐狄利克雷分配模型 LDA
toc: true
date: 2019-08-27
---

<center>

![](http://images.iterate.site/blog/image/180701/ikfEl45aL5.png?imageslim){ width=55% }

</center>

形象地说，如图 14.11所示，一个话题就像是一个箱子，里面装着在这个概念下出现概率较高的那些词。不妨假定数据集中一共包含 $K$ 个话题和 $T$ 篇文档，文档中的词来自一个包含 $N$ 个词的词典。我们用 $T$ 个 $N$ 维向量 $\mathbf{W}=\left\{\boldsymbol{w}_{1}, \boldsymbol{w}_{2}, \ldots, \boldsymbol{w}_{T}\right\}$ 表示数据集(即文档集合)，$K$ 个 $N$ 维向量典 $\boldsymbol{\beta}_{k}(k=1,2,\cdots,K)$ 表示话题，其中 $\boldsymbol{w}_{t} \in \mathbb{R}^{N}$ 的第 $n$ 个分量 $w_{t,n}$ 表示文档 $t$ 中词 $n$ 的词频，$\boldsymbol{\beta}_{k} \in \mathbb{R}^{N}$ 的第 $n$ 个分量 $\beta_{k,n}$ 表示话题 $k$ 中词 $n$ 的词频.



在现实任务中可通过统计文档中出现的词来获得词频向量 $\boldsymbol{w}_{i}(i=1,2,\cdots ,T)$ ，但通常并不知道这组文档谈论了哪些话题，也不知道每篇文档与哪些话题有关。LDA从生成式模型的角度来看待文档和话题。具体来说，LDA认为每篇文档包含多个话题，不妨用向量 $\Theta_{t} \in \mathbb{R}^{K}$  表示文档 $t$ 中所包含的每个话题的比例，$\Theta_{t,k}$ 即表示文档 $t$ 中包含话题 $k$ 的比例，进而通过下面的步骤由话题“生成”文档 $t$:

1. 根据参数为 $\boldsymbol{\alpha}$ 的狄利克雷分布随机采样一个话题分布 $\Theta_t$ ;
2. 按如下步骤生成文档中的 $N$ 个词：
    - 根据 $\Theta_t$ 进行话题指派，得到文档 t 中词 n 的话题 $z_{t,n}$
    - 根据指派的话题所对应的词频分布 $\boldsymbol{\beta}_{k}$ 随机采样生成词.

图 14.11演示出根据以上步骤生成文档的过程。显然，这样生成的文档自 然地以不同比例包含多个话题（步骤 1），文档中的每个词来自一个话题（步骤 2b），而这个话题是依据话题比例产生的（步骤 2a）.

图 14.12描述了 LDA 的变量关系，其中文档中的词频 $w_{t,n}$ 是唯一的已观测变量，它依赖于对这个词进行的话题指派 $z_{t,n}$ ，以及话题所对应的词频 $\boldsymbol{\beta}_{k}$ ；同时，话题指派 $z_{t,n}$ 依赖于话题分布 $\Theta_t$,$\Theta_t$ 依赖于狄利克雷分布的参数 $\boldsymbol{\alpha}$ ，而话题词频则依赖于参数 $\boldsymbol{\eta}$ .


<center>

![](http://images.iterate.site/blog/image/180701/49mBjh6KDL.png?imageslim){ width=55% }

</center>

于是,LDA模型对应的概率分布为

$$
\begin{aligned} p(\mathbf{W}, \mathbf{z}, \boldsymbol{\beta}, \Theta | \boldsymbol{\alpha}, \boldsymbol{\eta}) &=\\ \prod_{t=1}^{T} p\left(\Theta_{t} | \boldsymbol{\alpha}\right) \prod_{i=1}^{K} p\left(\boldsymbol{\beta}_{k} | \boldsymbol{\eta}\right) &\left(\prod_{n=1}^{N} P\left(w_{t, n} | z_{t, n}, \boldsymbol{\beta}_{k}\right) P\left(z_{t, n} | \Theta_{t}\right)\right) \end{aligned}
$$


其中 $p\left(\Theta_{t} | \boldsymbol{\alpha}\right)$ 和 $p\left(\boldsymbol{\beta}_{k} | \boldsymbol{\eta}\right)$ 通常分别设置为以 $\boldsymbol{\alpha}$ 和 $\boldsymbol{\eta}$ 为参数的 $K$ 维和 $N$ 维狄利克雷分布，例如

$$
p\left(\Theta_{t} | \boldsymbol{\alpha}\right)=\frac{\Gamma\left(\sum_{k} \alpha_{k}\right)}{\prod_{k} \Gamma\left(\alpha_{k}\right)} \prod_{k} \Theta_{t, k}^{\alpha_{k}-1}
$$


其中 $\Gamma(\cdot)$ 是 Gamma 函数。显然，$\boldsymbol{\alpha}$ 和 $\boldsymbol{\eta}$ 是模型式(14.41)中待确定的参数.

给定训练数据 $\mathbf{W}=\left\{\boldsymbol{w}_{1}, \boldsymbol{w}_{2}, \dots, \boldsymbol{w}_{T}\right\}$ , LDA 的模型参数可通过极大似然法估计，即寻找 $\boldsymbol{\alpha}$ 和 $\boldsymbol{\eta}$ 以最大化对数似然

$$
L L(\boldsymbol{\alpha}, \boldsymbol{\eta})=\sum_{t=1}^{T} \ln p\left(\boldsymbol{w}_{t} | \boldsymbol{\alpha}, \boldsymbol{\eta}\right)
$$

但由于 $p\left(\boldsymbol{w}_{t} | \boldsymbol{\alpha}, \boldsymbol{\eta}\right)$ 不易计算，式(14.43)难以直接求解，因此实践中常采用变分法来求取近似解.

若模型已知，即参数 $\boldsymbol{\alpha}$ 和 $\boldsymbol{\eta}$ 已确定，则根据词频 $w_{t,n}$ 来推断文档集所对应的话题结构(即推断 $\Theta_t$ ,$\boldsymbol{\beta}_{k}$ 和 $z_{t,n}$ )可通过求解

$$
p(\mathbf{z}, \boldsymbol{\beta}, \Theta | \mathbf{W}, \boldsymbol{\alpha}, \boldsymbol{\eta})=\frac{p(\mathbf{W}, \mathbf{z}, \boldsymbol{\beta}, \Theta | \boldsymbol{\alpha}, \boldsymbol{\eta})}{p(\mathbf{W} | \boldsymbol{\alpha}, \boldsymbol{\eta})}
$$


然而由于分母上的 $p(\mathbf{W} | \boldsymbol{\alpha}, \boldsymbol{\eta})$ 难以获取，式(14.44)难以直接求解，因此在实践中常采用吉布斯采样或变分法进行近似推断.







### LDA

LDA可以看作是 pLSA 的贝叶斯版本，其文本生成过程与 pLSA 基本相同，不同的是为主题分布和词分布分别加了两个狄利克雷（Dirichlet）先验。

为什么要加入狄利克雷先验呢？

这就要从频率学派和贝叶斯学派的区别说起：

- pLSA采用的是频率派思想，将每篇文章对应的主题分布 $p\left(z_{k} | d_{m}\right)$ 和每个主题对应的词分布 $p\left(w_{n} | z_{k}\right)$ 看成确定的未知常数，并可以求解出来；
- 而 LDA 采用的是贝叶斯学派的思想，认为待估计的参数（主题分布和词分布）不再是一个固定的常数，而是服从一定分布的随机变量。这个分布符合一定的先验概率分布（即狄利克雷分布），并且在观察到样本信息之后，可以对先验分布进行修正，从而得到后验分布。

LDA之所以选择狄利克雷分布作为先验分布，是因为它为多项式分布的共轭先验概率分布，后验概率依然服从狄利克雷分布，这样做可以为计算带来便利。<span style="color:red;">没有很明白，概率论的基础还是要重新掌握下的。</span>

图 6.11是 LDA 的图模型：

![](http://images.iterate.site/blog/image/20190407/OlpLtfefgBMP.png?imageslim){ width=55% }

其中 $\alpha, \beta$ 分别为两个狄利克雷分布的超参数，为人工设定。

语料库的生成过程为：对文本库中的每一篇文档 $d_i$，采用以下操作：

1. 从超参数为 $\alpha$ 的狄利克雷分布中抽样生成文档 $d_i$ 的主题分布 $\theta_i$ 。
2. 对文档 $d_i$ 中的每一个词进行以下 3 个操作：
    1. 从代表主题的多项式分布 $\theta_i$ 中抽样生成它所对应的主题 $z_{ij}$。
    2. 从超参数为 $\beta$ 的狄利克雷分布中抽样生成主题 $z_{ij}$ 对应的词分布 $\Psi_{z_{ij}}$。
    3. 从代表词的多项式分布 $\Psi_{z_{ij}}$ 中抽样生成词 $w_{ij}$。


我们要求解出主题分布 $\theta_i$ 以及词分布 $\Psi_{z_{ij}}$ 的期望，可以用吉布斯采样（Gibbs Sampling）的方式实现：

首先随机给定每个单词的主题，然后在其他变量固定的情况下，根据转移概率抽样生成每个单词的新主题。对于每个单词来说，转移概率可以理解为：给定文章中的所有单词以及除自身以外其他所有单词的主题，在此条件下该单词对应为各个新主题的概率。最后，经过反复迭代，我们可以根据收敛后的采样结果计算主题分布和词分布的期望。<span style="color:red;">嗯。</span>

<span style="color:red;">补充完善下。要完全弄清楚。</span>




# 相关

- 《百面机器学习》
- 《机器学习》周志华
