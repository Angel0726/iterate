---
title: 04 ALI 包揽推断业务
toc: true
date: 2019-04-26
---
# 可以补充进来的

- 感觉有些厉害，而且公式部分不是特别清楚。
- 而且，这个一般使用在什么场景下？

# ALI：包揽推断业务

宋朝有位皇帝非常喜爱书画，创建了世界上最早的皇家画院。一次考试，他出的题目是“深山藏古寺”，让众多前来报考的画家画。有的在山腰间画了一座古寺，有的将古寺画在丛林深处，有的古寺画得完整，有的只画了寺的一角。皇帝看了都不满意，就在他叹息之时，一幅画作进入他的视线，他端详一番称赞道：“妙哉！妙哉！”

原来这幅画上根本没有寺，只见崇山峻岭间，一股清泉飞流直下，一位老和尚俯身在泉边，背后是挑水的木桶，木桶后弯弯曲曲远去的小路，消失在丛林深处（见图 13.15）。寺虽不见于画，却定“藏”于山，比起寺的一角或一段墙垣，更切合考题。

<center>

![](http://images.iterate.site/blog/image/20190426/EF1iqqGk1zTX.png?imageslim){ width=55% }

</center>

人们看画，看的不仅是画家的画技，还有所表达的主题。同一主题，表现的手法很多，不同人会画出不同画。反过来，观者在看到这些不同画时，都能联想到同一主题或相似主题。

- 给一个主题，创作一幅画，这就是生成的过程；
- 给一幅画，推测画的主题，这就是推断的过程。

生成与推断是互逆的。

这样的例子还有很多：

- 一方面，当要测试一个人的创造力时，给他一个话题让他写文章，给他一个思路让他想实施细节，这类测试问题都是开放性的，没有标准答案，却不妨碍我们考查对方的能力；
- 另一方面，当听到一个人的发言或看到他的作品时，我们会揣摩对方的真实用意，他的话是什么意思，他的作品想表达什么。


我们面对两类信息：

- 一类可以观察到，
- 一类虽观察不到但似乎就在那里，或直白，或隐约，我们通过推断感受到它的存在。

这两类信息在两种表达空间里：

- 一种是观察数据所在的数据空间
- 一种是隐变量所在的隐空间，后者是前者的一种抽象。<span style="color:red;">嗯，是的，厉害。</span>

生成和推断就是这两种空间上信息的转换，用两个深度神经网络来构建：

- 一个是生成网络：建立从隐空间到数据空间的映射
- 一个是推断网络：建立从数据空间到隐空间的映射

<span style="color:red;">厉害！</span>

数据空间的信息看得见，称为明码；隐空间的信息看不见，称为暗码，因此：

- 生成网络是一个解码器（Decoder）
- 推断网络是一个编码器（Encoder）

<span style="color:red;">是的是的。</span>


把生成和推断相结合，想象一个场景：

我们想学习印象派画家的画风，仔细观察多幅名作，体会它们的表现手法及反映的主题，然后我们凭着自己的理解，亲自动手，创作一幅印象派画。整个过程分为推断和生成。

那么，如何提高我们的绘画水平？

我们需要一位大师或懂画的评论家，告诉我们哪里理解的不对，哪里画的不对。我们则要在评论家的批评中增进技艺，以至于让他挑不出毛病。

这也是 GANs 的基本思路。

2017 年的一篇论文提出 ALI（Adversarially Learned Inference）模型[40]，将生成网络和推断网络一起放到 GANs 的框架下，进而联合训练生成模型和推断模型，取得不错的效果。<span style="color:red;">厉害厉害。</span>


知识点

概率推断，隐空间，Encoder/Decoder

## 生成网络和推断网络的融合


请问如何把一个生成网络和一个推断网络融合在 GANs 框架下，借助来自判别器的指导，不仅让模拟样本的分布尽量逼近真实分布，而且让模拟样本的隐空间表示与真实样本的隐空间表示在分布上也尽量接近。<span style="color:red;">真的是牛逼，不仅让模拟样本的分布尽量逼近真实分布，而且让模拟样本的隐空间标识与真实样本的隐空间标识在分布上也尽量接近。</span>


分析与解答

任何一个观察数据 $x$，背后都有一个隐空间表示 $z$，从 $x$ 到 $z$ 有一条转换路径，从 $z$ 到 $x$ 也有一条转换路径，前者对应一个编码过程，后者对应一个解码过程。

从概率的角度看：

- 编码是一个推断过程，先从真实数据集采样一个样本 $x$，再由 $x$ 推断 $z$，有给定 $x$ 下 $z$ 的条件概率 $q(z | x)$。<span style="color:red;">嗯，是的，赞！</span>
- 解码是一个生成过程，先从一个固定分布（如：高斯分布 $N(0, I)$ ）出发，采样一个随机信号 $\epsilon$，经过简单变换成为 $z$，再由 $z$ 经过一系列复杂非线性变换生成 $x$ ，有给定 $z$ 下 $x$ 的条件概率 $q(x | z)$。

一般地，隐空间表示 $z$ 比观察数据 $x$ 更抽象更精炼，刻画 $z$ 的维数应远小于 $x$，从随机信号 $\epsilon$ 到 $z$ 只做简单变换，有时直接拿 $\epsilon$ 作 $z$，表明隐空间的信息被压缩得很干净，任何冗余都被榨干，任何相关维度都被整合到一起，使得隐空间各维度相互独立，因此隐空间的随机点是有含义的。<span style="color:red;">哇塞！！这个，嗯，有些厉害呀！为什么隐空间的标识比观察数据更抽象更精炼？emmm，好像是这么回事。比如数学公式比一道数学题更精炼。隐空间各维度响度独立，是什么意思？隐空间的随机点是有含义的是什么意思？emmm，好像是这么样，但是想更多了解下。</span>


将观察数据和其隐空间表示一起考虑，$(x, z)$，写出联合概率分布。

- 从推断的角度看，联合概率 $q(x, z)=q(x) q(z | x)$，其中 $q(x)$ 为真实数据集上的经验数据分布，可认为已知，条件概率 $q(z | x)$ 则要通过推断网络来表达。<span style="color:red;">厉害呀，条件概率 $q(z | x)$ 可通过推断网络来表达。赞！</span>
- 从生成的角度看，$p(x, z)=p(z) p(x | z)$ ，其中 $p(z)$ 是事先给定的，如 $z \sim N(0, I)$ ，条件概率 $p(x | z)$ 则通过生成网络来表达。<span style="color:red;">赞！</span>

然后，我们让这两个联合概率分布 $q(x, z)$ 和 $p(x, z)$ 相互拟合。当二者趋于一致时，可以确定：

- 对应的边缘概率都相等，$q(x)=p(x)$ ，$q(z)=p(z)$
- 对应的条件概率也都相等 $q(z | x)=p(z | x)$ ，$q(x | z)=p(x | z)$

最重要的是，得到的生成网络和推断网络是一对互逆的网络。<span style="color:red;">厉害！</span>

值得注意的是，这种互逆特性不同于自动编码器这种通过最小化重建误差学出的网络，后者是完全一等一的重建，而前者是具有相同隐空间分布（如：风格、主题）的再创造。<span style="color:red;">哇塞！厉害呀。感觉这个是不是可以用来生成小说？或者画画？嗯，对于自动编码器通过最小化重建误差学出的网络，再补充下。</span>

除了生成网络和推断网络，还有一个判别网络。它的目标是区分来自生成网络的 $\left(\hat{x}=G_{\text { decoder }}(z), z\right)$ 和来自推断网络的 $\left(x, \hat{z}=G_{\text { encoder }}(x)\right)$ ，如图 13.16 所示，ALI 模型：

![](http://images.iterate.site/blog/image/20190426/KoqgpA2fmSew.png?imageslim){ width=55% }


在 GANs 框架下，判别网络与生成和推断网络共享一个目标函数：


$$
\begin{aligned}
V\left(D_{\phi}, G_{\theta_{\mathrm{dec}}}, G_{\theta_{\mathrm{enc}}}\right)&=\mathbb{E}_{x \sim q(x)}\left[\log D_{\phi}\left(x, G_{\theta_{\mathrm{enc}}}(x)\right)\right]+\mathbb{E}_{z \sim p(z)}\left[\log \left(1-D_{\phi}\left(G_{\theta_{\mathrm{dec}}}(z), z\right)\right)\right]\\&=\iint q(x) q\left(z | x ; \theta_{\mathrm{enc}}\right) \log D_{\phi}(x, z) \mathrm{d} x \mathrm{d} z+\iint p(z) p\left(x | z ; \theta_{\mathrm{dec}}\right) \log \left(1-D_{\phi}(x, z)\right) \mathrm{d} x \mathrm{d} z\end{aligned}\tag{13.19}
$$


进行的也是一场 MiniMax 游戏：

$$
\min _{\theta=\left(\theta_{\mathrm{dec}}, \theta_{\mathrm{enc}}\right)} \max _{\phi} V\left(D_{\phi}, G_{\theta_{\mathrm{dec}}}, G_{\theta_{\mathrm{enc}}}\right)\tag{13.20}
$$

其中：

- $\theta_{\mathrm{dec}}$，$\theta_{\mathrm{enc}}$， $\phi$ 分别为生成网络、推断网络和判别网络的参数。
- 判别网络试图最大化 $V$ 函数，生成和推断网络则试图最小化 $V$ 函数。
- 第一个等号右边的式子，反映了在重参数化技巧（Re-parameterization Trick）下将三个网络组装成一个大网络；<span style="color:red;">没看懂。。</span>
- 第二个等号右边的式子，从判别器的角度看产生 $(x, z)$ 的两个不同数据源。<span style="color:red;">没有很明白。</span>


实际中，为克服训练初期生成和推断网络从判别网络获取梯度不足的问题，我们采用一个梯度增强版的优化目标，将原目标函数中的 $\log (1-D(G(\bullet))$ 改成 $-\log (D(G(\bullet))$ 。<span style="color:red;">嗯，这个之前一节有提过。</span>

原 GANs 论文指出，这个小变换不改变前后优化目标的解，但是前者会出现梯度饱和问题，后者能产生更明显的梯度。修改前生成和推断网络的优化目标为：

$$
\min _{\theta=\left(\theta_{\mathrm{dec}}, \theta_{\mathrm{enc}}\right)} \mathbb{E}_{x \sim q(x)}\left[\log D_{\phi}\left(x, G_{\theta_{\mathrm{enc}}}(x)\right)\right]+\mathbb{E}_{z \sim p(z)}\left[\log \left(1-D_{\phi}\left(G_{\theta_{\mathrm{dec}}}(z), z\right)\right)\right)\tag{13.21}
$$


修改后的优化目标为：

$$
\max _{\theta=\left(\theta_{\mathrm{dec}}, \theta_{\mathrm{enc}}\right)} \mathbb{E}_{x \sim q(x)}\left(\log \left(1-D_{\phi}\left(x, G_{\theta_{\mathrm{enc}}}(x)\right)\right)\right)+\mathbb{E}_{z \sim p(z)}\left[\log D_{\phi}\left(G_{\theta_{\mathrm{dec}}}(z), z\right)\right)\tag{13.22}
$$

有了上面的分析，就好设计出一个同时训练生成和推断网络及判别网络的 GANs 算法。


<span style="color:red;">公式的推理没有很明白，要补充下。</span>

<span style="color:red;">按照公式进行设计的 GANs 算法也要补充下。</span>




# 相关

- 《百面机器学习》
