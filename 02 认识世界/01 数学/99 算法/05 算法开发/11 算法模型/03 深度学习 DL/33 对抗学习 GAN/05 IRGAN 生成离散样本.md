---
title: 05 IRGAN 生成离散样本
toc: true
date: 2019-04-26
---
# 可以补充进来的

- 这个没看懂。但是感觉很厉害。再补充下。

# IRGAN：生成离散样本

Reddit 论坛上有一篇 Goodfellow 发表的帖子：“GANs have not been applied to NLP because GANs are only defined for real-valued data…… The gradient of the output of the discriminator network with respect to the synthetic data tells you how to slightly change the synthetic data to make it more realistic.You can make slight changes to the synthetic data if it is based on continuous numbers.If it is based on discrete numbers，there is no way to make a slight change.

大意是说，最初设计 GANs 是用来生成实值数据的，生成器输出一个实数向量。这样，判别器对实数向量每维都产生一个梯度，用作对模型参数的微小更新，持续的微小更新使得量变引起质变，最终达到一个非常好的解。

可是，如果生成器输出离散数据，诸如：搜索引擎返回的链接，电商网站推荐的手机，那么梯度产生的微小更新就被打破了，因为离散样本的变化不是连续而是跳跃的。

举个例子，灯光亮度是一种连续型数据，可以说把灯光调亮一些，也可以说把灯光调亮一点，还可以说把灯光调亮一点点，这都可操作；

但是，从买苹果转到买橙子，你不能说买橙子一些，一点或者一点点（见图 13.17）。<span style="color:red;">是的。</span>

<center>

![](http://images.iterate.site/blog/image/20190426/zdl9PLiggThl.png?imageslim){ width=55% }

</center>

将 GANs 用来生成离散数据，不是一件简单的事情，但是生活中很多时候，我们遇到的就是各型各色的离散型数据。


让我们想象一个信息检索的场景：

给定一个查询词，系统返回与查询词相关的若干文档。

现在，我们有一批用户点击数据，记录用户在某查询词下点击哪些文档。用户的反馈告诉我们哪些是正样本，为了训练识别正负样本的有监督模型，我们还需负样本，即与查询词不相关或者看似相关实则无关的样本。

通常做法是在全部文档集上随机负采样，一个查询词的正样本集与全体文档集比简直沧海一粟，所以随机采得的文档碰巧是正样本的概率很小。

但这会遇到一个问题，随机负采样的结果往往太简单，对模型构不成难度。我们想尽量制造易混淆的例子，才能让模型的学习能力达到一个新水平。

因此，我们不能在全集做负采样，必须在与查询词含义接近或貌似接近的地带负采样。一个新问题是，这种有偏采样下，采到正样本的概率大大增加，我们不能简单地认为随机采样结果都是负样本。<span style="color:red;">哇塞！这种场景，真的是。</span>

怎么办呢？2017 年的一篇论文提出了解决办法，称之为 IRGAN[41]。


离散样本，信息检索，负采样，策略梯度

## 用 GAN 产生负样本。

我们想借助 GANs 解决上面问题，设计一种制造负样本的生成器，采样一些迷惑性强的负样本，增大对判别模型的挑战。<span style="color:red;">嗯。</span>

查询词表示为 $q$，文档表示为 $d$。请描述一下你的设计思路，指出潜在的问题及解决方案。请问：训练到最后时，生成模型还是一个负样本生成器吗？

分析与解答

我们把全集上随机选负样本的方法，看成一种低级的生成器，该生成器始终是固定的，没有学习能力。我们想设计一个更好的有学习能力的生成器，可以不断学习，生成高质量的负样本去迷惑的判别器。实际上，在 GANs 的设计理念里，“负样本”的表述不准确，因为生成器的真正目标不是找出一批与查询词不相关的文档，而是让生成的模拟样本尽量逼近真实样本，判别器的目标是发现哪些样本是真实用户的点击行为，哪些是生成器的伪造数据。

“正负” 含义微妙变化，前面与查询词相关或不相关看成正或负，现在真实数据或模拟数据也可看成正或负。<span style="color:red;">嗯。</span>

在信息检索的场景下，我们遵循 GANs 的 MiniMax 游戏框架，设计一个生成式检索模型 $p_{\theta}(d | q)$ 和一个判别式检索模型 $f_{\phi}(q, d)$。

- 给定 $q$ ，生成模型会在整个文档集中按照概率分布 $p_{\theta}(d | q)$ 挑选出文档 $d_{\theta}$，它的目标是逼近真实数据的概率分布 $p_{\text { true }}(d | q)$，进而迷惑判别器；
- 同时，判别模型试图将生成器伪造的 $\left(q, d_{\theta}\right)$ 从真实的 $\left(q, d_{\text { true }}\right)$ 中区分出来。

原本的判别模型是用来鉴别与 Query 相关或不相关的文档，而在 GAN 框架下判别模型的目标发生了微妙变化，区分的是来自真实数据的相关文档和模拟产生的潜在相关文档。当然，最终的判别模型仍可鉴别与 Query 相关或不相关的文档。

我们用一个 MiniMax 目标函数来统一生成模型和判别模型：

$$
J^{G^{*}, D^{*}}=\min _{\theta} \max _{\phi} \sum_{n=1}^{N}\left(\mathbb{E}_{d \sim p_{\mathrm{true}}\left(d | q_{n}\right)}\left[\log D_{\phi}\left(d | q_{n}\right)\right]+\mathbb{E}_{d \sim p_{\theta}\left(d | q_{n}\right)}\left[\log \left(1-D_{\phi}\left(d | q_{n}\right)\right)\right]\right)\tag{13.23}
$$

其中 $D_{\phi}\left(d | q_{n}\right)=\operatorname{Sigmoid}\left(f_{\phi}(q, d)\right)$ 。这是一个交替优化的过程，固定判别器，对生成器的优化简化为：


$$
\theta^{*}=\underset{\theta}{\operatorname{argmax}} \sum_{n=1}^{N} \mathbb{E}_{d \sim p_{\theta}\left(d | q_{n}\right)}\left[\log \left(1+\exp \left(f_{\phi}\left(q_{n}, d\right)\right)\right)\right)]\tag{13.24}
$$



