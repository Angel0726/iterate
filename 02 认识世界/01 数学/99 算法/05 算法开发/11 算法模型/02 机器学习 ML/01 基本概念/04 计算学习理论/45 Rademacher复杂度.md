---
title: 45 Rademacher复杂度
toc: true
date: 2018-07-30 11:03:09
---
# 可以补充进来的

# Rademacher 复杂度

VC 维中提到，基于 VC 维的泛化误差界是分布无关、数据独立的，也就是说，对任何数据分布都成立。这使得基于 VC 维的可学习性分析结果具有一定 的“普适性”；但从另一方面来说，由于没有考虑数据自身，基于 VC 维得到 的泛化误差界通常比较“松”，对那些与学习问题的典型情况相差甚远的较 “坏”分布来说尤其如此。

Rademacher复杂度(Rademacher complexity)是另一种刻画假设空间复 杂度的途径，与 VC 维不同的是，它在一定程度上考虑了数据分布。

给定训练集 $D=\left\{\left(\boldsymbol{x}_{1}, y_{1}\right),\left(\boldsymbol{x}_{2}, y_{2}\right), \ldots,\left(\boldsymbol{x}_{m}, y_{m}\right)\right\}$ ，假设 $h$ 的经验误差为

$$
\begin{aligned} \widehat{E}(h) &=\frac{1}{m} \sum_{i=1}^{m} \mathbb{I}\left(h\left(\boldsymbol{x}_{i}\right) \neq y_{i}\right) \\ &=\frac{1}{m} \sum_{i=1}^{m} \frac{1-y_{i} h\left(\boldsymbol{x}_{i}\right)}{2} \\ &=\frac{1}{2}-\frac{1}{2 m} \sum_{i=1}^{m} y_{i} h\left(\boldsymbol{x}_{i}\right) \end{aligned}\tag{12.36}
$$

其中 $\frac{1}{m} \sum_{i=1}^{m} y_{i} h\left(\boldsymbol{x}_{i}\right)$ 体现了预测值 $h\left(\boldsymbol{x}_{i}\right)$ 与样例真实标记 $y_{i}$ 之间的一致性，若对于所有 $i \in\{1,2, \ldots, m\}$ 都有 $h\left(\boldsymbol{x}_{i}\right)=y_{i}$ ，则 $\frac{1}{m} \sum_{i=1}^{m} y_{i} h\left(\boldsymbol{x}_{i}\right)$ 取最大值 $1$。也就是说，经验误差最小的假设是

$$
\underset{h \in \mathcal{H}}{\arg \max } \frac{1}{m} \sum_{i=1}^{m} y_{i} h\left(\boldsymbol{x}_{i}\right)\tag{12.37}
$$

然而，现实任务中样例的标记有时会受到噪声影响，即对某些样例 $\left(\boldsymbol{x}_{i}, y_{i}\right)$ 其 $y_{i}$ 或许已受到随机因素的影响，不再是 $\boldsymbol{x}_{i}$ 的真实标记。在此情形下，选择假 设空间 $\mathcal{H}$ 中在训练集上表现最好的假设，有时还不如选择 $\mathcal{H}$ 中事先已考虑了 随机噪声影响的假设。

考虑随机变量 $\sigma_{i}$ ，它以 0.5的概率取值 $-1$，0.5的概率取值 $+1$，称为 Rademacher 随机变量。基于 $\sigma_{i}$ 可将式(12.37)重写为

$$
\sup _{h \in \mathcal{H}} \frac{1}{m} \sum_{i=1}^{m} \sigma_{i} h\left(\boldsymbol{x}_{i}\right)\tag{12.38}
$$

考虑  $\mathcal{H}$  中的所有假设:对式(12.38)取期望可得

$$
\mathbb{E}_{\boldsymbol{\sigma}}\left[\sup _{h \in \mathcal{H}} \frac{1}{m} \sum_{i=1}^{m} \sigma_{i} h\left(\boldsymbol{x}_{i}\right)\right]\tag{12.39}
$$

