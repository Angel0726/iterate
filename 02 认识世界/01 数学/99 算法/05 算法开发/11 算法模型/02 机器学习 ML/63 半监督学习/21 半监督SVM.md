---
title: 21 半监督SVM
toc: true
date: 2018-07-01 07:30:11
---


# 半监督 SVM


半监督支持向量机(Semi-Supervised Support Vector Machine，简称 S3VM)是支持向量机在半监督学习上的推广。在不考虑未标记样本时，支 持向量机试图找到最大间隔划分超平面，而在考虑未标记样本后，S3VM试图找到能将两类有标记样本分开，且穿过数据低密度区域的划分超平面，如图 13.3所示，这里的基本假设是“低密度分隔” (low-density separation)，显然，这是聚类假设在考虑了线性超平面划分后的推广.

<center>

![](http://images.iterate.site/blog/image/180630/Fa2C4a9Gke.png?imageslim){ width=55% }


</center>



半监督支持向量机中最著名的是 TSVM (Transductive Support Vector Machine) 。与标准 SVM 一样，TSVM也是针对二分类问题 的学习方法.TSVM试图考虑对未标记样本进行各种可能的标记指派(label assignment)，即尝试将每个未标记样本分别作为正例或反例，然后在所有这些 结果中，寻求一个在所有样本(包括有标记样本和进行了标记指派的未标记样 本)上间隔最大化的划分超平面。一旦划分超平面得以确定，未标记样本的最终 标记指派就是其预测结果.

形式化地说，给定 $D_{l}=\left\{\left(\boldsymbol{x}_{1}, y_{1}\right),\left(\boldsymbol{x}_{2}, y_{2}\right), \ldots,\left(\boldsymbol{x}_{l}, y_{l}\right)\right\}$ 和 $D_{u}=\left\{\boldsymbol{x}_{l+1}\right.\boldsymbol{x}_{l+2}, \dots, \boldsymbol{x}_{l+u} \}$ 其中 $y_{i} \in\{-1,+1\}$ ，$l\ll u$ ，$l+u=m$ 。 TSVM 的学习目标是 为 $D_u$ 中的样本给出预测标记 $\hat{\boldsymbol{y}}=\left(\hat{y}_{l+1}, \hat{y}_{l+2}, \dots, \hat{y}_{l+u}\right)$ ,$\hat{y}_i\in\{—1,+1\}$，使得

$$
\begin{aligned} \min _{\boldsymbol{w}, b, \hat{\boldsymbol{y}}, \boldsymbol{\xi}} & \frac{1}{2}\|\boldsymbol{w}\|_{2}^{2}+C_{l} \sum_{i=1}^{l} \xi_{i}+C_{u} \sum_{i=l+1}^{m} \xi_{i} \\ \text { s.t. } & y_{i}\left(\boldsymbol{w}^{\mathrm{T}} \boldsymbol{x}_{i}+b\right) \geqslant 1-\xi_{i}, \quad i=1,2, \ldots, l \\ & \hat{y}_{i}\left(\boldsymbol{w}^{\mathrm{T}} \boldsymbol{x}_{i}+b\right) \geqslant 1-\xi_{i}, \quad i=l+1, l+2, \ldots, m \\ & \xi_{i} \geqslant 0, \quad i=1,2, \ldots, m \end{aligned}
$$


其中， $(\boldsymbol{w}, b)$ 确定了一个划分超平面; $\boldsymbol{\xi}$ 为松弛向量，$\xi_i(i=1,2,\cdots ,l)$ 对应于有标记样本, $\xi_i(i=l+1,l+2,\cdots ,m)$ 对应于未标记样本；$C_l$ 与 $C_u$ 是由用户指 定的用于平衡模型复杂度、有标记样本与未标记样本重要程度的折中参数.

显然，尝试未标记样本的各种标记指派是一个穷举过程，仅当未标记样本 很少时才有可能直接求解。在一般情形下，必须考虑更高效的优化策略.

TSVM 采用局部搜索来迭代地寻找式 (13.9) 的近似解。具体来说，它先利用有标记样本学得一个 SVM ，即忽略式(13.9)中关于 $D_u$ 与 $\hat{\boldsymbol{y}}$ 的项及约束。然后，利用这个 SVM 对未标记数据进行标记指派(label assignment)，即将 SVM 预测的结果作为“伪标记”(pseudo-label)赋予未标记样本。此时 $\hat{\boldsymbol{y}}$ 成为已知，将其代入式(13.9)即得到一个标准 SVM 问题，于是可求解出新的划分超平面和 松弛向量；注意到此时未标记样本的伪标记很可能不准确，因此 $C_u$ 要设置为比 $C_l$ 小的值，使有标记样本所起作用更大。接下来，TSVM 找出两个标记指派为异类且很可能发生错误的未标记样本，交换它们的标记，再重新基于式(13.9)求 解出更新后的划分超平面和松弛向量，然后再找出两个标记指派为异类且很可能发生错误的未标记样本，.....。标记指派调整完成后，逐渐增大 $C_u$ 以提高未标记样本对优化目标的影响，进行下一轮标记指派调整，直至 $C_u=C_l$ 为止。此时 求解得到的 SVM 不仅给未标记样本提供了标记，还能对训练过程中未见的示例进行预测。TSVM的算法描述如图 13.4所示.

<center>

![](http://images.iterate.site/blog/image/180701/BFLBB6994c.png?imageslim){ width=55% }


</center>


在对未标记样本进行标记指派及调整的过程中，有可能出现类别不平衡问 题，即某类的样本远率于另一类，这将对 SVM 的训练造成困扰。为了减轻类别 不平衡性所造成的不利影响，可对图 13.4的算法稍加改进：将优化目标中的 $C_u$ 项拆分为 $C_u^+$ 与 $C_u^-$ 两项，分别对应基于伪标记而当作正、反例使用的未标记 样本，并在初始化时令

$$
C_{u}^{+}=\frac{u_{-}}{u_{+}} C_{u}^{-}
$$

其中 $u_+$ 与 $u_-$ 为基于伪标记而当作正、反例使用的未标记样本数.



在图 13.4算法的第 6-10行中，若存在一对未标记样本 $\boldsymbol{x}_{i}$ 与 $\boldsymbol{x}_{j}$ ，其标记 指派 $\hat{y}_i$ 与 $\hat{y}_j$ 不同，且对应的松弛变量满足 $\xi_i+\xi_j>2$ ，则意味着 $\hat{y}_i$ 与 $\hat{y}_j$ 很可 能是错误的，需对二者进行交换后重新求解式(13.9)，这样每轮迭代后均可使 式(13.9)的目标函数值下降.

显然，搜寻标记指派可能出错的每一对未标记样本进行调整，是一个涉及巨大计算开销的大规模优化问题。因此，半监督 SVM 研究的一个重点是 如何设计出高效的优化求解策略，由此发展出很多方法，如基于图核(graph kernel)函数梯度下降的 LDS 、基于标记均值估计的 meanS3VM 等.







# 相关

- 《机器学习》周志华



