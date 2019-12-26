---
title: 14 基于基尼指数来划分 CART
toc: true
date: 2019-08-27
---

### 基尼指数

<span style="color:red;">说实话，我看到基尼指数的时候，感觉这个肯定是比增益率更高级的，但是好像并不是这样。之前好像在哪里看到说还是推荐 C4.5 需要确认下。或者也许某种情景下基尼指数更好。</span>

<span style="color:red;">还是没怎么理解。</span>

CART 决策树使用 "基尼指数" (Gini index) 来选择划分属性。采用与信息熵相同的一些符号，数据集 $D$ 的纯度可用基尼值来度量：

$$
\begin{aligned} \operatorname{Gini}(D) &=\sum_{k=1}^{ | \mathcal{Y |}} \sum_{k^{\prime} \neq k} p_{k} p_{k^{\prime}} \\ &=1-\sum_{k=1}^{|\mathcal{Y}|} p_{k}^{2} \end{aligned}\tag{4.5}
$$


> $$
> \begin{aligned}
> Gini(D) &=\sum_{k=1}^{|y|}\sum_{k\neq{k'}}{p_k}{p_{k'}}\\
> &=1-\sum_{k=1}^{|y|}p_k^2
> \end{aligned}
> $$
> [推导]：假定当前样本集合 $D$ 中第 $k$ 类样本所占的比例为 $p_k(k =1,2,...,|y|)$，则 $D$ 的**基尼值**为
> $$
> \begin{aligned}
> Gini(p) &=\sum_{k=1}^{|y|}\sum_{k\neq{k'}}{p_k}{p_{k'}}\\
> &=\sum_{k=1}^{|y|}{p_k}{(1-p_k)} \\
> &=1-\sum_{k=1}^{|y|}p_k^2
> \end{aligned}
> $$


直观来说，$\operatorname{Gini}(D)$ 反映了从数据集中随机抽取两个样本，其类别标记不一致的概率。因此，$\operatorname{Gini}(D)$ 越小，则数据集的纯度越高。<span style="color:red;">嗯，再理解下，对这个式子还是不够理解。</span>

属性 $a$ 的基尼指数定义为：

$$
(D, a)=\sum_{v=1}^{V} \frac{\left|D^{v}\right|}{|D|} \operatorname{Gini}\left(D^{v}\right)\tag{4.6}
$$


于是，我们在候选属性集合 $A$ 中，选择那个使得划分后基尼指数最小的属性作为最优划分属性，即：

$$
a_{*}=\underset{a \in A}{\arg \min } \text { Gini\_index }(D, a)\tag{4.7}
$$


<span style="color:red;">奇怪，对于这个基尼系数好型没有怎么讲。</span>









# 相关

- 《机器学习》周志华
