---
title: 01 初识 GANs 的秘密
toc: true
date: 2019-04-21
---
# 可以补充进来的

- 真的挺好的。需要多看下，多补充下。多理解下。


# 初识 GANs 的秘密


2014 年来自加拿大蒙特利尔大学的年轻博士生 Ian Goodfellow 和他的导师 Yoshua Bengio 提出一个叫 GANs 的模型[32]。Facebook AI 实验室主任 Yann LeCun 称该模型是机器学习领域近十年最具创意的想法。


把 GANs 想象成造假币者与警察间展开的一场猫捉老鼠游戏，造假币者试图造出以假乱真的假币，警察试图发现这些假币，对抗使二者的水平都得到提高。从造假币到合成模拟图片，道理是一样的。

下面关于 GANs，从基础理论到具体模型，再到实验设计，我们依次思考如下几个问题。<span style="color:red;">嗯。</span>

MiniMax游戏，值函数（Value Function），JS距离（Jensen- Shannon Divergence），概率生成模型，优化饱和 <span style="color:red;">什么是 JS 距离？</span>



## 简述 GANs 的基本思想和训练过程。

GANs的主要框架如图 13.1所示：

<center>

![](http://images.iterate.site/blog/image/20190421/0IUR0IjMzgJO.png?imageslim){ width=55% }

</center>

包括两个部分：

- 生成器（Generator）：生成器用于合成 “假” 样本
- 判别器（Discriminator）：判别器用于判断输入的样本是真实的还是合成的

具体来说：

- 生成器从先验分布中采得随机信号，经过神经网络的变换，得到模拟样本；
- 判别器既接收来自生成器的模拟样本，也接收来自实际数据集的真实样本，但我们并不告诉判别器样本来源，需要它自己判断。

生成器和判别器是一对“冤家”，置身于对抗环境中，生成器尽可能造出样本迷惑判别器，而判别器则尽可能识别出来自生成器的样本。

然而，对抗不是目的，在对抗中让双方能力各有所长才是目的。理想情况下，生成器和判别器最终能达到一种平衡，双方都臻于完美，彼此都没有更进一步的空间。<span style="color:red;">有些厉害。到底要怎么实现呢？</span>



GANs 采用对抗策略进行模型训练：

- 一方面，生成器通过调节自身参数，使得其生成的样本尽量难以被判别器识别出是真实样本还是模拟样本；
- 另一方面，判别器通过调节自身参数，使得其能尽可能准确地判别出输入样本的来源。<span style="color:red;">嗯，只是判断输入样本的来源是吧？而不是为了判定这个样本是不是很像真实的样本。</span>


具体训练时，采用生成器和判别器交替优化的方式：


（1）在训练判别器时，先固定生成器 $G(\cdot)$；然后利用生成器随机模拟产生样本 $G(z)$ 作为负样本（ $z$ 是一个随机向量），并从真实数据集中采样获得正样本 $X$ ；将这些正负样本输入到判别器 $G(\cdot)$ 中，根据判别器的输出（即 $D(X)$ 或 $D(G(z))$ ）和样本标签来计算误差；最后利用误差反向传播算法来更新判别器 $G(\cdot)$ 的参数，如图 13.2所示。<span style="color:red;">是的，还是很好理解的，生成器生成的不管有多像，都是负样本。</span>

<center>

![](http://images.iterate.site/blog/image/20190421/kRSaymHrvbP4.png?imageslim){ width=55% }

</center>


（2）在训练生成器时，先固定判别器 $D(\cdot)$ ；然后利用当前生成器 $G(\cdot)$ 随机模拟产生样本 $G(z)$ ，并输入到判别器 $D(\cdot)$ 中；根据判别器的输出 $D(G(z))$ 和样本标签来计算误差，最后利用误差反向传播算法来更新生成器 $G(\cdot)$ 的参数，如图 13.3所示：<span style="color:red;">嗯。</span>

<center>

![](http://images.iterate.site/blog/image/20190421/F16eyi6twi55.png?imageslim){ width=55% }

</center>


## GANs 的值函数。


GANs 是一个双人 MiniMax 游戏，请给出游戏的值函数。

理想情况下游戏最终会达到一个纳什均衡点，此时记生成器为 $G^{*}$，判别器为 $D^{*}$，请给出此时的解 $\left(G^{*}, D^{*}\right)$ ，以及对应的值函数的取值。<span style="color:red;">之前听说过纳什均衡，补充到这里来。</span>

在未达到均衡点时，将生成器 $G$ 固定，寻找当下最优的判别器 $D_{G}^{*}$ ，请给出 $D_{G}^{*}$ 和此时的值函数。

上述问题的答案在 Goodfellow 的论文中都有回答，进一步地，倘若固定 $D$ 而将 $G$ 优化到底，那么解 $G_{D}^{*}$ 和此时的值函数又揭示出什么呢？


分析与解答


### 值函数

因为判别器 $D$ 试图识别实际数据为真实样本，识别生成器生成的数据为模拟样本，所以这是一个二分类问题，损失函数写成 Negative Log-Likelihood，也称 Categorical Cross-Entropy Loss，即：，

$$
\mathcal{L}(D)=-\int p(x)[p(\operatorname{data} | x) \log D(x)+p(g | x) \log (1-D(x))] \mathrm{d} x\tag{13.1}
$$

其中：

- $D(x)$ 表示判别器预测 $x$ 为真实样本的概率，
- $p($ data| $x)$ 和 $p(g | x)$ 表示 $x$ 分属真实数据集和生成器这两类的概率。样本 $x$ 的来源一半是实际数据集，一半是生成器，$p_{\mathrm{src}}(d a t a)=p_{\mathrm{src}}(g)=0.5$ 。<span style="color:red;">一定是 0.5 吗？</span>


我们用 $p_{\text { data }}(x) \doteq p(x | d a t a)$ 表示从实际数据集得到 $x$ 的概率，$p_{g}(x) \doteq p(x | g)$ 表示从生成器得到 $x$ 的概率，则有 $x$ 的总概率：<span style="color:red;">没看懂。</span>

$$
p(x)=p_{\mathrm{src}}(d a t a) p(x | d a t a)+p_{\mathrm{src}}(g) p(x | g)\tag{13.2}
$$


替换式（13.1）中的 $p(x) p(data | x)$ 为 $p_{\mathrm{src}}(data) p_{\text { data }}(x)$ ，以及 $p(x) p(g | x)$ 为 $p_{\mathrm{src}}(g) p_{g}(x)$ ，即可得到最终的目标函数：<span style="color:red;">有些理解，又有些不理解。</span>

$$
\mathcal{L}(D)=-\frac{1}{2}\left(\mathbb{E}_{x \sim p_{\text { data }}(x)}[\log D(x)]+\mathbb{E}_{x \sim p_{g}(x)}[\log (1-D(x))]\right)\tag{13.3}
$$


在此基础上得到值函数：

$$
V(G, D)=\mathbb{E}_{x \sim p_{\text { data }}(x)}[\log D(x)]+\mathbb{E}_{x \sim p_{g}(x)}[\log (1-D(x))]\tag{13.4}
$$

判别器 $D$ 最大化上述值函数，生成器 $G$ 则最小化它，整个 MiniMax 游戏（见图 13.4）可表示为 $\min _{G} \max _{D} V(G, D)$。

### 最优判别器

<span style="color:red;">没看懂</span>

训练中，给定生成器 $G$，寻找当下最优判别器 $D_{G}^{*}$ 。对于单个样本 $x$，最大化 $\max _{D} p_{\mathrm{data}}(x) \log D(x)+p_{g}(x) \log (1-D(x))$ 的解为 $\hat{D}(x)=p_{\text { data }}(x) /\left[p_{\text { data }}(x)+p_{g}(x)\right]$ ，外面套上对 $x$ 的积分就得到 $\max _{D} V(G, D)$ ，解由单点变成一个函数解：

$$
D_{G}^{*}=\frac{p_{\text { data }}}{p_{\text { data }}+p_{g}}\tag{13.5}
$$

此时，$\min _{G} V\left(G, D_{G}^{*}\right)=\min _{G}\left\{-\log 4+2 \cdot \operatorname{JSD}\left(p_{\text { data }} \| p_{g}\right)\right\}$ ，其中 $J S D(\cdot)$ 是 JS 距离。

由此看出，优化生成器 $G$ 实际是在最小化生成样本分布与真实样本分布的 JS 距离。最终，达到的均衡点是 $J S D\left(p_{\text { data }} \| p_{g}\right)$ 的最小值点，即 $p_{g}=p_{\text { data }}$ 时，$\operatorname{JSD}\left(p_{\text { data }} \| p_{g}\right)$ 取到零，最优解 $G^{*}(z)=x \sim p_{\mathrm{data}}(x)$ ，$D^{*}(x) \equiv \frac{1}{2}$，值函数 $V\left(G^{*}, D^{*}\right)=-\log 4$ 。

### 最优生成器

进一步地，训练时如果给定 D 求解最优 G，可以得到什么？不妨假设 $G′$ 表示前一步的生成器，D 是 $G′$ 下的最优判别器 $D_{G^{\prime}}^{*}$。那么，求解最优 G 的过程为：

$$
\underset{G}{\arg \min } V\left(G, D_{G^{\prime}}^{*}\right)=\underset{G}{\arg \min } K L\left(p_{g} \| \frac{p_{\mathrm{data}}+p_{g^{\prime}}}{2}\right)-K L\left(p_{g} \| p_{g^{\prime}}\right)\tag{13.6}
$$

由此，可以得出以下两点结论。

1. 优化 G 的过程是让 G 远离前一步的 G′，同时接近分布 $\left(p_{\mathrm{data}}+p_{g^{\prime}}\right) / 2$ 。
2. 达到均衡点时 $p_{g^{\prime}}=p_{\mathrm{data}}$ ，有 $\arg \min _{G} V\left(G, D_{G^{\prime}}^{*}\right)=\underset{G}{\arg \min } 0$ ，如果用这时的判别器去训练一个全新的生成器 $G_{\text { new }}$ ，理论上可能啥也训练不出来。

## GANs如何避开大量概率推断计算？

<span style="color:red;">理解的不深。</span>

发明 GANs 的初衷是为了更好地解决概率生成模型的估计问题。<span style="color:red;">是这样吗？</span>

传统概率生成模型方法（如：马尔可夫随机场、贝叶斯网络）会涉及大量难以完成的概率推断计算，GANs是如何避开这类计算的？


传统概率生成模型要定义一个概率分布表达式 $P(X)$ ，通常是一个多变量联合概率分布的密度函数 $p\left(X_{1}, X_{2}, \ldots, X_{N}\right)$ ，并基于此做最大似然估计。这过程少不了概率推断计算，比如计算边缘概率 $P(X_i)$ 、条件概率 $P\left(X_{i} | X_{j}\right)$ 以及作分母的 Partition Function 等。

当随机变量很多时，概率模型会变得十分复杂，概率计算变得非常困难，即使做近似计算，效果常不尽人意。

GANs在刻画概率生成模型时，并不对概率密度函数 $p(X)$ 直接建模，而是通过制造样本 $x$，间接体现出分布 $p(X)$，就是说我们看不到 $p(X)$ 的一个表达式。那么怎么做呢？<span style="color:red;">嗯。</span>

如果随机变量 $Z$ 和 $X$ 之间满足某种映射关系 $X=f(Z)$ ，那么它们的概率分布 $p_{X}(X)$ 和 $p_{Z}(Z)$ 也存在某种映射关系：

- 当￼ $Z$，$X$ 都是一维随机变量时，$p_{X}=\frac{\mathrm{d} f(Z)}{\mathrm{d} X} p_{Z}$ ；
- 当 $Z$，$X$ 是高维随机变量时，导数变成雅克比矩阵，即 $p_{X}=J p_{Z}$ 。

因此，已知 $Z$ 的分布，我们对随机变量间的转换函数 $f$ 直接建模，就唯一确定了 $X$ 的分布。<span style="color:red;">嗯。雅克比矩阵还是要补充下的。</span>

这样，不仅避开大量复杂的概率计算，而且给 $f$ 更大的发挥空间，我们可以用神经网络来训练 $f$。近些年神经网络领域大踏步向前发展，涌现出一批新技术来优化网络结构，除了经典的卷积神经网络和循环神经网络，还有 ReLu 激活函数、批量归一化、Dropout 等，都可以自由地添加到生成器的网络中，大大增强生成器的表达能力。<span style="color:red;">嗯。</span>

## GANs在实际训练中会遇到什么问题？

<span style="color:red;">再看下，有些不是特别理解。尤其是这些数学符号。</span>


实验中训练 GANs 会像描述的那么完美吗？最小化目标函数 $\mathbb{E}_{z \sim p(z)}\left[\log \left(1-D\left(G\left(z ; \theta_{g}\right)\right)\right]\right.$ 求解 $G$ 会遇到什么问题？你有何解决方案？

解答与分析

在实际训练中，早期阶段生成器 $G$ 很差，生成的模拟样本很容易被判别器 $D$ 识别，使得 $D$ 回传给 $G$ 的梯度极其小，达不到训练目的，这个现象称为优化饱和[33]。

为什么会这样呢？我们将 $D$ 的 Sigmoid 输出层的前一层记为 $o$，那么 $D(x)$ 可表示成  $D(x)=Sigmoid (o(x))$ ，此时有：

$$
\nabla D(x)=\nabla \operatorname{Sigmoid}(o(x))=D(x)(1-D(x)) \nabla o(x)\tag{13.7}
$$

因此训练 $G$ 的梯度为：

$$
\nabla \log \left(1-D\left(G\left(z ; \theta_{g}\right)\right)\right)=-D\left(G\left(z ; \theta_{g}\right)\right) \nabla o\left(G\left(z ; \theta_{g}\right)\right)\tag{13.8}
$$

当 $D$ 很容易认出模拟样本时，意味着认错模拟样本的概率几乎为零，即 $D\left(G\left(z ; \theta_{g}\right)\right) \rightarrow 0$。假定 $\left|\nabla o\left(G\left(z ; \theta_{g}\right)\right)\right|<C$ ， $C$ 为一个常量，则可推出：


$$
\begin{aligned}\lim _{D\left(G\left(z ; \theta_{g}\right)\right) \rightarrow 0} \nabla \log \left(1-D\left(G\left(z ; \theta_{g}\right)\right)\right)&=-\lim _{D\left(G\left(z ; \theta_{g}\right)\right) \rightarrow 0} D\left(G\left(z ; \theta_{g}\right)\right) \nabla o\left(G\left(z ; \theta_{g}\right)\right.\\&=0\end{aligned}
$$

故 $G$ 获得的梯度基本为零，这说明 $D$ 强大后对 $G$ 的帮助反而微乎其微。<span style="color:red;">嗯，是的。</span>

怎么办呢？解决方案是将 $\log \left(1-D\left(G\left(z ; \theta_{g}\right)\right)\right)$ 变为 $\log \left(D\left(G\left(z ; \theta_{g}\right)\right)\right)$ ，形式上有一个负号的差别，故让后者最大等效于让前者最小，二者在最优时解相同。我们看看更改后的目标函数有什么样的梯度：


$$
\nabla \log \left(D\left(G\left(z ; \theta_{g}\right)\right)\right)=\left(1-D\left(G\left(z ; \theta_{g}\right)\right)\right) \nabla o\left(G\left(z ; \theta_{g}\right)\right)\tag{13.10}
$$

$$
\lim _{D\left(G\left(z ; \theta_{g}\right))\rightarrow 0\right.} \nabla \log \left(D\left(G\left(z ; \theta_{g}\right)\right)\right)=\nabla o\left(G\left(z ; \theta_{g}\right)\right)\tag{13.11}
$$




即使 $D\left(G\left(z ; \theta_{g}\right)\right)$ 趋于零，$\nabla \log \left(D\left(G\left(z ; \theta_{g}\right)\right)\right)$ 也不会消失，仍能给生成器提供有效的梯度。




# 相关

- 《百面机器学习》
