---
title: 04 Gibbs 采样
toc: true
date: 2019-06-05
---

# Gibbs采样


目前为止我们已经了解了如何通过反复更新 $\boldsymbol x \xleftarrow{} \boldsymbol x'\sim T(\boldsymbol x'\mid\boldsymbol x)$ 从一个分布 $q(\boldsymbol x)$ 中采样。然而我们还没有介绍过如何确定 $q(\boldsymbol x)$ 是否是一个有效的分布。本书中将会描述两种基本的方法。第一种方法是从已经学习到的分布 $p_{\text{model}}$ 中推导出 $T$，下文描述了如何从基于能量的模型中采样。第二种方法是直接用参数描述 $T$，然后学习这些参数，其平稳分布隐式地定义了我们所感兴趣的模型 $p_{\text{model}}$。
我们将在\sec?和\sec?中讨论第二种方法的例子。



在深度学习中，我们通常使用马尔可夫链从定义为基于能量的模型的分布 $p_{\text{model}}(\boldsymbol x)$ 中采样。在这种情况下，我们希望马尔可夫链的 $q(\boldsymbol x)$ 分布就是 $p_{\text{model}}(\boldsymbol x)$。为了得到所期望的 $q(\boldsymbol x)$ 分布，我们必须选取合适的 $T(\boldsymbol x'\mid \boldsymbol x)$。



Gibbs采样是一种概念简单而又有效的方法。它构造一个从 $p_{\text{model}}(\boldsymbol x)$ 中采样的马尔可夫链，其中在基于能量的模型中从 $T(\mathbf x'\mid \mathbf x)$ 采样是通过选择一个变量 $\mathrm x_i$，然后从 $p_{\text{model}}$ 中该点关于在无向图 $\mathcal G$（定义了基于能量的模型结构）中邻接点的条件分布中采样。只要一些变量在给定相邻变量时是条件独立的，那么这些变量就可以被同时采样。正如在\sec?中看到的 RBM 示例一样，RBM 中所有的隐藏单元可以被同时采样，因为在给定所有可见单元的条件下它们相互条件独立。同样地，所有的可见单元也可以被同时采样，因为在给定所有隐藏单元的情况下它们相互条件独立。以这种方式同时更新许多变量的\,Gibbs采样通常被称为块吉布斯采样。


设计从 $p_{\text{model}}$ 中采样的马尔可夫链还存在其他备选方法。比如说，Metropolis-Hastings算法在其他领域中广泛使用。不过在深度学习的无向模型中，我们主要使用\,Gibbs采样，很少使用其他方法。改进采样技巧也是一个潜在的研究热点。





# 相关

- 《深度学习》花书