其中 $\boldsymbol{\sigma}=\left\{\sigma_{1}, \sigma_{2}, \ldots, \sigma_{m}\right\}$。式(12.39)的取值范围是［0，1］，它体现了假设空间 $\mathcal{H}$ 的表达能力，例如，当 $|\mathcal{H}|=1$ 时， $\mathcal{H}$ 中仅有一个假设，这时可计算出 式(12.39)的值为 0；当 $|\mathcal{H}|=2^{m}$ 且 $\mathcal{H}$ 能打散 $D$ 时，对任意 $\boldsymbol{\sigma}$ 总有一个假设使 得 $h\left(\boldsymbol{x}_{i}\right)=\sigma_{i} \quad(i=1,2, \ldots, m)$ ，这时可计算出式(l2.39)的值为 $1$。

考虑实值函数空间 $\mathcal{F} : \mathcal{Z} \rightarrow \mathbb{R}$ 。令 $Z=\left\{\boldsymbol{z}_{1}, \boldsymbol{z}_{2}, \ldots, \boldsymbol{z}_{m}\right\}$ ，其中 $\boldsymbol{z}_{i} \in \mathcal{Z}$ ，将 式(12.39)中的 $\mathcal{X}$ 和 $\mathcal{H}$ 替换为 $\mathcal{Z}$ 和 $\mathcal{F}$ 可得

**定义 12.8** 函数空间 $\mathcal{F}$ 关于 $Z$ 的经验 Rademacher 复杂度

$$
\widehat{R}_{Z}(\mathcal{F})=\mathbb{E}_{\boldsymbol{\sigma}}\left[\sup _{f \in \mathcal{F}} \frac{1}{m} \sum_{i=1}^{m} \sigma_{i} f\left(\boldsymbol{z}_{i}\right)\right]\tag{12.40}
$$

经验 Rademacher 复杂度衡量了函数空间 $\mathcal{F}$ 与随祖噪声在集合 $Z$ 中的相 关性。通常我们希望了解函数空间 $\mathcal{F}$ 在 $\mathcal{Z}$ 上关于分布 $\mathcal{D}$ 的相关性，因此，对所 有从 $\mathcal{D}$ 独立同分布采样而得的大小为 $m$ 的集合 $Z$ 求期望可得

**定义 12.9** 函数空间 $\mathcal{F}$ 关于 $\mathcal{Z}$ 上分布  $\mathcal{D}$ 的 Rademacher 复杂度

$$
R_{m}(\mathcal{F})=\mathbb{E}_{Z \subseteq \mathcal{Z} :|Z|=m}\left[\widehat{R}_{Z}(\mathcal{F})\right]\tag{12.41}
$$

基于 Rademacher 复杂度可得关于函数空间  $\mathcal{F}$  的泛化误差界［Mohri et al., 2012］:

定理 12.5 对实值函数空间 $\mathcal{F} : \mathcal{Z} \rightarrow[0,1]$ ，根据分布 $\mathcal{D}$ 从  $\mathcal{Z}$ 中独立同分布采样得到示例集 $Z=\left\{\boldsymbol{z}_{1}, \boldsymbol{z}_{2}, \ldots, \boldsymbol{z}_{m}\right\}$ , $\boldsymbol{z}_{i} \in \mathcal{Z}$, $0<\delta<1$，对任意 $f \in \mathcal{F}$ ，以至少 $1-\delta$ 的概率有

$$
\mathbb{E}[f(\boldsymbol{z})] \leqslant \frac{1}{m} \sum_{i=1}^{m} f\left(\boldsymbol{z}_{i}\right)+2 R_{m}(\mathcal{F})+\sqrt{\frac{\ln (1 / \delta)}{2 m}}\tag{12.42}
$$
$$
\mathbb{E}[f(\boldsymbol{z})] \leqslant \frac{1}{m} \sum_{i=1}^{m} f\left(\boldsymbol{z}_{i}\right)+2 \widehat{R}_{Z}(\mathcal{F})+3 \sqrt{\frac{\ln (2 / \delta)}{2 m}}\tag{12.43}
$$



证明令

$$
\widehat{E}_{Z}(f)=\frac{1}{m} \sum_{i=1}^{m} f\left(\boldsymbol{z}_{i}\right)
$$

$$
\Phi(Z)=\sup _{f \in \mathcal{F}} \mathbb{E}[f]-\widehat{E}_{Z}(f)
$$

同时，令 $Z^{\prime}$ 为只与 $Z$ 有一个示例不同的训练集，不妨设 $\boldsymbol{z}_{m} \in Z$ 和 $\boldsymbol{z}_{m}^{\prime} \in Z^{\prime}$ 为不同示例，可得：


