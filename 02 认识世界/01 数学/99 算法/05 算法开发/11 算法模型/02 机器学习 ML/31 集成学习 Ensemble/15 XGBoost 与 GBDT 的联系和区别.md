---
title: 15 XGBoost 与 GBDT 的联系和区别
toc: true
date: 2019-04-21
---
# 可以补充进来的

- 没看懂，而且，是怎么创造这个方法出来的？感觉真的厉害。


# XGBoost 与 GBDT 的联系和区别


XGBoost 是陈天奇等人开发的一个开源机器学习项目，高效地实现了 GBDT 算法并进行了算法和工程上的许多改进，被广泛应用在 Kaggle 竞赛及其他许多机器学习竞赛中并取得了不错的成绩。<span style="color:red;">厉害呀。</span>

我们在使用 XGBoost 平台的时候，也需要熟悉 XGBoost 平台的内部实现和原理，这样才能够更好地进行模型调参并针对特定业务场景进行模型改进。<span style="color:red;">嗯。</span>

XGBoost，GBDT，决策树

## XGBoost与 GBDT 的联系和区别有哪些？

原始的 GBDT 算法基于经验损失函数的负梯度来构造新的决策树，只是在决策树构建完成后再进行剪枝。而 XGBoost 在决策树构建阶段就加入了正则项，即：


$$
L_{t}=\sum_{i} l\left(y_{i}, F_{t-1}\left(x_{i}\right)+f_{t}\left(x_{i}\right)\right)+\Omega\left(f_{t}\right)\tag{12.2}
$$

其中 $F_{t-1}(x_i)$ 表示现有的 $t−1$ 棵树最优解。关于树结构的正则项定义为


$$
\Omega\left(f_{t}\right)=\gamma T+\frac{1}{2} \lambda \sum_{j=1}^{T} w_{j}^{2}\tag{12.3}
$$


其中 $T$ 为叶子节点个数， $w_j$ 表示第 $j$ 个叶子节点的预测值。对该损失函数在 $F_{t−1}$ 处进行二阶泰勒展开可以推导出：

$$
L_{t} \approx \tilde{L}_{t}=\sum_{j=1}^{T}\left[G_{j} w_{j}+\frac{1}{2}\left(H_{j}+\lambda\right) w_{j}^{2}\right]+\gamma T\tag{12.4}
$$

其中：

- $T$ 为决策树 $f_t$ 中叶子节点的个数，
- $G_{j}=\sum_{i \in I_{j}} \nabla_{F_{t-1}} l\left(y_{i}, F_{t-1}\left(x_{i}\right)\right)$，
- $H_{j}=\sum_{j \in I_{j}} \nabla_{F_{t-1}}^{2} l\left(y_{i}, F_{t-1}\left(x_{i}\right)\right)$ ，
- $I_j$ 表示所有属于叶子节点 $j$ 的样本的索引的结合。


假设决策树的结构已知，通过令损失函数相对于 $w_j$ 的导数为 $0$ 可以求出在最小化损失函数的情况下各个叶子节点上的预测值：

$$
w_{j}^{*}=-\frac{G_{j}}{H_{j}+\lambda}\tag{12.5}
$$

然而从所有的树结构中寻找最优的树结构是一个 NP-hard 问题，因此在实际中往往采用贪心法来构建出一个次优的树结构，基本思想是从根节点开始，每次对一个叶子节点进行分裂，针对每一种可能的分裂，根据特定的准则选取最优的分裂。<span style="color:red;">什么是 NP-hard 问题？一直听说过这个。</span>

不同的决策树算法采用不同的准则，如：

- IC3 算法采用信息增益，
- C4.5 算法为了克服信息增益中容易偏向取值较多的特征而采用信息增益比，
- CART 算法使用基尼指数和平方误差，

XGBoost也有特定的准则来选取最优分裂。

通过将预测值代入到损失函数中可求得损失函数的最小值：

$$
\tilde{L}_{t}^{*}=-\frac{1}{2} \sum_{j=1}^{T} \frac{G_{j}^{2}}{H_{j}+\lambda}+\gamma T\tag{12.6}
$$


容易计算出分裂前后损失函数的差值为：

$$
Gain=\frac{G_{L}^{2}}{H_{L}+\lambda}+\frac{G_{R}^{2}}{H_{R}+\lambda}-\frac{\left(G_{L}+G_{R}\right)^{2}}{H_{L}+H_{R}+\lambda}-\gamma\tag{12.7}
$$

XGBoost 采用最大化这个差值作为准则来进行决策树的构建，通过遍历所有特征的所有取值，寻找使得损失函数前后相差最大时对应的分裂方式。此外，由于损失函数前后存在差值一定为正的限制，此时 $\gamma$ 起到了一定的预剪枝效果。<span style="color:red;">没有明白。</span>


除了算法上与传统的 GBDT 有一些不同外，XGBoost 还在工程实现上做了大量的优化。<span style="color:red;">那些优化？</span>

总的来说，两者之间的区别和联系可以总结成以下几个方面。

1. GBDT 是机器学习算法，XGBoost 是该算法的工程实现。
2. 在使用 CART 作为基分类器时，XGBoost 显式地加入了正则项来控制模型的复杂度，有利于防止过拟合，从而提高模型的泛化能力。
3. GBDT 在模型训练时只使用了代价函数的一阶导数信息，XGBoost 对代价函数进行二阶泰勒展开，可以同时使用一阶和二阶导数。
4. 传统的 GBDT 采用 CART 作为基分类器，XGBoost 支持多种类型的基分类器，比如线性分类器。<span style="color:red;">这么厉害的吗？想对 XGBoost 有更多的理解。</span>
5. 传统的 GBDT 在每轮迭代时使用全部的数据，XGBoost 则采用了与随机森林相似的策略，支持对数据进行采样。
6. 传统的 GBDT 没有设计对缺失值进行处理，XGBoost 能够自动学习出缺失值的处理策略。<span style="color:red;">这么厉害的吗？</span>






# 相关

- 《百面机器学习》
