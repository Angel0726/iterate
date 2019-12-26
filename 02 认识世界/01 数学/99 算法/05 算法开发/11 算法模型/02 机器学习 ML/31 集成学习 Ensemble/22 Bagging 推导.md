---
title: 22 Bagging 推导
toc: true
date: 2019-08-27
---

# Bagging

Bagging 这个名字是由 Bootstrap AGGregatING 缩写而来.

Bagging 是并行式集成学习方法最著名的代表。从名字即 可看出，它直接基于我们在 2.2.3节介绍过的自助采样法(bootstrap sampling). 给定包含 $m$ 个样本的数据集，我们先随机取出一个样本放入采样集中，再把该样本放回初始数据集，使得下次采样时该样本仍有可能被选中，这样，经过 $m$ 次随机采样操作，我们得到含 $m$ 个样本的采样集，初始训练集中有的样本在采样集里多次出现，有的则从未出现。由式(2.1)可知，初始训练集中约有 $63.2%$ 的样本出现在采样集中.


照这样，我们可采样出 $T$ 个含 $m$ 个训练样本的采样集，然后基于每个采样 集训练出一个基学习器，再将这些基学习器进行结合。这就是 Bagging 的基本流程。在对预测输出进行结合时，Bagging通常对分类任务使用简单投票法，对回归任务使用简单平均法。若分类预测时出现两个类收到同样票数的情形，则最简单的做法是随机选择一个，也可进一步考察学习器投票的置信度来确定最终胜者.Bagging的算法描述如图 8.5所示.

<center>

![](http://images.iterate.site/blog/image/180628/eG9glEFeci.png?imageslim){ width=55% }


</center>


假定基学习器的计算复杂度为 $O(m)$ 则 Bagging 的复杂度大致为  $T(O(m)+O(s))$ ，考虑到采样与投票/平均过程的复杂度 $O(s)$ 很小，而 T 通常是一个不太大的常数，因此，训练一个 Bagging 集成与直接使用基学习算法训练一个学习器的复杂度同阶，这说明 Bagging 是一个很高效的集成学习算法。另外，与标准 AdaBoost 只适用于二分类任务不同，Bagging 能不经修改地 用于多分类、回归等任务.



值得一提的是，自助采样过程还给 Bagging 带来了另一个优点：由于每个基学习器只使用了初始训练集中约 $63.2%$ 的样本，剩下约 $36.8%$ 的样本可用作验证集来对泛化性能进行 “包外估计”(out-of-bag estimate) 。为此需记录每个基学习器所使用的训练样本. 不妨令 $D_t$ 表示 $h_t$ 实际使用的训练样本集，令 $H^{oo b}(\boldsymbol{x})$ 表示对样本 $\boldsymbol{x}$ 的包外预 测，即仅考虑那些未使用 $\boldsymbol{x}$ 训练的基学习器在 $\boldsymbol{x}$ 上的预测，有

$$
H^{o o b}(\boldsymbol{x})=\underset{y \in \mathcal{Y}}{\arg \max } \sum_{t=1}^{T} \mathbb{I}\left(h_{t}(\boldsymbol{x})=y\right) \cdot \mathbb{I}\left(\boldsymbol{x} \notin D_{t}\right)
$$

则 Bagging 泛化误差的包外估计为

$$
\epsilon^{o o b}=\frac{1}{|D|} \sum_{(\boldsymbol{x}, y) \in D} \mathbb{I}\left(H^{o o b}(\boldsymbol{x}) \neq y\right)
$$


事实上，包外样本还有许多其他用途。例如当基学习器是决策树时，可使用包外样本来辅助剪枝，或用于估计决策树中各结点的后验概率以辅助对零训练样本结点的处理；当基学习器是神经网络时，可使用包外样本来辅助早期停止 以减小过拟合风险.


从偏差一方差分解的角度看，Bagging 主要关注降低方差，因此它在不剪枝决策树、神经网络等易受样本扰动的学习器上效用更为明显。我们以基于信息増益划分的决策树为基学习器，在表 4.5 的西瓜数据集 3.0$\alpha$ 上运行 Bagging 算法，不同规模的集成及其基学习器所对应的分类边界如图 8.6所示.

<center>

![](http://images.iterate.site/blog/image/180628/39IfBdI599.png?imageslim){ width=55% }


</center>



# 相关

- 《机器学习》周志华
