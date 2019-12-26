---
title: 71 Wasserstein Distance
toc: true
date: 2019-08-28
---
# 可以补充进来的

- 学好实变和测度论对机器学习是很有帮助的。

# Wasserstein Distance



可以安全的把概率测度(probability measure)理解为概率分布(probability distribution)，只要我们关心的空间是 $\mathbb{R}^{n}$。两个概率分布之间的距离有很多种描述方式，一个比较脍炙人口的是 KL divergence:

$$
K L(p \| q)=\int_{\mathbb{R}^{n}} p(x) \log \frac{p(x)}{q(x)} d x
$$

尽管它严格意义上不是一个距离(比如不满足对称性)。从定义可以看出，KL并不关心 $\mathbb{R}^{n}$ 的几何性质，因为 $p$ 和 $q$ 的比较都是在同一点进行的(换句话说，只要 $x_{1} \neq x_{2}$，KL并不 care $p\left(x_{1}\right) / q\left(x_{2}\right)$ 的大小)。举个例子，考虑如下两个一维高斯分布：$\mathrm{p}=\mathcal{N}\left(0, \epsilon^{3}\right)$ 和 $q=\mathcal{N}\left(\epsilon, \epsilon^{3}\right)$ ，借蛮力可算出 $K L(p \| q)=\frac{1}{2 \epsilon}$ 。q只是 p 的一个微小平移，但当平移量趋于 0 时，KL却 blow up了！

这就激励我们定义一种分布间的距离，使其能够把 $\mathbb{R}^{n}$ 的几何/度量性质也考虑进去。Wasserstein distance就做到了这一点，而且是高调的做到了这一点，因为 $d(x, y)$ 显式的出现在了定义中。具体的，对于定义在 $\mathbb{R}^{n}$ 上的概率分布 $\mu$ 和 $\nu$，

$$
W_{p}(\mu, \nu) :=\inf _{\gamma \in \Gamma(\mu, \nu)}\left(\int_{\mathbb{R}^{n} \times \mathbb{R}^{n}} d(x, y)^{p} d \gamma(x, y)\right)^{1 / p}=\left(\inf _{\xi} \mathbf{E}\left[d(x, y)^{p}\right]\right)^{1 / p}
$$

其中 $\xi$ 是一个 $\mathbb{R}^{n} \times \mathbb{R}^{n}$ 上的联合分布，必须同时满足 $\mu$ 和 $\nu$ 是其边缘分布。$d$ 可以是 $\mathbb{R}^{n}$ 上的任意距离，比如欧式距离，$L_{1}$ 距离等等。举个特例，当 $\mu=\delta_{x}$ 和 $\nu=\delta_{y}$ 时，唯一符合条件的 $\xi$ 只有 $\delta_{(x, y)}$，所以 $W_{p}(\mu, \nu)=d(x, y)$ ，两个 delta 分布间的距离正好等于它们中心间的距离，非常的符号直觉对不对！(因为建立了 $\mathbb{R}^{n}$ 和 delta 分布之间的 isometry)

刚才的例子也告诉我们，Wasserstein distance是可以定义两个 support 不重合，甚至一点交集都没有的分布之间的距离的，而 KL 在这种情况并不适用。

维基中也给出了两个正态分布的 Wasserstein distance ($p=2$ 时候) 的公式，大家可以去看一下，正好是两部分的和，一部分代表了中心间的几何距离，另一部分代表了两个分布形状上的差异。现在返回去看上面 KL 时候举的那个例子，它们之间的 Wasserstein distance正好是 $\epsilon$。

实际应用中 Wasserstein distance的计算大都依赖离散化，因为目前只对有限的几个分布存在解析解。对于任意分布 $\mu$ 我们可以用 delta 分布来逼近 $\mu \approx \frac{1}{n} \sum_{i=1}^{n} \delta_{x_{i}}$，这里并不要求 $x_i$ 是唯一的。对于 $\nu$ 做同样的近似 $\nu \approx \frac{1}{n} \sum_{i=1}^{n} \delta_{y_{i}}$。为什么 $\mu$ 和 $\nu$ 的近似能够取相同的 $n$？因为总是可以把当前的近似点拷贝几份然后 renormalize，所以取 $n$ 为两者原始近似点数量的最小公倍数即可。那么

$$
W_{p}(\mu, \nu) \approx W_{p}\left(\frac{1}{n} \sum_{i=1}^{n} \delta_{x_{i}}, \frac{1}{n} \sum_{i=1}^{n} \delta_{y_{i}}\right)=\min _{\sigma \in S_{n}}\left(\sum_{i=1}^{n} d\left(x_{\sigma_{i}}, y_{i}\right)^{p}\right)^{1 / p}
$$

这就变成了一个组合优化的问题：有 $n$ 个位于 $x_{i}$ 的石子，大自然借助水的力量将它们冲到的新的位置 $y_i$。就像光总是走最短路一样，大自然总是选取最短的路径来移动这些石子。这些石子总共移动的距离等于 Wasserstein distance。

## 定义

Wasserstein Distance是一套用来衡量两个概率分部之间距离的度量方法。该距离在一个度量空间 $(M,\rho)$ 上定义，其中 $\rho(x,y)$ 表示集合 $M$ 中两个实例 $x$ 和 $y$ 的距离函数，比如欧几里得距离。两个概率分布 $\mathbb{P}$ 和 $\mathbb{Q}$ 之间的 $p{\text{-th} }$ Wasserstein distance可以被定义为

$$
W_p(\mathbb{P}, \mathbb{Q}) = \Big(\inf_{\mu \in \Gamma(\mathbb{P}, \mathbb{Q}) } \int \rho(x,y)^p d\mu(x,y) \Big)^{1/p},
$$

其中 $\Gamma(\mathbb{P}, \mathbb{Q})$ 是在集合 $M\times M$ 内所有的以 $\mathbb{P}$ 和 $\mathbb{Q}$ 为边缘分布的联合分布。著名的 Kantorovich-Rubinstein定理表示当 $M$ 是可分离的时候，第一 Wasserstein distance可以等价地表示成一个积分概率度量(integral probability metric)的形式

$$
W_1(\mathbb{P},\mathbb{Q})= \sup_{\left \| f \right \|_L \leq 1} \mathbb{E}_{x \sim \mathbb{P} }[f(x)] - \mathbb{E}_{x \sim \mathbb{Q} }[f(x)],
$$

其中 $\left \| f \right \|_L = \sup{|f(x) - f(y)|} / \rho(x,y)$ 并且 $\left \| f \right \|_L \leq 1$ 称为 $1-$ 利普希茨条件。







# 相关

- [迁移学习简明手册](https://github.com/jindongwang/transferlearning-tutorial)  [王晋东](https://zhuanlan.zhihu.com/p/35352154)
- [wasserstein 距离的问题?](https://www.zhihu.com/question/41752299/answer/147394973)
