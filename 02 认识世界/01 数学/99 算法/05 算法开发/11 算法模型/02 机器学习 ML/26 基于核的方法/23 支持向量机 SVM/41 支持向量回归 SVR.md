---
title: 41 支持向量回归 SVR
toc: true
date: 2018-07-31 18:06:17
---

# 可以补充进来的

- <span style="color:red;">看得云里雾里，说不清楚吧，每个字都还认识，说清楚吧，其实一团糊涂。</span>

# 支持向量回归


现在我们来考虑回归问题，给定训练样本 $D=\left\{\left(\boldsymbol{x}_{1}, y_{1}\right),\left(\boldsymbol{x}_{2}, y_{2}\right), \ldots\right.\left(\boldsymbol{x}_{m}, y_{m}\right) \}$， $y_{i} \in \mathbb{R}$ 。希望学得一个形如式(6.7)的回归模型，使得 $f(\boldsymbol{x})$ 与 $y$ 尽可能接近，$\boldsymbol{w}$ 和 $b$ 是待确定的模型参数。

对样本 $(\boldsymbol{x}, y)$ ，传统回归模型通常直接基于模型输出 $f(\boldsymbol{x})$ 与真实输出 $y$ 之间的差别来计算损失，当且仅当 $f(\boldsymbol{x})$ 与 $y$ 完全相同时，损失才为零。

与此不同，支持向量回归(Support Vector Regression，简称 SVR) 假设我们能容忍 $f(\boldsymbol{x})$ 与 $y$ 之间最多有 $\epsilon$ 的偏差，即仅当 $f(\boldsymbol{x})$ 与 $y$ 之间的差别绝对值大于 $\epsilon$ 时才计算损失。

如图 6.6所示，这相当于以为 $f(\boldsymbol{x})$ 中心，构建了一个宽度为 $2\epsilon$ 的间隔带，若训练样本落入此间隔带，则认为是被预测正确的.

<center>

