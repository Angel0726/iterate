---
title: 31 最大均值差异 MMD
toc: true
date: 2019-08-28
---
# 可以补充进来的

- 用处。

# 最大均值差异 MMD


最大均值差异

Maximum mean discrepancy MMD

最大均值差异是迁移学习中使用频率最高的度量。它度量在再生希尔伯特空间中两个分布的距离，是一种核学习方法。两个随机变量的 MMD 平方距离为


最大平均差异最先提出的时候用于双样本的检测（two-sample test）问题，用于判断两个分布 p 和 q 是否相同。它的基本假设是：如果对于所有以分布生成的样本空间为输入的函数 f，如果两个分布生成的足够多的样本在 f 上的对应的像的均值都相等，那么那么可以认为这两个分布是同一个分布。现在一般用于度量两个分布之间的相似性。

下面从任意空间到 RKHS 上介绍了 MMD 的计算。

## 任意函数空间（arbitary function space）的 MMD

具体而言，基于 MMD（maximize mean discrepancy）的统计检验方法是指下面的方式：基于两个分布的样本，通过寻找在样本空间上的连续函数 f，求不同分布的样本在 f 上的函数值的均值，通过把两个均值作差可以得到两个分布对应于 f 的 mean discrepancy。寻找一个 f 使得这个 mean discrepancy有最大值，就得到了 MMD。

最后取 MMD 作为检验统计量（test statistic），从而判断两个分布是否相同。如果这个值足够小，就认为两个分布相同，否则就认为它们不相同。同时这个值也用来判断两个分布之间的相似程度。如果用 F 表示一个在样本空间上的连续函数集，那么 MMD 可以用下面的式子表示：

$$
\mathrm{MMD}[\mathcal{F}, p, q] :=\sup _{f \in \mathcal{F}}\left(\mathbf{E}_{x \sim p}[f(x)]-\mathbf{E}_{y \sim q}[f(y)]\right)
$$


假设 X 和 Y 分别是从分布 p 和 q 通过独立同分布(iid)采样得到的两个数据集，数据集的大小分别为 m 和 n。基于 X 和 Y 可以得到 MMD 的经验估计(empirical estimate)为：

$$
\operatorname{MMD}[\mathcal{F}, X, Y] :=\sup _{f \in \mathcal{F}}\left(\frac{1}{m} \sum_{i=1}^{m} f\left(x_{i}\right)-\frac{1}{n} \sum_{i=1}^{n} f\left(y_{i}\right)\right)
$$

在给定两个分布的观测集 X,Y的情况下，这个结果会严重依赖于给定的函数集 F。为了能表示 MMD 的性质：当且仅当 p 和 q 是相同分布的时候 MMD 为 0，那么要求 F 足够 rich；另一方面为了使检验具有足够的连续性（be consistent in power），从而使得 MMD 的经验估计可以随着观测集规模增大迅速收敛到它的期望，F必须足够 restrictive。文中证明了当 F 是 universal RKHS上的（unit ball）单位球时，可以满足上面两个性质。

## 再生核希尔伯特空间的 MMD（The MMD In reproducing kernel Hilbert Spaces）：


这部分讲述了在 RHKS 上单位球（unit ball）作为 F 的时，通过有限的观测来对 MMD 进行估计，并且设立一些 MMD 可以用来区分概率度量的条件。
在 RKHS 上，每个 f 对应一个 feature map。在 feature map的基础上，首先对于某个分布 p 定义一个 mean embedding of p，它满足如下的性质：

$$
\mu_{p} \in \mathcal{H} \text { such that } \mathbf{E}_{x} f=\left\langle f, \mu_{p}\right\rangle_{\mathcal{H}} \text { for all } f \in \mathcal{H}
$$


mean embedding存在是有约束条件的。在 p 和 q 的 mean embedding存在的条件下，MMD的平方可以表示如下：

$$
\begin{aligned} \operatorname{MMD}^{2}[\mathcal{F}, p, q] &=\left[\sup _{\|f\|_{\mathcal{Y} \in 1}}\left(\mathbf{E}_{x}[f(x)]-\mathbf{E}_{y}[f(y)]\right)\right]^{2} \\ &=\left[\sup _{\|f\|_{\mathcal{Y} \in 1}}\left\langle\mu_{p}-\mu_{q}, f\right\rangle_{\mathcal{H}}\right]^{2} \\ &=\left\|\mu_{p}-\mu_{q}\right\|_{\mathcal{H}}^{2} \end{aligned}
$$

下面是关于 MMD 作为一个 Borel probability measures时，对 F 的一个约束及其证明，要求 F：be a unit ball in a universal RKHS。比如 Gaussian 和 Laplace RKHSs。进一步在给定了 RKHS 对应核函数，这个 MMD 的平方可以表示：

$$
\operatorname{MMD}^{2}[\mathcal{F}, p, q]=\mathbf{E}_{x, x^{\prime}}\left[k\left(x, x^{\prime}\right)\right]-2 \mathbf{E}_{x, y}[k(x, y)]+\mathbf{E}_{y, y^{\prime}}\left[k\left(y, y^{\prime}\right)\right]
$$

x和 x’分别表示两个服从于 p 的随机变量，y和 y‘分别表示服从 q 的随机变量。对于上面的一个统计估计可以表示为：

$$
\operatorname{MMD}[\mathcal{F}, X, Y]=\left[\frac{1}{m^{2}} \sum_{i, j=1}^{m} k\left(x_{i}, x_{j}\right)-\frac{2}{m n} \sum_{i, j=1}^{m, n} k\left(x_{i}, y_{j}\right)+\frac{1}{n^{2}} \sum_{i, j=1}^{n} k\left(y_{i}, y_{j}\right)\right]^{\frac{1}{2}}
$$


对于一个 two-sample test, 给定的 null hypothesis: p和 q 是相同，以及 the alternative hypothesis: p和 q 不等。这个通过将 test statistic和一个给定的阈值相比较得到，如果 MMD 大于阈值，那么就 reject null hypothesis，也就是两个分布不同。如果 MMD 小于某个阈值，就接受 null hypothesis。由于 MMD 的计算时使用的是有限的样本数，这里会出现两种类型的错误：第一种错误出现在 null hypothesis被错误的拒绝了；也就是本来两个分布相同，但是却被判定为相同。反之，第二种错误出现在 null hypothesis被错误的接受了。文章[1]中提供了许多关于 hypothesis test的方法，这里不讨论。

在 domain adaptation中，经常用到 MMD 来在特征学习的时候构造正则项来约束学到的表示，使得两个域上的特征尽可能相同。从上面的定义看，我们在判断两个分布 p 和 q 的时候，需要将观测样本首先映射到 RKHS 空间上，然后再判断。但实际上很多文章直接将观测样本用于计算，省了映射的那个步骤。


# 相关

- [迁移学习简明手册](https://github.com/jindongwang/transferlearning-tutorial)  [王晋东](https://zhuanlan.zhihu.com/p/35352154)
- [MMD ：maximum mean discrepancy(最大平均差异)](https://blog.csdn.net/xiaocong1990/article/details/72051375)