$$
\begin{aligned} \Phi\left(Z^{\prime}\right)-\Phi(Z) &=\left(\sup _{f \in \mathcal{F}} \mathbb{E}[f]-\widehat{E}_{Z^{\prime}}(f)\right)-\left(\sup _{f \in \mathcal{F}} \mathbb{E}[f]-\widehat{E}_{Z}(f)\right) \\ & \leqslant \sup _{f \in \mathcal{F}} \widehat{E}_{Z}(f)-\widehat{E}_{Z^{\prime}}(f) \\ &=\sup _{f \in \mathcal{F}} \frac{f\left(z_{m}\right)-f\left(z_{m}^{\prime}\right)}{m} \\ & \leqslant \frac{1}{m} \end{aligned}
$$


同理可得

$$
\Phi(Z)-\Phi\left(Z^{\prime}\right) \leqslant \frac{1}{m}
$$
$$
\left|\Phi(Z)-\Phi\left(Z^{\prime}\right)\right| \leqslant \frac{1}{m}
$$


根据 McDiarmid 不等式(12.7)可知，对任意 $\delta \in(0,1)$：

$$
\Phi(Z) \leqslant \mathbb{E}_{Z}[\Phi(Z)]+\sqrt{\frac{\ln (1 / \delta)}{2 m}}\tag{12.44}
$$



以至少 $1-\delta$ 的概率成立。下面来估计 $\mathbb{E}_{Z}[\Phi(Z)]$ 的上界:


$$
\begin{aligned} \mathbb{E}_{Z}[\Phi(Z)] &=\mathbb{E}_{Z}\left[\sup _{f \in \mathcal{F}} \mathbb{E}[f]-\widehat{E}_{Z}(f)\right] \\ &=\mathbb{E}_{Z}\left[\sup _{f \in \mathcal{F}} \mathbb{E}_{Z^{\prime}}\left[\widehat{E}_{Z^{\prime}}(f)-\widehat{E}_{Z}(f)\right]\right] \\ & \leqslant \mathbb{E}_{Z, Z^{\prime}}\left[\sup _{f \in \mathcal{F}} \widehat{E}_{Z^{\prime}}(f)-\widehat{E}_{Z}(f)\right] \\&{=\mathbb{E}_{Z, Z^{\prime}}\left[\sup _{f \in \mathcal{F}} \frac{1}{m} \sum_{i=1}^{m}\left(f\left(\boldsymbol{z}_{i}^{\prime}\right)-f\left(\boldsymbol{z}_{i}\right)\right)\right]}\\&{=\mathbb{E}_{\boldsymbol{\sigma}, Z, Z^{\prime}}\left[\sup _{f \in \mathcal{F}} \frac{1}{m} \sum_{i=1}^{m} \sigma_{i}\left(f\left(\boldsymbol{z}_{i}^{\prime}\right)-f\left(\boldsymbol{z}_{i}\right)\right)\right]}\\&{ \leqslant \mathbb{E}_{\boldsymbol{\sigma}, Z^{\prime}}\left[\sup _{f \in \mathcal{F}} \frac{1}{m} \sum_{i=1}^{m} \sigma_{i} f\left(\boldsymbol{z}_{i}^{\prime}\right)\right]+\mathbb{E}_{\boldsymbol{\sigma}, Z}\left[\sup _{f \in \mathcal{F}} \frac{1}{m} \sum_{i=1}^{m}-\sigma_{i} f\left(\boldsymbol{z}_{i}\right)\right]}\\&{=2 \mathbb{E}_{\boldsymbol{\sigma}, Z}\left[\sup _{f \in \mathcal{F}} \frac{1}{m} \sum_{i=1}^{m} \sigma_{i} f\left(\boldsymbol{z}_{i}\right)\right]}\\&{=2 R_{m}(\mathcal{F})}\end{aligned}
$$



至此，式(12.42)得证。由定义 12.9可知，改变 $Z$ 中的一个示例对 $\widehat{R}_{Z}(\mathcal{F})$ 的值所 造成的改变最多为 1$/ m$ 。由 McDiarmid 不等式(12.7)可知，

$$
R_{m}(\mathcal{F}) \leqslant \widehat{R}_{Z}(\mathcal{F})+\sqrt{\frac{\ln (2 / \delta)}{2 m}}\tag{12.45}
$$

以至少 $1-\delta / 2$ 的概率成立。再由式(12.44)可知，