问题来了，如果 $d$ 连续，我们沿用原 GANs 的套路没问题，对每个 $q_{n}$，生成 $K$ 个文档 $\left\{d_{k}\right\}_{k=1}^{K}$ ，用 $\sum_{k=1}^{K} \log \left(1+\exp \left(f_{\phi}\left(q_{n}, d_{k}\right)\right)\right)$ 近似估计每个 $q_{n}$ 下的损失函数 $\mathbb{E}_{d \sim p_{\theta}\left(d | q_{n}\right)}\left[\log \left(1+\exp \left(f_{\phi}\left(q_{n}, d\right)\right)\right)\right)]$ ，损失函数对 $d_{k}$ 的梯度会回传给生成 $d_{k}$ 的生成器。但是，如果 $d$ 离散，损失函数对 $d$ 是没有梯度的，我们拿什么传给生成器呢？<span style="color:red;">是呀。</span>


强化学习中的策略梯度方法揭示了期望下损失函数的梯度的另外一种形式[42]。我们用 $J^{G}\left(q_{n}\right)$ 表示给定 $q_{n}$ 下损失函数的期望，即：

$$
J^{G}\left(q_{n}\right) :=\mathbb{E}_{d \sim p_{\theta}\left(d | q_{n}\right)}\left[\log \left(1+\exp \left(f_{\phi}\left(q_{n}, d\right)\right)\right)\right]\tag{13.25}
$$

我们暂不用蒙特卡洛采样（即采样样本之和的形式）去近似期望，而是直接对期望求梯度：

$$
\nabla_{\theta} J^{G}\left(q_{n}\right)=\mathbb{E}_{d \sim p_{\theta}\left(d | q_{n}\right)}\left[\log \left(1+\exp \left(f_{\phi}\left(q_{n}, d\right)\right)\right) \nabla_{\theta} \log p_{\theta}\left(d | q_{n}\right)\right]\tag{13.26}
$$

梯度仍是期望的形式，是对数概率函数 $\log p_{\theta}\left(d | q_{n}\right)$ 对 $\theta$ 的梯度带上权重 $\log \left(1+\exp \left(f_{\phi}\left(q_{n}, d\right)\right)\right)$ 的期望，我们再用蒙特卡洛采样去近似它：

$$
\nabla_{\theta} J^{G}\left(q_{n}\right) \approx \frac{1}{K} \sum_{k=1}^{K} \log \left(1+\exp \left(f_{\phi}\left(q_{n}, d_{k}\right)\right)\right) \nabla_{\theta} \log p_{\theta}\left(d_{k} | q_{n}\right)\tag{13.27}
$$






其中 $K$ 为采样个数。

此时，我们就能估计目标函数对生成器参数 $\theta$ 的梯度，因为梯度求解建立在对概率分布函数 $p_{\theta}\left(d_{k} | q_{n}\right)$ （强化学习中称策略函数）求梯度的基础上，所以称为策略梯度。

欲得到策略梯度，我们必须显式表达 $p_{\theta}\left(d_{k} | q_{n}\right)$ ，这与原 GANs 的生成器不同。原 GANs 不需要显式给出概率分布函数的表达式，而是使用了重参数化技巧，通过对噪音信号的变换直接给出样本，好处是让生成过程变得简单，坏处是得不到概率表达式，不适于这里的生成器。

这里直接输出的不是离散样本，而是每个离散样本的概率：

- 一方面，生成器的输入端不需要引入噪音信号，我们不想让概率分布函数也变成随机变量；
- 另一方面，生成器的输出端需要增加抽样操作，依据所得概率分布生成 $K$ 个样本。

如果用神经网络对生成器建模，那么最后一层应是 Softmax 层，才能得到离散样本概率分布。在判别器的构建中，输入的是离散样本的 $n$ 维向量表示，如一个文档向量每维可以是一些诸如 BM25，TF-IDF，PageRank 的统计值，其余部分参照原 GANs 的做法。

最后，训练过程与原 GANs 的步骤一样，遵循一个 MiniMax 的优化框架，对生成器和判别器交替优化。

- 优化生成器阶段，先产生 $K$ 个样本，采用策略梯度对生成模型参数进行多步更新；
- 优化判别器阶段，也要先产生 $K$ 个样本，作为负样本与真实数据的样本共同训练判别器。

理论上，优化过程会收敛到一个纳什均衡点，此时生成器完美地拟合了真实数据的 Query-Document 相关性分布  $p_{\text { true }}(d | q_n)$，这个生成模型被称为生成式检索模型（Generative Retrieval Models），对应于有监督的判别式检索模型（Discriminative Retrieval Models）。<span style="color:red;">感觉太厉害了。</span>



<span style="color:red;">没看懂。再看下，从头推导下。</span>



# 相关

- 《百面机器学习》
