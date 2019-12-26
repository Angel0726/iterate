---
title: 06 SeqGAN 生成文本序列
toc: true
date: 2019-04-26
---
# 需要补充下

- 没有怎么看懂，再看下。


# SeqGAN 生成文本序列

我们已探讨了用 GANs 生成离散数据。在有限点构成的离散空间中，每个样本是一个最小且不可分的点，不考虑点内部构造。

因此，上节 IRGAN 模型中“生成”二字的含义，是从一群点中挑出一些点，是全集生成子集的过程。信息检索的生成模型，在给定查询词后，从文档集中找出最相关的文档，一个文档就是一个最小单位的点，至于文档用了哪些词，它并不关心。<span style="color:red;">嗯。</span>


很多时候，我们想得到更多细节，比如：写出一篇文章、一段话或一句话，而不只是从文档集中选出文章。一个是“选”的任务，一个是“写”的任务，二者差别很大。

以句子为例，选句子是把句子看成一个点，写句子是把句子看成一个个字。假设你手里有一本《英语 900 句》，你把它背得滚瓜烂熟。当让你 “活用 900 句” 时，是指针对特定场景，从 900 句中挑选最恰当的一句话；当让你 “模仿 900 句造句” 时，是指不拘泥于书中原句，可替换名词动词、改变时态等，造出书中没有的句子，此时你不能将句子看成点，不能死记硬背，需开启创造性思维，切入句子结构来选词造句。<span style="color:red;">是的。</span>

生成一句话的过程，是生成文字序列的过程，序列中每个字是最小的基本单位。也就是说，句子是离散的，词也是离散的，生成器要做的不是 “选” 句子，而是 “选” 词 “写” 句子。<span style="color:red;">嗯，厉害。</span>


2017 年一个名为 SeqGAN 的模型被提出[43]，用来解决 GANs 框架下生成文本序列的问题，进一步拓展 GANs 的适用范围。该文章借鉴了强化学习理论中的策略和动作值函数，将生成文字序列的过程看成一系列选词的决策过程，GANs 的判别器在决策完毕时为产生的文字序列打分，作为影响决策的奖励[44]。<span style="color:red;">哈哈，好吧。</span>


知识点

循环神经网络，LSTM/GRU，语言模型（Language Model），奖励/长期奖励，策略梯度，动作值函数

## 如何构建生成器，生成文字组成的序列来表示句子？

假定生成器产生一个定长（记 $T$ ）的句子，即文字序列 $Y_{1 : T}=\left(y_{1}, y_{2}, \ldots, y_{T}\right),y_{i} \in \mathcal{Y}$，其中 $y_{i}$ 表示一个词，$\mathcal{Y}$ 表示词库。

一般地，序列建模采用 RNN 框架（见图 13.18），具体单元可以是 LSTM 或 GRU，甚至带上注意力机制。

序列建模 LSTM 框架：
<center>