$$
\Phi(Z) \leqslant \mathbb{E}_{Z}[\Phi(Z)]+\sqrt{\frac{\ln (2 / \delta)}{2 m}}
$$

以至少 $1-\delta / 2$ 的概率成立。于是，

$$
\Phi(Z) \leqslant 2 \widehat{R}_{Z}(\mathcal{F})+3 \sqrt{\frac{\ln (2 / \delta)}{2 m}}\tag{12.46}
$$

以至少 $1-\delta$ 的概率成立。至此，式(12.43)得证。

需注意的是，定理 12.5中的函数空间 $\mathcal{F}$ 是区间 $[0,1]$ 上的实值函数，因此定理 12.5只适用于回归问题。对二分类问题，我们有下面的定理：


**定理 12.6** 对假设空间 $\mathcal{H} : \mathcal{X} \rightarrow\{-1,+1\}$ ，根据分布 $\mathcal{D}$ 从 $\mathcal{X}$ 中独立同分布采样得到示例集 $D=\left\{\boldsymbol{x}_{1}, \boldsymbol{x}_{2}, \ldots, \boldsymbol{x}_{m}\right\}$,$\boldsymbol{x}_{i} \in \mathcal{X}$, $0<\delta<1$，对任意 $h \in \mathcal{H}$，以至少 $1-\delta$ 的概率有

$$
E(h) \leqslant \widehat{E}(h)+R_{m}(\mathcal{H})+\sqrt{\frac{\ln (1 / \delta)}{2 m}}\tag{12.47}
$$
$$
E(h) \leqslant \widehat{E}(h)+\widehat{R}_{D}(\mathcal{H})+3 \sqrt{\frac{\ln (2 / \delta)}{2 m}}\tag{12.48}
$$

**证明** 对二分类问题的假设空间 $\mathcal{H}$ ，令 $\mathcal{Z}=\mathcal{X} \times\{-1,+1\}$ ，则 $\mathcal{H}$ 中的假设 $h$ 变形为

$$
f_{h}(\boldsymbol{z})=f_{h}(\boldsymbol{x}, y)=\mathbb{I}(h(\boldsymbol{x}) \neq y)\tag{12.49}
$$

于是就可将值域为 $\{-1,+1\}$ 的假设空间 $\mathcal{H}$ 转化为值域为 $[0,1]$ 的函数空间 $\mathcal{F}_{\mathcal{H}}=\left\{f_{h} : h \in \mathcal{H}\right\}$。由定义 12.8，有


$$
\begin{aligned} \widehat{R}_{Z}\left(\mathcal{F}_{\mathcal{H}}\right) &=\mathbb{E}_{\boldsymbol{\sigma}}\left[\sup _{f_{h} \in \mathcal{F}_{\mathcal{H}}} \frac{1}{m} \sum_{i=1}^{m} \sigma_{i} f_{h}\left(\boldsymbol{x}_{i}, y_{i}\right)\right] \\ &=\mathbb{E}_{\boldsymbol{\sigma}}\left[\sup _{h \in \mathcal{H}} \frac{1}{m} \sum_{i=1}^{m} \sigma_{i} \mathbb{I}\left(h\left(\boldsymbol{x}_{i}\right) \neq y_{i}\right)\right] \\ &=\mathbb{E}_{\boldsymbol{\sigma}}\left[\sup _{\boldsymbol{h} \in \mathcal{H}} \frac{1}{m} \sum_{i=1}^{m} \sigma_{i} \frac{1-y_{i} h\left(\boldsymbol{x}_{i}\right)}{2}\right] \\&{=\frac{1}{2} \mathbb{E}_{\boldsymbol{\sigma}}\left[\frac{1}{m} \sum_{i=1}^{m} \sigma_{i}+\sup _{h \in \mathcal{H}} \frac{1}{m} \sum_{i=1}^{m}\left(-y_{i} \sigma_{i} h\left(\boldsymbol{x}_{i}\right)\right)\right]}\\&{=\frac{1}{2} \mathbb{E}_{\boldsymbol{\sigma}}\left[\sup _{h \in \mathcal{H}} \frac{1}{m} \sum_{i=1}^{m}\left(-y_{i} \sigma_{i} h\left(\boldsymbol{x}_{i}\right)\right)\right]}\\&{=\frac{1}{2} \mathbb{E}_{\boldsymbol{\sigma}}\left[\sup _{h \in \mathcal{H}} \frac{1}{m} \sum_{i=1}^{m}\left(\sigma_{i} h\left(\boldsymbol{x}_{i}\right)\right)\right]}\\&{=\frac{1}{2} \widehat{R}_{D}(\mathcal{H})}\end{aligned}\tag{12.50}
$$


