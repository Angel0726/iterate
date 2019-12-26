---
title: 51 A-distance
toc: true
date: 2019-08-28
---
# 可以补充进来的

- 用处。

# A-distance


$\mathcal{A}$ -distance 是一个很简单却很有用的度量。它可以用来估计不同分布之间的差异性。

$\mathcal{A}$ -distance被定义为建立一个线性分类器来区分两个数据领域的 hinge 损失(也就是进行二类分类的 hinge 损失)。

它的计算方式是，我们首先在源域和目标域上训练一个二分类器 $h$ ，使得这个分类器可以区分样本是来自于哪一个领域。我们用 $err(h)$ 来表示分类器的损失，则 $\mathcal{A}$ -distance定义为：

$$
\mathcal{A}(\mathcal{D}_s,\mathcal{D}_t) = 2(1 - 2 err(h))
$$

$\mathcal{A}$ -distance通常被用来计算两个领域数据的相似性程度，以便与实验结果进行验证对比。







# 相关

- [迁移学习简明手册](https://github.com/jindongwang/transferlearning-tutorial)  [王晋东](https://zhuanlan.zhihu.com/p/35352154)
