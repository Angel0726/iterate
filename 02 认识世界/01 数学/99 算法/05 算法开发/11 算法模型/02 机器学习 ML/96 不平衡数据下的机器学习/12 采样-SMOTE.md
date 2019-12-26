---
title: 12 采样-SMOTE
toc: true
date: 2019-08-28
---

# SMOTE 算法

合成少数类过采样技术 SMOTE Synthetic Minority Oversampling Technique


合成少数类过采样技术，它是基于随机过采样算法的一种改进方案，由于随机过采样采取简单复制样本的策略来增加少数类样本，这样容易产生模型过拟合的问题，即使得模型学习到的信息过于特别(Specific)而不够泛化(General)，SMOTE算法的基本思想是对少数类样本进行分析并根据少数类样本人工合成新样本添加到数据集中，具体如图 2 所示，算法流程如下。

1. 对于少数类中每一个样本 $x$，以欧氏距离为标准计算它到少数类样本集 $S_{min}$ 中所有样本的距离，得到其 $k$ 近邻。
2. 根据样本不平衡比例设置一个采样比例以确定采样倍率 $N$，对于每一个少数类样本 $x$，从其 $k$ 近邻中随机选择若干个样本，假设选择的近邻为 $\hat{x}$。
3. 对于每一个随机选出的近邻 $\hat{x}$，分别与原样本按照如下的公式构建新的样本。


$$
x_{n e w}=x+\operatorname{rand}(0,1) \times(\widetilde{x}-x)
$$


![mark](http://images.iterate.site/blog/image/20190828/YNvpR1JsASo0.png?imageslim)


SMOTE算法摒弃了随机过采样复制样本的做法，可以防止随机过采样易过拟合的问题，实践证明此方法可以提高分类器的性能。但是由于对每个少数类样本都生成新样本，因此容易发生生成样本重叠(Overlapping)的问题，为了解决 SMOTE 算法的这一缺点提出一些改进算法，其中的一种是 Borderline-SMOTE算法，如图 3 所示。

在 Borderline-SMOTE中，若少数类样本的每个样本 $x_i$ 求 $k$ 近邻，记作 $S_i-knn$，且 $S_i-knn$ 属于整个样本集合 $S$ 而不再是少数类样本，若满足

$$
\frac{\mathrm{k}}{2}<\left|S_{\mathrm{i}-\mathrm{knn}} \cap \mathrm{S}_{\mathrm{maj}}\right|<\mathrm{k}
$$


则将样本 $x_i$ 加入 DANGER 集合，显然 DANGER 集合代表了接近分类边界的样本，将 DANGER 当作 SMOTE 种子样本的输入生成新样本。特别地，当上述条件取右边界，即 k 近邻中全部样本都是多数类时，此样本不会被选择为种样本生成新样本，此情况下的样本为噪音。

![mark](http://images.iterate.site/blog/image/20190828/4Del4IABf7af.png?imageslim)






# 相关

- [不平衡数据下的机器学习方法简介](http://baogege.info/2015/11/16/learning-from-imbalanced-data/)
