---
title: 23 AdaCost 算法
toc: true
date: 2019-08-28
---

### AdaCost算法

让我们先来简单回顾一下 Adaboost 算法，如下图 6 所示。Adaboost算法通过反复迭代，每一轮迭代学习到一个分类器，并根据当前分类器的表现更新样本的权重，如图中红框所示，其更新策略为正确分类样本权重降低，错误分类样本权重加大，最终的模型是多次迭代模型的一个加权线性组合，分类越准确的分类器将会获得越大的权重。


<center>

![mark](http://images.iterate.site/blog/image/20190828/kajcxHPftUUM.png?imageslim)

</center>


AdaCost算法修改了 Adaboost 算法的权重更新策略，其基本思想是对于代价高的误分类样本大大地提高其权重，而对于代价高的正确分类样本适当地降低其权重，使其权重降低相对较小。总体思想是代价高样本权重增加得大降低得慢。其样本权重按照如下公式进行更新。其中 $\beta*+和\beta*-分别表示样本被正确和错误分类情况下\beta$ 的取值。

$$
D_{t+1}(i)=D_{t}(i) \exp \left(-\alpha_{t} h_{t}\left(x_{i}\right) y_{i} \beta_{i}\right) / Z_{t}
$$

$$
\beta_{+}=-0.5 C_{i}+0.5
$$

$$
\beta_{-}=0.5 C_{i}+0.5
$$






# 相关

- [不平衡数据下的机器学习方法简介](http://baogege.info/2015/11/16/learning-from-imbalanced-data/)