![](http://images.iterate.site/blog/image/180627/eAKlA3BEmJ.png?imageslim){ width=55% }


</center>

于是，SVR 问题可形式化为

$$
\min _{\boldsymbol{w}, b} \frac{1}{2}\|\boldsymbol{w}\|^{2}+C \sum_{i=1}^{m} \ell_{\iota}\left(f\left(\boldsymbol{x}_{i}\right)-y_{i}\right)\tag{6.43}
$$

其中 C 为正则化常数，$\ell_{\epsilon}$ 是图 6.7所示的 $\epsilon$-不敏感损失($\epsilon$-insensitive loss) 函数。<span style="color:red;">嗯，是这样。</span>

$$
\ell_{\epsilon}(z)=\left\{\begin{array}{ll}{0,} & {\text { if }|z| \leqslant \epsilon} \\ {|z|-\epsilon,} & {\text { otherwise }}\end{array}\right.\tag{6.44}
$$

引入松弛变量 $\xi_i$ 和 $\hat{\xi}_i$ 可将式(6.43)重写为 <span style="color:red;">什么是松弛变量，到底为什么要引入？</span>



$$
\begin{array}{ll}{\min _{\boldsymbol{w}, b, \xi_{i}, \hat{\xi}_{i}}}& {\frac{1}{2}\|\boldsymbol{w}\|^{2}+C \sum_{i=1}^{m}\left(\xi_{i}+\hat{\xi}_{i}\right)}\\{\text { s.t. }}&{ f\left(\boldsymbol{x}_{i}\right)-y_{i} \leqslant \epsilon+\xi_{i}} \\ {}&{ \begin{array}{l}{y_{i}-f\left(\boldsymbol{x}_{i}\right) \leqslant \epsilon+\hat{\xi}_{i}} \\ {\xi_{i} \geqslant 0, \hat{\xi}_{i} \geqslant 0, i=1,2, \ldots, m}\end{array}}\end{array}\tag{6.45}
$$

<center>

![](http://images.iterate.site/blog/image/180627/Ke2LHdHL6D.png?imageslim){ width=55% }


</center>

类似式 (6.36)，通过引入拉格朗日乘子 $\mu_{i} \geqslant 0, \hat{\mu}_{i} \geqslant 0, \alpha_{i} \geqslant 0, \hat{\alpha}_{i} \geqslant 0$，由拉格朗日乘子法可得到式子 (6.45) 的拉格朗日函数：

$$
\begin{array}{l}{L(\boldsymbol{w}, b, \boldsymbol{\alpha}, \hat{\boldsymbol{\alpha}}, \boldsymbol{\xi}, \hat{\boldsymbol{\xi}}, \boldsymbol{\mu}, \hat{\boldsymbol{\mu}})} \\ {=\frac{1}{2}\|\boldsymbol{w}\|^{2}+C \sum_{i=1}^{m}\left(\xi_{i}+\hat{\xi}_{i}\right)-\sum_{i=1}^{m} \mu_{i} \xi_{i}-\sum_{i=1}^{m} \hat{\mu}_{i} \hat{\xi}_{i}} \\ {+\sum_{i=1}^{m} \alpha_{i}\left(f\left(\boldsymbol{x}_{i}\right)-y_{i}-\epsilon-\xi_{i}\right)+\sum_{i=1}^{m} \hat{\alpha}_{i}\left(y_{i}-f\left(\boldsymbol{x}_{i}\right)-\epsilon-\hat{\xi}_{i}\right)}\end{array}\tag{6.46}
$$

将式(6.7)代入，再令 $L(\boldsymbol{w}, b, \boldsymbol{\alpha}, \hat{\boldsymbol{\alpha}}, \boldsymbol{\xi}, \hat{\boldsymbol{\xi}}, \boldsymbol{\mu}, \hat{\boldsymbol{\mu}})$ 对 $\boldsymbol{w}$，$b$ ，$\xi_i$， $\hat{\xi}_i$ 的偏导为零可得：

$$
\boldsymbol{w}=\sum_{i=1}^{m}\left(\hat{\alpha}_{i}-\alpha_{i}\right) \boldsymbol{x}_{i}\tag{6.47}
$$
$$
0=\sum_{i=1}^{m}\left(\hat{\alpha}_{i}-\alpha_{i}\right)\tag{6.48}
$$
$$
C=\alpha_{i}+\mu_{i}\tag{6.49}
$$
$$
C=\hat{\alpha}_{i}+\hat{\mu}_{i}\tag{6.50}
$$

将式(6.47)-(6.S0)代入式(6.46)，即可得到 SVR 的对偶问题

$$
\begin{aligned} \max _{\boldsymbol{\alpha}, \hat{\alpha}} & \sum_{i=1}^{m} y_{i}\left(\hat{\alpha}_{i}-\alpha_{i}\right)-\epsilon\left(\hat{\alpha}_{i}+\alpha_{i}\right) \\ &-\frac{1}{2} \sum_{i=1}^{m} \sum_{j=1}^{m}\left(\hat{\alpha}_{i}-\alpha_{i}\right)\left(\hat{\alpha}_{j}-\alpha_{j}\right) \boldsymbol{x}_{i}^{\mathrm{T}} \boldsymbol{x}_{j} \\ \text { s.t. } & \sum_{i=1}^{m}\left(\hat{\alpha}_{i}-\alpha_{i}\right)=0 \\ 0 & \leqslant \alpha_{i}, \hat{\alpha}_{i} \leqslant C \end{aligned}\tag{6.51}
$$

上述过程中需满足 KKT 条件，即要求

$$
\left\{\begin{array}{l}{\alpha_{i}\left(f\left(\boldsymbol{x}_{i}\right)-y_{i}-\epsilon-\xi_{i}\right)=0} \\ {\hat{\alpha}_{i}\left(y_{i}-f\left(\boldsymbol{x}_{i}\right)-\epsilon-\hat{\xi}_{i}\right)=0} \\ {\alpha_{i} \hat{\alpha}_{i}=0, \xi_{i} \hat{\xi}_{i}=0} \\ {\left(C-\alpha_{i}\right) \xi_{i}=0,\left(C-\hat{\alpha}_{i}\right) \hat{\xi}_{i}=0}\end{array}\right.\tag{6.52}
$$


> $$
> \left\{\begin{array}{l}
> {\alpha_{i}\left(f\left(\boldsymbol{x}_{i}\right)-y_{i}-\epsilon-\xi_{i}\right)=0} \\ {\hat{\alpha}_{i}\left(y_{i}-f\left(\boldsymbol{x}_{i}\right)-\epsilon-\hat{\xi}_{i}\right)=0} \\ {\alpha_{i} \hat{\alpha}_{i}=0, \xi_{i} \hat{\xi}_{i}=0} \\
> {\left(C-\alpha_{i}\right) \xi_{i}=0,\left(C-\hat{\alpha}_{i}\right) \hat{\xi}_{i}=0}
> \end{array}\right.
> $$
> [推导]：
> 将式（6.45）的约束条件全部恒等变形为小于等于 0 的形式可得：
> $$
> \left\{\begin{array}{l}
> {f\left(\boldsymbol{x}_{i}\right)-y_{i}-\epsilon-\xi_{i} \leq 0 }  \\
> {y_{i}-f\left(\boldsymbol{x}_{i}\right)-\epsilon-\hat{\xi}_{i} \leq 0 } \\
> {-\xi_{i} \leq 0} \\
> {-\hat{\xi}_{i} \leq 0}
> \end{array}\right.
> $$
> 由于以上四个约束条件的拉格朗日乘子分别为 $\alpha_i,\hat{\alpha}_i,\mu_i,\hat{\mu}_i$，所以由西瓜书附录式（B.3）可知，以上四个约束条件可相应转化为以下 KKT 条件：
> $$
> \left\{\begin{array}{l}
> {\alpha_i\left(f\left(\boldsymbol{x}_{i}\right)-y_{i}-\epsilon-\xi_{i} \right) = 0 }  \\
> {\hat{\alpha}_i\left(y_{i}-f\left(\boldsymbol{x}_{i}\right)-\epsilon-\hat{\xi}_{i} \right) = 0 } \\
> {-\mu_i\xi_{i} = 0 \Rightarrow \mu_i\xi_{i} = 0 }  \\
> {-\hat{\mu}_i \hat{\xi}_{i} = 0  \Rightarrow \hat{\mu}_i \hat{\xi}_{i} = 0 }
> \end{array}\right.
> $$
> 由式（6.49）和式（6.50）可知：
> $$
> \begin{aligned}
> \mu_i=C-\alpha_i \\
> \hat{\mu}_i=C-\hat{\alpha}_i
> \end{aligned}
> $$
> 所以上述 KKT 条件可以进一步变形为：
> $$
> \left\{\begin{array}{l}
> {\alpha_i\left(f\left(\boldsymbol{x}_{i}\right)-y_{i}-\epsilon-\xi_{i} \right) = 0 }  \\
> {\hat{\alpha}_i\left(y_{i}-f\left(\boldsymbol{x}_{i}\right)-\epsilon-\hat{\xi}_{i} \right) = 0 } \\
> {(C-\alpha_i)\xi_{i} = 0 }  \\
> {(C-\hat{\alpha}_i) \hat{\xi}_{i} = 0 }
> \end{array}\right.
> $$
> 又因为样本 $(\boldsymbol{x}_i,y_i)$ 只可能处在间隔带的某一侧，那么约束条件 $f\left(\boldsymbol{x}_{i}\right)-y_{i}-\epsilon-\xi_{i}=0$ 和 $y_{i}-f\left(\boldsymbol{x}_{i}\right)-\epsilon-\hat{\xi}_{i}=0$ 不可能同时成立，所以 $\alpha_i$ 和 $\hat{\alpha}_i$ 中至少有一个为 0，也即 $\alpha_i\hat{\alpha}_i=0$。在此基础上再进一步分析可知，如果 $\alpha_i=0$ 的话，那么根据约束 $(C-\alpha_i)\xi_{i} = 0$ 可知此时 $\xi_i=0$，同理，如果 $\hat{\alpha}_i=0$ 的话，那么根据约束 $(C-\hat{\alpha}_i)\hat{\xi}_{i} = 0$ 可知此时 $\hat{\xi}_i=0$，所以 $\xi_i$ 和 $\hat{\xi}_i$ 中也是至少有一个为 0，也即 $\xi_{i} \hat{\xi}_{i}=0$。将 $\alpha_i\hat{\alpha}_i=0,\xi_{i} \hat{\xi}_{i}=0$ 整合进上述 KKT 条件中即可得到式（6.52）。



可以看出，当且仅当 $f\left(\boldsymbol{x}_{i}\right)-y_{i}-\epsilon-\xi_{i}=0$ 时 $\alpha_i$ 能取非零值，当且仅当 $y_{i}-f\left(\boldsymbol{x}_{i}\right)-\epsilon-\hat{\xi}_{i}=0$ 时 $\hat{\alpha}_i$ 能取非零值。换言之，仅当样本 $\left(\boldsymbol{x}_{i}, y_{i}\right)$ 不落入 $\epsilon$-间隔带中，相应的 $\alpha_i$ 和 $\hat{\alpha}_i$ 才能取非零值。

此外，约束 $f\left(\boldsymbol{x}_{i}\right)-y_{i}-\epsilon-\xi_{i}=0$ 和 $y_{i}-f\left(\boldsymbol{x}_{i}\right)-\epsilon-\hat{\xi}_{i}=0$ 不能同时成立，因此 $\alpha_i$ 和 $\hat{\alpha}_i$ 中至少有一个为零。

将式(6.47)代入(6.7)，则 SVR 的解形如：

$$
f(\boldsymbol{x})=\sum_{i=1}^{m}\left(\hat{\alpha}_{i}-\alpha_{i}\right) \boldsymbol{x}_{i}^{\mathrm{T}} \boldsymbol{x}+b\tag{6.53}
$$

能使式(6.53)中的 $(\hat{\alpha}_i-\alpha_i)\neq 0$ 的样本即为 SVR 的支持向量，它们必落在 $\epsilon$-间隔带之外。显然，SVR的支持向量仅是训练样本的一部分，即其解仍具有稀疏性。

由 KKT 条件(6.52)可看出，对每个样本 $\left(\boldsymbol{x}_{i}, y_{i}\right)$ 都有 $(C-\alpha_i)\xi_i=0$ 且 $\alpha_{i}\left(f\left(\boldsymbol{x}_{i}\right)-y_{i}-\epsilon-\xi_{i}\right)=0$ 。于是。于是，在得到 $\alpha_i$ 后，若 $0<\alpha_i<C$ ，则必有 $\xi_i=0$ ，进而有：

$$
b=y_{i}+\epsilon-\sum_{i=1}^{m}\left(\hat{\alpha}_{i}-\alpha_{i}\right) \boldsymbol{x}_{i}^{\mathrm{T}} \boldsymbol{x}\tag{6.54}
$$


因此，在求解式(6.51)得到 $\alpha_i$ 后，理论上来说，可任意选取满足 $0<\alpha_i<C$ 的样本通过式(6.54)求得 $b$。实践中常采用一种更鲁棒的办法：选取多个(或所有)满足条件  $0<\alpha_i<C$  的样本求解 $b$ 后取平均值。

若考虑特征映射形式(6.19)，则相应的，式(6.47)将形如

$$
\boldsymbol{w}=\sum_{i=1}^{m}\left(\hat{\alpha}_{i}-\alpha_{i}\right) \phi\left(\boldsymbol{x}_{i}\right)\tag{6.55}
$$

将式(6.55)代入(6.19)，则 SVR 可表示为

$$
f(\boldsymbol{x})=\sum_{i=1}^{m}\left(\hat{\alpha}_{i}-\alpha_{i}\right) \kappa\left(\boldsymbol{x}, \boldsymbol{x}_{i}\right)+b\tag{6.56}
$$

其中 $\kappa\left(\boldsymbol{x}_{i}, \boldsymbol{x}_{j}\right)=\phi\left(\boldsymbol{x}_{i}\right)^{\mathrm{T}} \phi\left(\boldsymbol{x}_{j}\right)$ 为核函数。




# 相关

- 《机器学习》
- [pumpkin-book](https://github.com/datawhalechina/pumpkin-book)
