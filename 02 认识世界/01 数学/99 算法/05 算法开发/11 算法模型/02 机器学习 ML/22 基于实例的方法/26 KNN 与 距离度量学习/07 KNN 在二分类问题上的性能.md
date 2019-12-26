---
title: 07 KNN 在二分类问题上的性能
toc: true
date: 2019-08-27
---

# KNN 在二分类问题上的性能

暂且假设距离计算是“恰当”的，即能够恰当地找出 $k$ 个近邻，我们来对 “最近邻分类器” (1NN，即 $k$= 1)在二分类问题上的性能做一个简单的讨论。


给定测试样本 $\boldsymbol{x}$ ，若其最近邻样本为 $\boldsymbol{z}$ ，则最近邻分类器出错的概率就是 $\boldsymbol{x}$ 与 $\boldsymbol{z}$ 类别标记不同的概率，即

$$
P(e r r)=1-\sum_{c \in \mathcal{Y}} P(c | \boldsymbol{x}) P(c | \boldsymbol{z})\tag{10.1}
$$


假设样本独立同分布，且对任意 $\boldsymbol{x}$ 和任意小正数 $\delta$ ，在 $\boldsymbol{x}$ 附近 $\delta$ 距离范围内总能找到一个训练样本；换言之，对任意测试样本，总能在任意近的范围内找到式(10.1)中的训练样本 $\boldsymbol{z}$ 。

令 $c^{*}=\arg \max _{c \in \mathcal{Y}} P(c | \boldsymbol{x})$ 表示贝叶斯最优分类器的结果，有

$$
\begin{aligned} P(e r r) &=1-\sum_{c \in \mathcal{Y}} P(c | \boldsymbol{x}) P(c | \boldsymbol{z}) \\ & \simeq 1-\sum_{c \in \mathcal{Y}} P^{2}(c | \boldsymbol{x}) \\ & \leqslant 1-P^{2}\left(c^{*} | \boldsymbol{x}\right) \\ &=\left(1+P\left(c^{*} | \boldsymbol{x}\right)\right)\left(1-P\left(c^{*} | \boldsymbol{x}\right)\right) \\ & \leqslant 2 \times\left(1-P\left(c^{*} | \boldsymbol{x}\right)\right) \end{aligned}
$$

于是我们得到了有点令人惊讶的结论：最近邻分类器虽简单，但它的泛化错误率不超过贝叶斯最优分类器的错误率的两倍！<span style="color:red;">确认下贝叶斯最优分类器的错误率的计算</span>





# 相关

- 《机器学习》周志华
