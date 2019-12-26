---
title: 02 WGAN 抓住低维的幽灵
toc: true
date: 2019-04-21
---
# 可以补充进来的

- 没看懂。一些数学基础不知道。



# WGAN：抓住低维的幽灵



看过《三体Ⅲ·死神永生》的朋友，一定听说过 “降维打击” 这个词，像拍苍蝇一样把敌人拍扁。<span style="color:red;">是呀，太牛逼了</span>

其实，低维不见得一点好处都没有。想象猫和老鼠这部动画的一个镜头，老鼠 Jerry 被它的劲敌 Tom 猫一路追赶，突然 Jerry 发现墙上挂了很多照片，其中一张的背景是海边浴场，沙滩上有密密麻麻很多人，Jerry 一下子跳了进去，混在人群中消失了，Tom 怎么也找不到 Jerry。三维的 Jerry 变成了一个二维的 Jerry，躲过了 Tom。

一个新的问题是：Jerry 对于原三维世界来说是否还存在？极限情况下，如果这张照片足够薄，没有厚度，那么它就在一个二维平面里，不占任何体积（见图 13.5），体积为零的东西不就等于没有吗！

二维画面与三维空间：

<center>

![](http://images.iterate.site/blog/image/20190421/5KW3NMHBxItm.png?imageslim){ width=55% }

</center>

拓展到高维空间，这个体积叫测度，无论 $N$ 维空间的 $N$ 有多大，在 $N+1$ 维空间中测度就是零，就像二维平面在三维空间中一样。<span style="color:red;">嗯，测度。</span>

因此，一个低维空间的物体，在高维空间中忽略不计。对生活在高维世界的人来说，低维空间是那么无足轻重，像一层纱，似一个幽灵，若有若无，是一个隐去的世界。

2017 年，一个训练生成对抗网络的新方法 WGAN 被提出[34]。在此之前，GANs已提出三年，吸引了很多研究者来使用它。原理上，大家都觉得 GANs 的思路实在太巧妙，理解起来也不复杂，符合人们的直觉，万物不都是在相互制约和对抗中逐渐演化升级吗。<span style="color:red;">是呀。</span>

理论上，Goodfellow 在 2014 年已经给出 GANs 的最优性证明，证明 GANs 本质上是在最小化生成分布与真实数据分布的 JS 距离，当算法收敛时生成器刻画的分布就是真实数据的分布。<span style="color:red;">JS 距离是什么？补充下，之前就想补充的。</span>

但是，实际使用中发现很多解释不清的问题，生成器的训练很不稳定[35]。生成器这只 Tom 猫，很难抓住真实数据分布这只老鼠 Jerry。<span style="color:red;">哦，是这样吗？</span>


坍缩模式（Collapse Mode），Wasserstein距离，1-Lipschitz函数

## GANs的陷阱：原 GANs 中存在的哪些问题制约模型训练效果。

GANs的判别器试图区分真实样本和生成的模拟样本。Goodfellow 在论文中指出：

- 训练判别器，是在度量生成器分布和真实数据分布的 JS 距离；
- 训练生成器，是在减小这个 JS 距离。

即使我们不清楚形成真实数据的背后机制，还是可以用一个模拟生成过程去替代之，只要它们的数据分布一致。

但是实验中发现，训练好生成器是一件很困难的事，生成器很不稳定，常出现坍缩模式。

什么是坍缩模式？

拿图片举例，反复生成一些相近或相同的图片，多样性太差。生成器似乎将图片记下，没有泛化，更没有造新图的能力，好比一个笨小孩被填鸭灌输了知识，只会死记硬背，没有真正理解，不会活学活用，更无创新能力。<span style="color:red;">。</span>


为什么会这样？

既然训练生成器基于 JS 距离，猜测问题根源可能与 JS 距离有关。高维空间中不是每点都能表达一个样本（如一张图片），空间大部分是多余的，真实数据蜷缩在低维子空间的流形（即高维曲面）上，因为维度低，所占空间体积几乎为零，就像一张极其薄的纸飘在三维空间，不仔细看很难发现。<span style="color:red;">什么意思？真实数据蜷缩在低维子空间的流行上（即高维曲面）上。是什么意思？看来数学还是要学好，不然真的不行。</span>

考虑生成器分布与真实数据分布的 JS 距离，即两个 KL 距离的平均：

$$
\mathrm{JS}\left(\mathbb{P}_{r} \| \mathbb{P}_{g}\right)=\frac{1}{2}\left(\mathrm{KL}\left(\mathbb{P}_{r} \| \frac{\mathbb{P}_{r}+\mathbb{P}_{g}}{2}\right)+\mathrm{KL}\left(\mathbb{P}_{g} \| \frac{\mathbb{P}_{r}+\mathbb{P}_{g}}{2}\right)\right)\tag{13.12}
$$

初始的生成器，由于参数随机初始化，与其说是一个样本生成器，不如说是高维空间点的生成器，点广泛分布在高维空间中。<span style="color:red;">嗯，是的。</span>

打个比方，生成器将一张大网布满整个空间，“兵力”有限，网布得越大，每个点附近的兵力就越少。想象一下，当这张网穿过低维子空间时，可看见的“兵”几乎为零，这片子空间成了一个 “盲区”，如果真实数据全都分布在这，它们就对生成器“隐身”了，成了“漏网之鱼”（见图 13.6）。<span style="color:red;">是的。</span>


高维空间中的生成器样本网点与低维流形上的真实分布：

<center>

![](http://images.iterate.site/blog/image/20190421/mmX9hK4dY9tq.png?imageslim){ width=55% }

</center>

回到公式，看第一个 KL 距离：

$$
\mathrm{KL}\left(\mathbb{P}_{r} \| \frac{\mathbb{P}_{r}+\mathbb{P}_{g}}{2}\right)=\int \log \left(\frac{p_{r}(x)}{\left(p_{r}(x)+p_{g}(x)\right) / 2}\right) p_{r}(x) \mathrm{d} \mu(x)\tag{13.13}
$$

高维空间绝大部分地方见不到真实数据，$p_{r}(x)$ 处处为零，对 KL 距离的贡献为零；即使在真实数据蜷缩的低维空间，高维空间会忽略低维空间的体积，概率上讲测度为零。KL 距离就成了：$\int \log 2 \cdot p_{r}(x) \mathrm{d} \mu(x)=\log 2$ 。

再看第二个 KL 距离：


$$
\mathrm{KL}\left(\mathbb{P}_{g} \| \frac{\mathbb{P}_{r}+\mathbb{P}_{g}}{2}\right)=\int \log \left(\frac{p_{g}(x)}{\left(p_{r}(x)+p_{g}(x)\right) / 2}\right) p_{g}(x) \mathrm{d} \mu(x)\tag{13.14}
$$

同理 KL 距离也为：$\int \log 2 \cdot p_{g}(x) \mathrm{d} \mu(x)=\log 2$ 。因此，JS距离为 $\log 2$ ，一个常量。无论生成器怎么 “布网”，怎么训练，JS 距离不变，对生成器的梯度为零。训练神经网络是基于梯度下降的，用梯度一次次更新模型参数，如果梯度总是零，训练还怎么进行？

<span style="color:red;">厉害呀！再理解下。</span>


## 破解武器：WGAN 针对前面问题做了哪些改进？什么是 Wasserstein 距离？

直觉告诉我们：不要让生成器在高维空间傻傻地布网，让它直接到低维空间 “抓” 真实数据。

道理虽然是这样，但是在高维空间中藏着无数的低维子空间，如何找到目标子空间呢？

站在大厦顶层，环眺四周，你可以迅速定位远处的山峦和高塔，却很难知晓一个个楼宇间办公室里的事情。你需要线索，而不是简单撒网。处在高维空间，对抗隐秘的低维空间，不能再用粗暴简陋的方法，需要有特殊武器，这就是 Wasserstein 距离（见图 13.7），也称推土机距离（Earth Mover distance）：<span style="color:red;">以前从来没听说过。</span>

Wasserstein 距离：

$$
W\left(\mathbb{P}_{r}, \mathbb{P}_{g}\right)=\inf _{\gamma \sim \Pi\left(\mathbb{P}_{r}, \mathbb{P}_{g}\right)} \mathbb{E}_{(x, y) \sim \gamma}[\|x-y\|]\tag{13.15}
$$

<center>

![](http://images.iterate.site/blog/image/20190421/3P1C9v4LkEze.png?imageslim){ width=55% }

</center>

怎么理解这个公式？想象你有一个很大的院子，院子里有几处坑坑洼洼需要填平，四个墙角都有一堆沙子，沙子总量正好填平所有坑。

搬运沙子很费力，你想知道有没有一种方案，使得花的力气最少。

直觉上，每个坑都选择最近的沙堆，搬运的距离最短。但是存在一些问题，如果最近的沙堆用完了，或者填完坑后近处还剩好多沙子，或者坑到几个沙堆的距离一样，我们该怎么办？

所以需要设计一个系统的方案，通盘考虑这些问题。最佳方案是上面目标函数的最优解。可以看到，当沙子分布和坑分布给定时，我们只关心搬运沙子的整体损耗，而不关心每粒沙子的具体摆放，在损耗不变的情况下，沙子摆放可能有很多选择。

对应式（13.16），当你选择一对 $(x,y)$ 时，表示把 $x$ 处的一些沙子搬到 $y$ 处的坑，可能搬部分沙子，也可能搬全部沙子，可能只把坑填一部分，也可能都填满了。$x$ 处沙子总量为 $\mathbb{P}_{r}(x)$ ，$y$ 处坑的大小为 $\mathbb{P}_{g}(x)$ ，从 $x$ 到 $y$ 的沙子量为 $\gamma(x, y)$ ，整体上满足等式：


$$
\sum_{x} \gamma(x, y)=\mathbb{P}_{g}(y)\tag{13.16}
$$

$$
\sum_{y} \gamma(x, y)=\mathbb{P}_{r}(x)\tag{13.17}
$$

为什么 Wasserstein 距离能克服 JS 距离解决不了的问题？

理论上的解释很复杂，需要证明当生成器分布随参数 $\theta$ 变化而连续变化时，生成器分布与真实分布的 Wasserstein 距离也随 $\theta$ 变化而连续变化，并且几乎处处可导，而 JS 距离不保证随 $\theta$ 变化而连续变化。<span style="color:red;">还是要补充进来的。</span>


通俗的解释，接着 “布网” 的比喻，现在生成器不再 “布网” ，改成 “定位追踪” 了：

- 不管真实分布藏在哪个低维子空间里，生成器都能感知它在哪，因为生成器只要将自身分布稍做变化，就会改变它到真实分布的推土机距离；
- 而 JS 距离是不敏感的，无论生成器怎么变化，JS 距离都是一个常数。

因此，使用推土机距离，能有效锁定低维子空间中的真实数据分布。

<span style="color:red;">厉害呀，虽然没看懂。</span>



## WGAN 之道：怎样具体应用 Wasserstein 距离实现 WGAN 算法？

一群老鼠开会，得出结论：如果在猫脖上系一铃铛，每次它靠近时都能被及时发现，那多好！唯一的问题是：谁来系这个铃铛？

现在，我们知道了推土机距离这款武器，那么怎么计算这个距离？

推土机距离的公式太难求解。幸运的是，它有一个孪生兄弟，和它有相同的值，这就是 Wasserstein 距离的对偶式：<span style="color:red;">不懂</span>

$$
\begin{aligned} W\left(\mathbb{P}_{r}, \mathbb{P}_{g}\right) &=\sup _{f_{L} \leqslant 1} \mathbb{E}_{x \sim \mathbb{P}_{r}}[f(x)]-\mathbb{E}_{x \sim \mathbb{P}_{g}}[f(x)] \\ &=\max _{w \in \mathcal{W}} \mathbb{E}_{x \sim \mathbb{P}_{r}}\left[f_{w}(x)\right]-\mathbb{E}_{z \sim p(z)}\left[f_{w}\left(g_{\theta}(z)\right)\right] \end{aligned}\tag{13.18}
$$


对偶式大大降低了 Wasserstein 距离的求解难度，计算过程变为找到一个函数 $f$，使得它最大化目标函数 $\mathbb{E}_{x \sim \mathbb{P}_{r}}[f(x)]-\mathbb{E}_{x \sim \mathbb{P}_{g}}[f(x)]$ ，这个式子看上去很眼熟，对比原 GANs 的 $\max _{D} \mathbb{E}_{x-\mathbb{P}_{r}}[\log D(x)]+\mathbb{E}_{x-\mathbb{P}_{g}}[\log (1-D(x))]$ ，它只是去掉了 log，所以只做微小改动就能使用原 GANs 的框架。


细心的你会发现，这里的 $f$ 与 $D$ 不同，前者要满足 $\|f\|_{L} \leq 1$，即 1-Lipschitz 函数，后者是一个 Sigmoid 函数作输出层的神经网络。它们都要求在寻找最优函数时，一定要考虑界的限制。如果没有限制，函数值会无限大或无限小。Sigmoid 函数的值有天然的界，而 1-Lipschitz 不是限制函数值的界，而是限制函数导数的界，使得函数在每点上的变化率不能无限大。

神经网络里如何体现 1-Lipschitz 或 K-Lipschitz 呢？

WGAN 的思路很巧妙，在一个前向神经网络里，输入经过多次线性变换和非线性激活函数得到输出，输出对输入的梯度，绝大部分都是由线性操作所乘的权重矩阵贡献的，因此约束每个权重矩阵的大小，可以约束网络输出对输入的梯度大小。

判别器在这里换了一个名字，叫评分器（Critic），目标函数由 “区分样本来源” 变成 “为样本打分” ：越像真实样本分数越高，否则越低，有点类似支持向量机里 margin 的概念（见图 13.8）。

<center>

![](http://images.iterate.site/blog/image/20190421/ajFqEmj8osbN.png?imageslim){ width=55% }

</center>

打个龟兔赛跑的比方，评分器是兔子，生成器是乌龟。评分器的目标是甩掉乌龟，让二者的距离（或 margin）越来越大；生成器的目标是追上兔子。严肃一点讲，训练评分器就是计算生成器分布与真实分布的 Wasserstein 距离；给定评分器，训练生成器就是在缩小这个距离，算法中要计算 Wasserstein 距离对生成器参数 $\theta$ 的梯度，$\nabla_{\theta} W\left(\mathbb{P}_{r}, \mathbb{P}_{g}\right)=-\mathbb{E}_{z \sim p(z)}\left[\nabla_{\theta} f_{w}\left(g_{\theta}(z)\right)\right]$ ，再通过梯度下降法更新参数，让 Wasserstein 距离变小。

<span style="color:red;">没看懂</span>


# 相关

- 《百面机器学习》
