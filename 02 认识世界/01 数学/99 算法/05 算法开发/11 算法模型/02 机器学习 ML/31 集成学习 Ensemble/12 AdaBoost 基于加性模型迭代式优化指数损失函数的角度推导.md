---
title: 12 AdaBoost 基于加性模型迭代式优化指数损失函数的角度推导
toc: true
date: 2018-08-21 18:16:23
---

# 基于加性模型迭代式优化指数损失函数的角度推导 AdaBoost


Boosting 族算法最著名的代表是 AdaBoost , 其描述如图 8.3所示，其中 $y_i\in\{-1,+1\}$ , $f$ 是真实函数.

<center>

![](http://images.iterate.site/blog/image/180628/L1e4dhGI9J.png?imageslim){ width=55% }


</center>

AdaBoost 算法有多种推导方式，比较容易理解的是基于“加性模型”(additive model)，即基学习器的线性组合

$$
H(\boldsymbol{x})=\sum_{t=1}^{T} \alpha_{t} h_{t}(\boldsymbol{x})\tag{8.4}
$$


来最小化指数损失函数(exponential loss function)

$$
\ell_{\exp }(H | \mathcal{D})=\mathbb{E}_{\boldsymbol{x} \sim \mathcal{D}}\left[e^{-f(\boldsymbol{x}) H(\boldsymbol{x})}\right]\tag{8.5}
$$

若 $H(\boldsymbol{x})$ 能令指数损失函数最小化，则考虑式(8.5)对 $H(\boldsymbol{x})$ 的偏导

$$
\frac{\partial \ell_{\exp }(H | \mathcal{D})}{\partial H(\boldsymbol{x})}=-e^{-H(\boldsymbol{x})} P(f(\boldsymbol{x})=1 | \boldsymbol{x})+e^{H(\boldsymbol{x})} P(f(\boldsymbol{x})=-1 | \boldsymbol{x})\tag{8.6}
$$

令式(8.6)为零可解得

$$
H(\boldsymbol{x})=\frac{1}{2} \ln \frac{P(f(x)=1 | \boldsymbol{x})}{P(f(x)=-1 | \boldsymbol{x})}\tag{8.7}
$$

因此，有
$$
\begin{aligned} \operatorname{sign}(H(\boldsymbol{x})) &=\operatorname{sign}\left(\frac{1}{2} \ln \frac{P(f(x)=1 | \boldsymbol{x})}{P(f(x)=-1 | \boldsymbol{x})}\right) \\ &=\left\{\begin{array}{ll}{1,} & {P(f(x)=1 | \boldsymbol{x})>P(f(x)=-1 | \boldsymbol{x})} \\ {-1,} & {P(f(x)=1 | \boldsymbol{x})<P(f(x)=-1 | \boldsymbol{x})}\end{array}\right.\\& =\underset{y \in\{-1,1\}}{\arg \max } P(f(x)=y | \boldsymbol{x})\end{aligned}\tag{8.8}
$$

> 8.5-8.8
> [推导]：由式(8.4)可知
> $$H(\boldsymbol{x})=\sum_{t=1}^{T} \alpha_{t} h_{t}(\boldsymbol{x})$$
>
> 又由式(8.11)可知
> $$
> \alpha_{t}=\frac{1}{2} \ln \left(\frac{1-\epsilon_{t}}{\epsilon_{t}}\right)
> $$
> 该分类器的权重只与分类器的错误率负相关(即错误率越大，权重越低)
>
> (1)先考虑指数损失函数 $e^{-f(x) H(x)}$ 的含义：$f$ 为真实函数，对于样本 $x$ 来说，$f(\boldsymbol{x}) \in\{-1,+1\}$ 只能取和两个值，而 $H(\boldsymbol{x})$ 是一个实数；
> 当 $H(\boldsymbol{x})$ 的符号与 $f(x)$ 一致时，$f(\boldsymbol{x}) H(\boldsymbol{x})>0$，因此 $e^{-f(\boldsymbol{x}) H(\boldsymbol{x})}=e^{-|H(\boldsymbol{x})|}<1$，且 $|H(\boldsymbol{x})|$ 越大指数损失函数 $e^{-f(\boldsymbol{x}) H(\boldsymbol{x})}$ 越小（这很合理：此时 $|H(\boldsymbol{x})|$ 越大意味着分类器本身对预测结果的信心越大，损失应该越小；若 $|H(\boldsymbol{x})|$ 在零附近，虽然预测正确，但表示分类器本身对预测结果信心很小，损失应该较大）；
> 当 $H(\boldsymbol{x})$ 的符号与 $f(\boldsymbol{x})$ 不一致时，$f(\boldsymbol{x}) H(\boldsymbol{x})<0$，因此 $e^{-f(\boldsymbol{x}) H(\boldsymbol{x})}=e^{|H(\boldsymbol{x})|}>1$，且 $| H(\boldsymbol{x}) |$ 越大指数损失函数越大（这很合理：此时 $| H(\boldsymbol{x}) |$ 越大意味着分类器本身对预测结果的信心越大，但预测结果是错的，因此损失应该越大；若 $| H(\boldsymbol{x}) |$ 在零附近，虽然预测错误，但表示分类器本身对预测结果信心很小，虽然错了，损失应该较小）；
> (2)符号 $\mathbb{E}_{\boldsymbol{x} \sim \mathcal{D}}[\cdot]$ 的含义：$\mathcal{D}$ 为概率分布，可简单理解为在数据集 $D$ 中进行一次随机抽样，每个样本被取到的概率；$\mathbb{E}[\cdot]$ 为经典的期望，则综合起来 $\mathbb{E}_{\boldsymbol{x} \sim \mathcal{D}}[\cdot]$ 表示在概率分布 $\mathcal{D}$ 上的期望，可简单理解为对数据集 $D$ 以概率 $\mathcal{D}$ 进行加权后的期望。
> $$
> \begin{aligned}
> \ell_{\mathrm{exp}}(H | \mathcal{D})=&\mathbb{E}_{\boldsymbol{x} \sim \mathcal{D}}\left[e^{-f(\boldsymbol{x}) H(\boldsymbol{x})}\right]
> \\ =&P(f(x)=1|x)*e^{-H(x)}+P(f(x)=-1|x)*e^{H(x)}
> \end{aligned}
> $$
>
> 由于 $P(f(x)=1|x)和 P(f(x)=-1|x)$ 为常数
>
> 故式(8.6)可轻易推知
>
> $$
> \frac{\partial \ell_{\exp }(H | \mathcal{D})}{\partial H(\boldsymbol{x})}=-e^{-H(\boldsymbol{x})} P(f(\boldsymbol{x})=1 | \boldsymbol{x})+e^{H(\boldsymbol{x})} P(f(\boldsymbol{x})=-1 | \boldsymbol{x})
> $$
>
> 令式(8.6)等于 0 可得
>
> 式(8.7)
> $$
> H(\boldsymbol{x})=\frac{1}{2} \ln \frac{P(f(x)=1 | \boldsymbol{x})}{P(f(x)=-1 | \boldsymbol{x})}
> $$
> 式(8.8)显然成立
> $$
> \begin{aligned}
> \operatorname{sign}(H(\boldsymbol{x}))&=\operatorname{sign}\left(\frac{1}{2} \ln \frac{P(f(x)=1 | \boldsymbol{x})}{P(f(x)=-1 | \boldsymbol{x})}\right)
> \\ & =\left\{\begin{array}{ll}{1,} & {P(f(x)=1 | \boldsymbol{x})>P(f(x)=-1 | \boldsymbol{x})} \\ {-1,} & {P(f(x)=1 | \boldsymbol{x})<P(f(x)=-1 | \boldsymbol{x})}\end{array}\right.
> \\ & =\underset{y \in\{-1,1\}}{\arg \max } P(f(x)=y | \boldsymbol{x})
> \end{aligned}
> $$

这意味着 $\operatorname{sign}(H(\boldsymbol{x}))$ 达到了贝叶斯最优错误率。换言之，若指数损失函数最小化，则分类错误率也将最小化；这说明指数损失函数是分类任务原本 $0/1$ 损失函数的一致的(consistent)替代损失函数。由于这个替代函数有更好的数学性质，例如它是连续可微函数，因此我们用它替代 $0/1$ 损失函数作为优化目标.

在 AdaBoost 算法中，第一个基分类器 $h_1$ 是通过直接将基学习算法用于初始数据分布而得；此后迭代地生成 $h_t$ 和 $\alpha_t$ ，当基分类器 $h_t$ 基于分布 $\mathcal{D}_t$ 产生后，该基分类器的权重 $\alpha_t$ 应使得  $\alpha_th_t$ 最小化指数损失函数

$$
\begin{aligned} \ell_{\exp }\left(\alpha_{t} h_{t} | \mathcal{D}_{t}\right) &=\mathbb{E}_{\boldsymbol{x} \sim \mathcal{D}_{t}}\left[e^{-f(\boldsymbol{x}) \alpha_{t} h_{t}(\boldsymbol{x})}\right] \\ &=\mathbb{E}_{\boldsymbol{x} \sim \mathcal{D}_{t}}\left[e^{-\alpha_{t} \mathbb{I}}\left(f(\boldsymbol{x})=h_{t}(\boldsymbol{x})\right)+e^{\alpha_{t}} \mathbb{I}\left(f(\boldsymbol{x}) \neq h_{t}(\boldsymbol{x})\right)\right] \\ &=e^{-\alpha_{t}} P_{\boldsymbol{x} \sim \mathcal{D}_{t}}\left(f(\boldsymbol{x})=h_{t}(\boldsymbol{x})\right)+e^{\alpha_{t}} P_{\boldsymbol{x} \sim \mathcal{D}_{t}}\left(f(\boldsymbol{x}) \neq h_{t}(\boldsymbol{x})\right) \\ &=e^{-\alpha_{t}}\left(1-\epsilon_{t}\right)+e^{\alpha_{t}} \epsilon_{t} \end{aligned}\tag{8.9}
$$

其中 $\epsilon_{t}=P_{\boldsymbol{x} \sim \mathcal{D}_{t}}\left(h_{t}(\boldsymbol{x}) \neq f(\boldsymbol{x})\right)$ 考虑指数损失函数的导数

$$
\frac{\partial \ell_{\exp }\left(\alpha_{t} h_{t} | \mathcal{D}_{t}\right)}{\partial \alpha_{t}}=-e^{-\alpha_{t}}\left(1-\epsilon_{t}\right)+e^{\alpha_{t}} \epsilon_{t}\tag{8.10}
$$

令式(8.10)为零可解得

$$
\alpha_{t}=\frac{1}{2} \ln \left(\frac{1-\epsilon_{t}}{\epsilon_{t}}\right)\tag{8.11}
$$

这恰是图 8.3中算法第 6 行的分类器权重更新公式.

AdaBoost算法在获得 $H_{t-1}$ 之后样本分布将进行调整，使下一轮的基学习器 $h_t$ 能纠正 $H_{t-1}$ 的一些错误。理想的 $h_t$ 能纠正 $H_{t-1}$ 的全部错误，即最小化

$$
\begin{aligned} \ell_{\exp }\left(H_{t-1}+h_{t} | \mathcal{D}\right) &=\mathbb{E}_{\boldsymbol{x} \sim \mathcal{D}}\left[e^{-f(\boldsymbol{x})\left(H_{t-1}(\boldsymbol{x})+h_{t}(\boldsymbol{x})\right)}\right] \\ &=\mathbb{E}_{\boldsymbol{x} \sim \mathcal{D}}\left[e^{-f(\boldsymbol{x}) H_{t-1}(\boldsymbol{x})} e^{-f(\boldsymbol{x}) h_{t}(\boldsymbol{x})}\right] \end{aligned}\tag{8.12}
$$

注意到 $f^{2}(\boldsymbol{x})=h_{t}^{2}(\boldsymbol{x})=1$ ，式(8.12)可使用 $e^{-f(\boldsymbol{x}) h_{t}(\boldsymbol{x})}$ 泰勒展式近似为

$$
\begin{aligned} \ell_{\exp }\left(H_{t-1}+h_{t} | \mathcal{D}\right) & \simeq \mathbb{E}_{\boldsymbol{x} \sim \mathcal{D}}\left[e^{-f(\boldsymbol{x}) H_{t-1}(\boldsymbol{x})}\left(1-f(\boldsymbol{x}) h_{t}(\boldsymbol{x})+\frac{f^{2}(\boldsymbol{x}) h_{t}^{2}(\boldsymbol{x})}{2}\right)\right] \\ &=\mathbb{E}_{\boldsymbol{x} \sim \mathcal{D}}\left[e^{-f(\boldsymbol{x}) H_{t-1}(\boldsymbol{x})}\left(1-f(\boldsymbol{x}) h_{t}(\boldsymbol{x})+\frac{1}{2}\right)\right] \end{aligned}\tag{8.13}
$$

于是，理想的基学习器



$$
\begin{aligned}h_{t}(\boldsymbol{x})&=\underset{h}{\arg \min } \ell_{\exp }\left(H_{t-1}+h | \mathcal{D}\right)\\&{=\underset{h}{\arg \min } \mathbb{E}_{\boldsymbol{x} \sim \mathcal{D}}\left[e^{-f(\boldsymbol{x}) H_{t-1}(\boldsymbol{x})}\left(1-f(\boldsymbol{x}) h(\boldsymbol{x})+\frac{1}{2}\right)\right]} \\& {=\underset{h}{\arg \max } \mathbb{E}_{\boldsymbol{x} \sim \mathcal{D}}\left[e^{-f(\boldsymbol{x}) H_{t-1}(\boldsymbol{x})} f(\boldsymbol{x}) h(\boldsymbol{x})\right]} \\ &{=\underset{\boldsymbol{h}}{\arg \max } \mathbb{E}_{\boldsymbol{x} \sim \mathcal{D}}\left[\frac{e^{-f(\boldsymbol{x}) H_{t-1}(\boldsymbol{x})}}{\mathbb{E}_{\boldsymbol{x} \sim \mathcal{D}}\left[e^{-f(\boldsymbol{x}) H_{t-1}(\boldsymbol{x})}\right]} f(\boldsymbol{x}) h(\boldsymbol{x})\right]}\end{aligned}\tag{8.14}
$$




注意到 $\mathbb{E}_{\boldsymbol{x} \sim \mathcal{D}}\left[e^{-f(\boldsymbol{x}) H_{t-1}(\boldsymbol{x})}\right]$ 是一个常数。令 $\mathcal{D}_{t}$ 表示一个分布

$$
\mathcal{D}_{t}(\boldsymbol{x})=\frac{\mathcal{D}(\boldsymbol{x}) e^{-f(\boldsymbol{x}) H_{t-1}(\boldsymbol{x})}}{\mathbb{E}_{\boldsymbol{x} \sim \mathcal{D}}\left[e^{-f(\boldsymbol{x}) H_{t-1}(\boldsymbol{x})}\right]}\tag{8.15}
$$

则根据数学期望的定义，这等价于令

$$
\begin{aligned} h_{t}(\boldsymbol{x}) &=\underset{h}{\arg \max } \mathbb{E}_{\boldsymbol{x} \sim \mathcal{D}}\left[\frac{e^{-f(\boldsymbol{x}) H_{t-1}(\boldsymbol{x})}}{\mathbb{E}_{\boldsymbol{x} \sim \mathcal{D}}\left[e^{-f(\boldsymbol{x}) H_{t-1}(\boldsymbol{x})}\right]} f(\boldsymbol{x}) h(\boldsymbol{x})\right] \\ &=\underset{\boldsymbol{h}}{\arg \max } \mathbb{E}_{\boldsymbol{x} \sim \mathcal{D}_{t}}[f(\boldsymbol{x}) h(\boldsymbol{x})] \end{aligned}\tag{8.16}
$$

> $$
> \begin{aligned} h_{t}(\boldsymbol{x}) &=\underset{h}{\arg \max } \mathbb{E}_{\boldsymbol{x} \sim \mathcal{D}}\left[\frac{e^{-f(\boldsymbol{x}) H_{t-1}(\boldsymbol{x})}}{\mathbb{E}_{\boldsymbol{x} \sim \mathcal{D}}\left[e^{-f(\boldsymbol{x}) H_{t-1}(\boldsymbol{x})}\right]} f(\boldsymbol{x}) h(\boldsymbol{x})\right] \\ &=\underset{\boldsymbol{h}}{\arg \max } \mathbb{E}_{\boldsymbol{x} \sim \mathcal{D}_{t}}[f(\boldsymbol{x}) h(\boldsymbol{x})] \end{aligned}
> $$
> [推导]：
> 假设 x 的概率分布是 f(x)
> (注:本书中概率分布全都是 $\mathcal{D(x)}$)
>
> $$
> \mathbb{E(g(x))}=\sum_{i=1}^{|D|}f(x)g(x)
> $$
> 故可得
>
> $$
> \mathbb{E}_{\boldsymbol{x} \sim \mathcal{D}}\left[e^{-f(\boldsymbol{x}) H(\boldsymbol{x})}\right]=\sum_{i=1}^{|D|} \mathcal{D}\left(\boldsymbol{x}_{i}\right) e^{-f\left(\boldsymbol{x}_{i}\right) H\left(\boldsymbol{x}_{i}\right)}
> $$
> 由式(8.15)可知
> $$
> \mathcal{D}_{t}\left(\boldsymbol{x}_{i}\right)=\mathcal{D}\left(\boldsymbol{x}_{i}\right) \frac{e^{-f\left(\boldsymbol{x}_{i}\right) H_{t-1}\left(\boldsymbol{x}_{i}\right)}}{\mathbb{E}_{\boldsymbol{x} \sim \mathcal{D}}\left[e^{-f(\boldsymbol{x}) H_{t-1}(\boldsymbol{x})}\right]}
> $$
>
> 所以式(8.16)可以表示为
> $$
> \begin{aligned} & \mathbb{E}_{\boldsymbol{x} \sim \mathcal{D}}\left[\frac{e^{-f(\boldsymbol{x}) H_{t-1}(\boldsymbol{x})}}{\mathbb{E}_{\boldsymbol{x} \sim \mathcal{D}}\left[e^{-f(\boldsymbol{x}) H_{t-1}(\boldsymbol{x})}\right]} f(\boldsymbol{x}) h(\boldsymbol{x})\right] \\=& \sum_{i=1}^{|D|} \mathcal{D}\left(\boldsymbol{x}_{i}\right) \frac{e^{-f\left(\boldsymbol{x}_{i}\right) H_{t-1}\left(\boldsymbol{x}_{i}\right)}}{\mathbb{E}_{\boldsymbol{x} \sim \mathcal{D}}\left[e^{-f(\boldsymbol{x}) H_{t-1}(\boldsymbol{x}) }]  \right.}f(x_i)h(x_i) \\=& \sum_{i=1}^{|D|} \mathcal{D}_{t}\left(\boldsymbol{x}_{i}\right) f\left(\boldsymbol{x}_{i}\right) h\left(\boldsymbol{x}_{i}\right) \\=& \mathbb{E}_{\boldsymbol{x} \sim \mathcal{D}_{t}}[f(\boldsymbol{x}) h(\boldsymbol{x})] \end{aligned}
> $$
>
> 【注】：由下式 $(*)$ 也可推至式(8.16)
>
> $$
> P(f(x)=1|x)e^{-H(x)}+P(f(x)=-1|x)e^{H(x)}(*)
> $$
>
> 首先式 $(*)$ 可以拆成 n 个式子,n的个数为 x 的取值个数
>
>
> $$
> P(f(x_i)=1|x_i)e^{-H(x_i)}+P(f(x_i)=-1|x_i)e^{H(x_i)}(i=1,2,...,n)(**)
> $$
>
> 当 $x_i$ 确定的时候
> $P(f(x_i=1|x_i))$ 与 $P(f(x_i=-1|x_i))$
> 其中有一个为 0，另一个为 1
>
> 则式 $(**)$ 可以化简成
> $$
> e^{-f(x_i)H(x_i)}(i=1,2,...,n)(***)
> $$
>
> 拆成 n 个式子是根据不同的 x 来拆分的，可以把 $x=x_i$ 看成一个事件，设为事件 $A_i$。
>
> 当事件 $A_i$ 发生时，事件 $A_j$ 一定不发生，即各事件互斥，而且各个事件发生的概率是 $P(A_i)=\mathcal{D}(x_i)$
>
> 此时可以考虑成原来的 x 被分成了 n 叉树，每个路径的概率是 $\mathcal{D}(x_i)$，叶子结点的值是 $e^{-f(x_i)H(x_i)}$ 相乘再相加即为期望，同式(8.16)



由 $f(\boldsymbol{x}), h(\boldsymbol{x}) \in\{-1,+1\}$ ，有

$$
f(\boldsymbol{x}) h(\boldsymbol{x})=1-2 \mathbb{I}(f(\boldsymbol{x}) \neq h(\boldsymbol{x}))\tag{8.17}
$$

则理想的基学习器

$$
h_{t}(\boldsymbol{x})=\underset{h}{\arg \min } \mathbb{E}_{\boldsymbol{x} \sim \mathcal{D}_{t}}[\mathbb{I}(f(\boldsymbol{x}) \neq h(\boldsymbol{x}))]\tag{8.18}
$$


由此可见，理想的 $h_t$ 将在分布 $\mathcal{D}_t$ 下蕞小化分类误差。因此，弱分类器将基于分布 $\mathcal{D}_t$ 来训练，且针对 $\mathcal{D}_t$ 的分类误差应小于 $0.5$。这在一定程度上类似 “残差逼近” 的思想。考虑到 $\mathcal{D}_t$ 和 $\mathcal{D}_{t+1}$ 的关系，有

$$
\begin{aligned} \mathcal{D}_{t+1}(\boldsymbol{x}) &=\frac{\mathcal{D}(\boldsymbol{x}) e^{-f(\boldsymbol{x}) H_{t}(\boldsymbol{x})}}{\mathbb{E}_{\boldsymbol{x} \sim \mathcal{D}}\left[e^{-f(\boldsymbol{x}) H_{t}(\boldsymbol{x})}\right]} \\ &=\frac{\mathcal{D}(\boldsymbol{x}) e^{-f(\boldsymbol{x}) H_{t-1}(\boldsymbol{x})} e^{-f(\boldsymbol{x}) \alpha_{t} h_{t}(\boldsymbol{x})}}{\mathbb{E}_{\boldsymbol{x} \sim \mathcal{D}}\left[e^{-f(\boldsymbol{x}) H_{t}(\boldsymbol{x})}\right]} \\ &=\mathcal{D}_{t}(\boldsymbol{x}) \cdot e^{-f(\boldsymbol{x}) \alpha_{t} h_{t}(\boldsymbol{x})} \frac{\mathbb{E}_{\boldsymbol{x} \sim \mathcal{D}}\left[e^{-f(\boldsymbol{x}) H_{t-1}(\boldsymbol{x})}\right]}{\mathbb{E}_{\boldsymbol{x} \sim \mathcal{D}}\left[e^{-f(\boldsymbol{x}) H_{t-1}(\boldsymbol{x})}\right]} \end{aligned}\tag{8.19}
$$



这恰是图 8.3 中算法第 7 行的样本分布更新公式.

于是，由式 (8.11) 和 (8.19) 可见，我们从基于加性模型迭代式优化指数损失函数的角度推导出了图 8.3 的 AdaBoost 算法.



# 相关

- 《机器学习》周志华
- [pumpkin-book](https://github.com/datawhalechina/pumpkin-book)