![](http://images.iterate.site/blog/image/20190426/RB6dySnh2BGi.png?imageslim){ width=55% }

</center>


$$
h_{t}=g\left(h_{t-1}, x_{t}\right)\tag{13.28}
$$

$$
p\left(\bullet | x_{1}, \ldots, x_{t}\right)=z_{t}\left(h_{t}\right)=\operatorname{Softmax}\left(W h_{t}+c\right)\tag{13.29}
$$

$$
y_{t} \sim p\left(\bullet | x_{1}, \ldots, x_{t}\right)\tag{13.30}
$$


上面式子刻画了 RNN 的第 $t$ 步，$h_{t-1}$ 表示前一步的隐状态，$x_{t}$ 采用前一步生成词 $y_{t-1}$ 的表示向量，$x_{t}$ 和 $h_{t-1}$ 共同作为 $g$ 的输入，计算当前步的隐状态 $h_{t}$。

如果 $g$ 是一个 LSTM 单元，隐状态要包含一个用作记忆的状态。隐状态 $h_t$ 是一个 $d$ 维向量，经线性变换成为一个 $|Y|$ 维向量，再经过一个 Softmax 层，计算出选择词的概率分布 $z_t$，并采样一个词 $y_t$ 。概率分布 $z_t$ 是一个条件概率 $p\left(y_{t} | x_{1}, \ldots, x_{t}\right)$ ，因为 $x_t$ 为词 $y_{t−1}$ 的表示向量，$x_1$ 作为一个空字符或 RNN 头输入暂忽略，所以条件概率写成 $p\left(y_{t} | y_{1}, \dots, y_{t-1}\right)$ ，进而生成文字序列 $Y_{1 : T}$ 的概率为：


$$
p\left(Y_{1 : T}\right)=p\left(y_{1}, y_{2}, \ldots, y_{T}\right)=p\left(y_{1}\right) p\left(y_{2} | y_{1}\right) \ldots p\left(y_{T} | y_{1}, \ldots, y_{T-1}\right)\tag{13.31}
$$

实际上，RNN 每个单元的输出就是联合概率分解后的各个条件概率，根据每个条件概率挑选一个词，依次进行，最终得到一个长度为 $T$ 的句子。





## 训练序列生成器的优化目标通常是什么？GANs 框架下有何不同？


GANs 框架下有一个生成器 $G_{\theta}$ 和一个判别器 $D_{\varphi}$。

对于本问题，生成器的目标是生成文字序列，高度地模仿真实句子；判别器的目标是区分哪些是生成器给的句子，哪些是真实数据集中挑的句子。

通俗地讲，就是机器模仿人造句，一方面要让模仿尽可能像，一方面要辨认哪些是机器说的、哪些是人说的。

前者工作由生成器负责，后者工作则交给判别器，生成器的工作成果要受到判别器的评判。判别器的优化目标为：

$$
\max _{\phi} \mathbb{E}_{Y \sim p_{\text { data }}}\left[\log D_{\phi}(Y)\right]+\mathbb{E}_{Y \sim G_{\theta}}\left[\log \left[1-D_{\phi}(Y)\right]\right]\tag{13.32}
$$

这和原 GANs 中判别器的优化目标一样。

如果没有 GANs，生成器是一个普通的序列生成器，通常会采取什么样的优化目标来训练它？

熟悉语言模型的人，会想到最大似然估计，即：

$$
\max _{\theta} \sum_{i=1}^{n} \log p\left(Y_{1 : T}^{(i)} ; \theta\right)\tag{13.33}
$$


这需要有一份真实数据集，$Y_{1 : T}^{(i)}=\left(y_{1}^{(i)}, y_{2}^{(i)}, \ldots, y_{T}^{(i)}\right)$ 表示数据集中第 $i$ 个句子，生成器要最大化生成它们的总概率。

从数据集到句子，可假设句子独立同分布，但是从句子到词，词与词在一句话内有强依赖性，不能假定它们相互独立，必须依链式法则做概率分解，最终得到：<span style="color:red;">考虑的真到位。</span>

$$
\max _{\theta} \sum_{i=1}^{n} \log p\left(y_{1}^{(i)} ; \theta\right)+\ldots+\log p\left(y_{T}^{(i)}\left| y_{1}^{(i)}, \ldots, y_{T-1}^{(i)} ; \theta\right)\right.\tag{13.34}
$$

转变为最大化一个个对数条件概率之和。

GANs 框架下，生成器的优化目标不再是一个可拆解的联合概率，在与判别器的博弈中，以假乱真欺骗判别器才是生成器的目标。

判别器的评判针对一个完整句子，生成器欲知判别器的打分，必须送上整句话，不能在生成一半时就拿到判别器打分，故不能像最大似然估计那样拆解目标式，转为每个词的优化。<span style="color:red;">是的。</span>

而且，训练生成器时，也要训练判别器，对二者的训练交替进行。

固定判别器，生成器的优化目标为：

$$
\min _{\theta} \mathbb{E}_{Y \sim G_{\theta}}\left[\log \left(1-D_{\phi}(Y)\right)\right]\tag{13.35}
$$

表面上看，这与原 GANs 中生成器的优化目标一样，问题在于生成器输出的是离散样本，一个由离散词组成的离散句子，不像原 GANs 中生成图片，每个像素都是一个连续值。原 GANs 用重参数化技巧构造生成器，直接对采样过程建模，不去显性刻画样本概率分布。

上一节的 IRGAN，生成器生成文档序号 $d$ 这类离散数据，不能用原 GANs 的重参数化技巧。离散数据的特性，让我们无法求解目标函数对 $d$、$d$ 对生成器参数 $\theta$ 的梯度。而且，期望 $\mathbb{E}_{d \sim G_{\theta}}$ 的下脚标里包含参数 $\theta$ ，需对期望求梯度 $\nabla_{\theta} \mathbb{E}_{d-G_{\theta}}[\bullet]$，不得不显式写出概率函数 $p(d | q ; \theta)$。


在 SeqGAN 中，生成器生成的文本序列更离散，序列每个元素都是离散的，如图 13.19 所示。

联想强化学习理论，可把生成序列的过程看成是一连串动作，每步动作是挑选一个词，即动作 $a_{t}=y_{t}$ ，每步状态为已挑选词组成的序列前缀，即状态 $S_{t}=\left(y_{1}, \dots, y_{t-1}\right)$ ，最后一步动作后得到整个序列 $\left(y_{1}, y_{2}, \ldots, y_{T}\right)$ 。接着，判别器收到一个完整句子，判断是真是假并打分，这个分数就是生成器的奖励。训练生成器就是要最大化奖励期望，优化目标为：，


$$
\max _{\theta} \mathbb{E}_{Y_{1: T} \sim G_{\theta}}\left[-\log \left(1-D_{\phi}\left(Y_{1 : T}\right)\right)\right]\tag{13.36}
$$


或梯度增强版的，


$$
\max _{\theta} \mathbb{E}_{Y_{1: T} \sim G_{\theta}}\left[\log D_{\phi}\left(Y_{1 : T}\right)\right]\tag{13.37}
$$


其中 $\log D_{\phi}\left(Y_{1: T}\right)$ 就是生成器的奖励。



强化学习里有两个重要概念，策略和动作值函数：

- 前者记 $G_{\theta}(a | s)=p(a | s ; \theta)$ ，表示状态 $s$ 下选择动作 $a$ 的概率，体现模型根据状态做决策的能力；
- 后者记 $Q^{\theta}(s,a)$，表示状态 $s$ 下做动作 $a$ 后，根据策略 $G_{\theta}$ 完成后续动作获得的总奖励期望。结合本例，前 $T-1$ 个词已选的状态下选第 $T$ 个词的 $Q^{\theta}(s, a)$ 为：


$$
Q^{\theta}\left(s=Y_{1 : T-1}, a=y_{T}\right)=\log D_{\phi}\left(Y_{1 : T}\right)\tag{13.38}
$$


总奖励期望为：


$$
\mathbb{E}_{Y_{1:T} \sim G_{\theta}}\left[Q^{\theta}\left(Y_{1 : T-1}, y_{T}\right)\right]=\sum_{y_{1}} G_{\theta}\left(y_{1} | s_{0}\right) \ldots \sum_{y_{T}} G_{\theta}\left(y_{T} | Y_{1 : T-1}\right) Q^{\theta}\left(Y_{1 : T-1}, y_{T}\right)\tag{13.39}
$$

上式包含了各序列前缀的状态下策略，以及一个最终的奖励。

如果对此式做优化，序列每增加一个长度，计算复杂度将呈指数上升。我们不这么干，利用前后状态下动作值函数的递归关系：

$$
Q^{\theta}\left(Y_{1 : t-1}, y_{t}\right)=\sum_{y_{t+1}} G_{\theta}\left(y_{t+1} | Y_{l:T}\right) Q^{\theta}\left(Y_{1 : t}, y_{t+1}\right)\tag{13.40}
$$


将序列末端的 $Q^{\theta}\left(Y_{1 : T-1}, y_{T}\right)$ 转换为序列初端的 $Q^{\theta}\left(s_{0}, y_{1}\right)$ ，得到一个简化的生成器优化目标：

$$
J(\theta)=\sum_{y_{1} \in \mathcal{Y}} G_{\theta}\left(y_{1} | s_{0}\right) Q^{\theta}\left(s_{0}, y_{1}\right)\tag{13.41}
$$


该优化目标的含义是，在起始状态 $s_{0}$ 下根据策略选择第一个词 $y_1$，并在之后依旧根据这个策略选词，总体可得奖励的期望。此时序列末端的奖励成了序列初端的长期奖励。


SeqGAN 示意图：

<center>

![](http://images.iterate.site/blog/image/20190426/wPTVDc3jVwbz.png?imageslim){ width=55% }


</center>


## 有了生成器的优化目标，怎样求解它对生成器参数的梯度？


我们已有目标函数 $J(\theta)$，现在对它求梯度 $\nabla_{\theta} J(\theta)$。此优化目标是一个求和，里面包含两项：策略 $G_{\theta}$ 和动作值函数 $Q^{\theta}$ ，它们都含参数 $\theta$，根据求导法则 $(u(x) v(x))^{\prime}=u^{\prime}(x) v(x)+u(x) v(x)^{\prime}$ ，免不了求 $\nabla_{\theta} G_{\theta}\left(y_{1} | s_{0}\right)$ 和 $\nabla_{\theta} Q^{\theta}\left(s_{0}, y_{1}\right)$。

与 IRGAN 不同，IRGAN 中也有两项：策略和即时奖励，但它没有长期奖励，不用计算动作值函数，而且即时奖励不依赖于策略，也就与参数 $\theta$ 无关，只需求策略对 $\theta$ 的梯度。但是在 SeqGAN 里，策略对 $\theta$ 的梯度和动作值函数对 $\theta$ 的梯度都要求。这里是一个概率函数，计算不难，但是呢？如何计算？

这确实是一个不小的挑战。前面已给出 $Q^{\theta}$ 的递归公式：

$$
Q^{\theta}\left(Y_{1:t-1}, y_{t}\right)=\sum_{y_{t+1}} G_{\theta}\left(y_{t+1} | Y_{1:T}\right) Q^{\theta}\left(Y_{1:t}, y_{t+1}\right)\tag{13.42}
$$

现在我们推导 $\nabla_{\theta} J(\theta)$ ：

$$
\begin{aligned} \nabla_{\theta} J(\theta) &=\sum_{y_{1} \in \mathcal{Y}}\left(\nabla_{\theta} G_{\theta}\left(y_{1} | s_{0}\right) \cdot Q^{\theta}\left(s_{0}, y_{1}\right)+G_{\theta}\left(y_{1} | s_{0}\right) \cdot \nabla_{\theta} Q^{\theta}\left(s_{0}, y_{1}\right)\right) \\ &=\sum_{y_{1} \in \mathcal{Y}}\left(\nabla_{\theta} G_{\theta}\left(y_{1} | s_{0}\right) \cdot Q^{\theta}\left(s_{0}, y_{1}\right)+G_{\theta}\left(y_{1} | s_{0}\right) \cdot \nabla_{\theta}\left(\sum_{y_{2} \in \mathcal{Y}} G_{\theta}\left(y_{2} | Y_{1 : 1}\right) Q^{\theta}\left(Y_{1 : 1}, y_{2}\right)\right)\right)\end{aligned}\tag{13.43}
$$


像上面，依次用后面的动作值 $Q^{\theta}\left(Y_{1 : t}, y_{t+1}\right)$ 替换前面的动作值 $Q^{\theta}\left(Y_{1 : t-1}, y_{t}\right)$，最终可得：



$$
\nabla_{\theta} J(\theta)=\sum_{t=1}^{T} \mathbb{E}_{Y_{1:t-1} \sim G_{\theta}}\left[\sum_{y_{t} \in \mathcal{Y}} \nabla_{\theta} G_{\theta}\left(y_{t} | Y_{1 : t-1}\right) \cdot Q^{\theta}\left(Y_{1 : t-1}, y_{t}\right)\right]\tag{13.44}
$$


其中记 $Y_{1:0} :=s_{0}$ 。





# 相关

- 《百面机器学习》