对式(12.50)求期望后可得

$$
R_{m}\left(\mathcal{F}_{\mathcal{H}}\right)=\frac{1}{2} R_{m}(\mathcal{H})\tag{12.51}
$$

由定理 12.5和式(12.50)~(12.51)，定理 12.6得证。


定理 12.6给出了基于 Rademacher 复杂度的泛化误差界。与定理 12.3对比 可知，基于 VC 维的泛化误差界是分布无关、数据独立的，而基于 Rademacher 复杂度的泛化误差界(12.47)与分布 $\mathcal{D}$ 有关，式(12.48)与数据 $D$ 有关。换言之， 基于 Rademacher 复杂度的泛化误差界依赖于具体学习何题上的数据分布，有 点类似于为该学习问题“量身定制”的，因此它通常比基于 VC 维的泛化误差 界更紧一些。

值得一提的是，关于 Rademacher 复杂度与増长函数，有如下定理：

证明过程参阅［Mohri et al., 2012】。


**定理 12.7** 假设空间 $\mathcal{H}$ 的 Rademacher 复杂度 $R_{m}(\mathcal{H})$ 与增长函数 满足

$$
R_{m}(\mathcal{H}) \leqslant \sqrt{\frac{2 \ln \Pi_{\mathcal{H}}(m)}{m}}\tag{12.52}
$$

由式(12.47)，(12.52)和推论 12.2可得


$$
E(h) \leqslant \widehat{E}(h)+\sqrt{\frac{2 d \ln \frac{e m}{d}}{m}}+\sqrt{\frac{\ln (1 / \delta)}{2 m}}\tag{12.53}
$$

也就是说，我们从 Rademacher 复杂度和增长函数能推导出基于 VC 维的泛化误差界。

> 给定函数空间 $F_{1}, F_{2}$，证明 $Rademacher$ 复杂度:
> $$
> R_{m}\left(F_{1}+F_{2}\right) \leq R_{m}\left(F_{1}\right)+R_{m}\left(F_{2}\right)
> $$
> [推导]：
> $$
> R_{m}\left(F_{1}+F_{2}\right)=E_{Z \in \mathbf{z} :|Z|=m}\left[\hat{R}_{Z}\left(F_{1}+F_{2}\right)\right]
> $$
>
> $$
> \hat{R}_{Z}\left(F_{1}+F_{2}\right)=E_{\sigma}\left[\sup _{f_{1} F_{1}, f_{2} \in F_{2}} \frac{1}{m} \sum_{i}^{m} \sigma_{i}\left(f_{1}\left(z_{i}\right)+f_{2}\left(z_{i}\right)\right)\right]
> $$
>
> 当 $f_{1}\left(z_{i}\right) f_{2}\left(z_{i}\right)<0$ 时，
> $$
> \sigma_{i}\left(f_{1}\left(z_{i}\right)+f_{2}\left(z_{i}\right)\right)<\sigma_{i 1} f_{1}\left(z_{i}\right)+\sigma_{i 2} f_{2}\left(z_{i}\right)
> $$
> 当 $f_{1}\left(z_{i}\right) f_{2}\left(z_{i}\right) \geq 0$ 时，
> $$
> \sigma_{i}\left(f_{1}\left(z_{i}\right)+f_{2}\left(z_{i}\right)\right)=\sigma_{i 1} f_{1}\left(z_{i}\right)+\sigma_{i 2} f_{2}\left(z_{i}\right)
> $$
> 所以：
> $$
> \hat{R}_{Z}\left(F_{1}+F_{2}\right) \leq \hat{R}_{Z}\left(F_{1}\right)+\hat{R}_{Z}\left(F_{2}\right)
> $$
> 也即：
> $$
> R_{m}\left(F_{1}+F_{2}\right) \leq R_{m}\left(F_{1}\right)+R_{m}\left(F_{2}\right)
> $$

# 相关

- 《机器学习》周志华
- [pumpkin-book](https://github.com/datawhalechina/pumpkin-book)
