---
title: 18种热门GAN的PyTorch开源代码
toc: true
date: 2019-11-17
---

# 18种热门GAN的PyTorch开源代码


18种热门GAN的PyTorch实现，每一种GAN的论文地址。

这18种GAN是：

- Auxiliary Classifier GAN
- Adversarial Autoencoder
- Boundary-Seeking GAN
- Conditional GAN
- Context-Conditional GAN
- CycleGAN
- Deep Convolutional GAN
- DiscoGAN
- DRAGAN
- DualGAN
- GAN
- LSGAN
- Pix2Pix
- PixelDA
- Semi-Supervised GAN
- Super-Resolution GAN
- Wasserstein GAN
- Wasserstein GAN GP

![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtBYiaWP4xfjjCB8CJYhEmm0FR9Mlnfk3JymPymcxrasJibKuVzRVhGqJGvQzPiaH5FQA3pL780Qn9cZg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

###### **△** 来源：Kaggle blog

下面，量子位简单介绍一下这些GAN：

# Auxiliary Classifier GAN

带辅助分类器的GAN，简称ACGAN。

![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtBYiaWP4xfjjCB8CJYhEmm0FMF1IqoVDfUbb8MvV0LIPnCoFo1qw1GmL97G6TML7PwRZ4Xctfsyhag/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

在这类GAN变体中，生成器生成的每张图像，都带有一个类别标签，鉴别器也会同时针对来源和类别标签给出两个概率分布。

论文中描述的模型，可以生成符合1000个ImageNet类别的128×128图像。

**paper**：

###### Conditional Image Synthesis With Auxiliary Classifier GANsAugustus Odena, Christopher Olah, Jonathon Shlens https://arxiv.org/abs/1610.09585

# Adversarial Autoencoder

这种模型简称AAE，是一种概率性自编码器，运用GAN，通过将自编码器的隐藏编码向量和任意先验分布进行匹配来进行变分推断，可以用于半监督分类、分离图像的风格和内容、无监督聚类、降维、数据可视化等方面。

在论文中，研究人员给出了用MNIST和多伦多人脸数据集 (TFD)训练的模型所生成的样本。

![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtBYiaWP4xfjjCB8CJYhEmm0FnZ9engNOXagW7xiaZ5eA6WL6IacYq7eLTAIOyr6ZwcsH4U2iacx6A3PQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**paper**：

###### Adversarial Autoencoders Alireza Makhzani, Jonathon Shlens, Navdeep Jaitly, Ian Goodfellow, Brendan Frey https://arxiv.org/abs/1511.05644

# Boundary-Seeking GAN

原版GAN不适用于离散数据，而Boundary-Seeking GAN（简称BGAN）用来自鉴别器的估计差异度量来计算生成样本的重要性权重，为训练生成器来提供策略梯度，因此可以用离散数据进行训练。

BGAN里生成样本的重要性权重和鉴别器的判定边界紧密相关，因此叫做“寻找边界的GAN”。

![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtBYiaWP4xfjjCB8CJYhEmm0FicZbM0gdUMntQNysic5AWSZvaVvraCz2R88ia8tZI9ln7BR4gRWzF7xzQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**Paper**：

###### Boundary-Seeking Generative Adversarial Networks R Devon Hjelm, Athul Paul Jacob, Tong Che, Adam Trischler, Kyunghyun Cho, Yoshua Bengio https://arxiv.org/abs/1702.08431

# Conditional GAN

条件式生成对抗网络，简称CGAN，其中的生成器和鉴别器都以某种外部信息为条件，比如类别标签或者其他形式的数据。

![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtBYiaWP4xfjjCB8CJYhEmm0Fic9v94JHsiaynt0fyYzbaWyI2blo5tibWpfVU7XuicOvCOdJL4WZvdCibrQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**Paper**：

###### Conditional Generative Adversarial Nets Mehdi Mirza, Simon Osindero https://arxiv.org/abs/1411.1784

# Context-Conditional GAN

简称CCGAN，能用半监督学习的方法，修补图像上缺失的部分。

![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtBYiaWP4xfjjCB8CJYhEmm0F8eFMqhic8doP4fMlbzpp2icoR2wvc898aRMbib6sI2icrqicspCYsCTAwQw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtBYiaWP4xfjjCB8CJYhEmm0FxCibE0aE7ctB9SJWQk1qjs7Q3D45H2Z91L1ib1la4ZZ2ZbD7bHDxjUsg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**Paper**：

###### Semi-Supervised Learning with Context-Conditional Generative Adversarial Networks Emily Denton, Sam Gross, Rob Fergus https://arxiv.org/abs/1611.06430

# CycleGAN

这个模型是[加州大学伯克利分校的一项研究成果](http://mp.weixin.qq.com/s?__biz=MzIzNjc1NzUzMw==&mid=2247484928&idx=2&sn=4442def06e466086dd7002c70152bb63&chksm=e8d3b172dfa438647fa14ad3527167ebe9848c9c948c49d41e6f7372dd9ca8438971cecdcab0&scene=21#wechat_redirect)，可以在没有成对训练数据的情况下，实现图像风格的转换。

![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtBYiaWP4xfjjCB8CJYhEmm0F2QmM76jHzmwmjyDDTbnSFtCuywzg9qD2YRHRj2k3sxmRP9Q8ZoPiaKA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

这些例子，你大概不陌生：

![img](https://mmbiz.qpic.cn/mmbiz_jpg/YicUhk5aAGtBYiaWP4xfjjCB8CJYhEmm0FyHUlntKH6pHGekz0x6Fs8howT4KnfzbChPCMCQRMlkagdiaauBQK6Nw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**Paper**：

###### Unpaired Image-to-Image Translation using Cycle-Consistent Adversarial Networks Jun-Yan Zhu, Taesung Park, Phillip Isola, Alexei A. Efros https://arxiv.org/abs/1703.10593

论文原作者也开源了Torch和PyTorch的实现代码，详情见项目主页：

###### https://junyanz.github.io/CycleGAN

# Deep Convolutional GAN

深度卷积生成对抗网络（DCGAN）模型是作为无监督学习的一种方法而提出的，GAN在其中是最大似然率技术的一种替代。

![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtBYiaWP4xfjjCB8CJYhEmm0FO70wnOJibtibkDTPGBmULqrLIq3Jf3Giby8F1ZWbcYOflwphkEr6nJqxQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**Paper**：

###### Unsupervised Representation Learning with Deep Convolutional Generative Adversarial Networks Alec Radford, Luke Metz, Soumith Chintala https://arxiv.org/abs/1511.06434

# DiscoGAN

在这种方法中，GAN要学习发现不同域之间的关系，然后在跨域迁移风格的时候保留方向、脸部特征等关键属性。

![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtDLTB5zxxVWEqH729fEbAl9GnWJ0wMs5wMHtGoXib1jRBibUwf6IcM43r6akiaPmCiav0jUXiaTOAj4GWw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**Paper**：

###### Learning to Discover Cross-Domain Relations with Generative Adversarial Networks Taeksoo Kim, Moonsu Cha, Hyunsoo Kim, Jung Kwon Lee, Jiwon Kim https://arxiv.org/abs/1703.05192

**官方PyTorch实现：**

###### https://github.com/SKTBrain/DiscoGAN

# DRAGAN

DRAGAN用一种梯度惩罚策略来避免退化的局部局部均衡，加快了训练速度，通过减少模式崩溃提升了稳定性。

**Paper**：

###### On Convergence and Stability of GANs Naveen Kodali, Jacob Abernethy, James Hays, Zsolt Kira https://arxiv.org/abs/1705.07215

# DualGAN

这种变体能够用两组不同域的无标签图像来训练图像翻译器，架构中的主要GAN学习将图像从域U翻译到域V，而它的对偶GAN学习一个相反的过程，形成一个闭环。

![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtBYiaWP4xfjjCB8CJYhEmm0FMMeF6zeEMRxe7rxF39J2FvMLicicFpNoCBngqsOr1IGPibhf7XMptLBPQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**Paper**：

###### DualGAN: Unsupervised Dual Learning for Image-to-Image Translation Zili Yi, Hao Zhang, Ping Tan, Minglun Gong https://arxiv.org/abs/1704.02510

# GAN

对，就是Ian Goodfellow那个原版GAN。

![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtBYiaWP4xfjjCB8CJYhEmm0FujiblJ9y4t7UzqD6icEKWjElkMbD3gCcvAKxD7pxskxFD8d2oEoiaAOKA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**Paper**：

###### Generative Adversarial Networks Ian J. Goodfellow, Jean Pouget-Abadie, Mehdi Mirza, Bing Xu, David Warde-Farley, Sherjil Ozair, Aaron Courville, Yoshua Bengio https://arxiv.org/abs/1406.2661

# Least Squares GAN

最小平方GAN（LSGAN）的提出，是为了解决GAN无监督学习训练中梯度消失的问题，在鉴别器上使用了最小平方损失函数。

**Paper**：

###### Least Squares Generative Adversarial Networks Xudong Mao, Qing Li, Haoran Xie, Raymond Y.K. Lau, Zhen Wang, Stephen Paul Smolley https://arxiv.org/abs/1611.04076

# Pix2Pix

这个模型大家应该相当熟悉了。它和CycleGAN出自同一个伯克利团队，是CGAN的一个应用案例，以整张图像作为CGAN中的条件。

![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtBYiaWP4xfjjCB8CJYhEmm0Ff3oeXUlI6q56OW14WydtfnFGauZZfAkRVR5RlxTrVjb874YT6Qw87Q/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

在它基础上，衍生出了各种上色Demo，波及[猫](http://mp.weixin.qq.com/s?__biz=MzIzNjc1NzUzMw==&mid=2247484309&idx=3&sn=8c9884e899b590bb675c7773e0b6f250&chksm=e8d3b4e7dfa43df1d656380d8f2059bea2a14a9f97990a05a2ea111a8f4eda2a44e4d6dfd23c&scene=21#wechat_redirect)、[人脸](http://mp.weixin.qq.com/s?__biz=MzIzNjc1NzUzMw==&mid=2247486048&idx=2&sn=be762df28877eb59a1f75bfea10c0e73&chksm=e8d3bd12dfa43404f2cbf790454145593c1117c6ea607c5ff2e0974bc7974771da78ba91c33f&scene=21#wechat_redirect)、房子、包包、[漫画](http://mp.weixin.qq.com/s?__biz=MzIzNjc1NzUzMw==&mid=2247492663&idx=1&sn=3f9df390e6eea05159510ab1601895d6&chksm=e8d05345dfa7da5331d10cabc6bd65cbd91d2e102f6e05ea9db179dd894b61d3ec2321a04307&scene=21#wechat_redirect)等各类物品，甚至还有人用它来[去除（爱情动作片中的）马赛克](http://mp.weixin.qq.com/s?__biz=MzIzNjc1NzUzMw==&mid=2247486669&idx=6&sn=f74e6b03f9c066d77d42b034f001e050&chksm=e8d3bbbfdfa432a92f034a7b6333604a42b747b957ba49d4ec47568d73b51b49a16dc21cb5a3&scene=21#wechat_redirect)。

![img](https://mmbiz.qpic.cn/mmbiz_jpg/YicUhk5aAGtBYiaWP4xfjjCB8CJYhEmm0Fw7rc74hzksh1XPtEEbrdfd7juGC0umKHumx72uXXohlsdjlic19ic3dg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**Paper**:

###### Image-to-Image Translation with Conditional Adversarial Networks Phillip Isola, Jun-Yan Zhu, Tinghui Zhou, Alexei A. Efros https://arxiv.org/abs/1611.07004

Pix2Pix目前有开源的Torch、PyTorch、TensorFlow、Chainer、Keras模型，详情见项目主页：

###### https://phillipi.github.io/pix2pix/

# PixelDA

这是一种以非监督方式学习像素空间跨域转换的方法，能泛化到训练中没有的目标类型。

**Paper**：

###### Unsupervised Pixel-Level Domain Adaptation with Generative Adversarial Networks Konstantinos Bousmalis, Nathan Silberman, David Dohan, Dumitru Erhan, Dilip Krishnan https://arxiv.org/abs/1612.05424

# Semi-Supervised GAN

半监督生成对抗网络简称SGAN。它通过强制让辨别器输出类别标签，实现了GAN在半监督环境下的训练。

**Paper**:

###### Semi-Supervised Learning with Generative Adversarial NetworksAugustus Odenahttps://arxiv.org/abs/1606.01583

# Super-Resolution GAN

超分辨率生成对抗网络简称SRGAN，将GAN用到了超分辨率任务上，可以将照片扩大4倍。

![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtDLTB5zxxVWEqH729fEbAl9uWCHSk9CDwvg332ccEWuSc9pcHDh1LxcL4xL8V0AVhGFggZibPQrxaA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**Paper**：

###### Photo-Realistic Single Image Super-Resolution Using a Generative Adversarial Network Christian Ledig, Lucas Theis, Ferenc Huszar, Jose Caballero, Andrew Cunningham, Alejandro Acosta, Andrew Aitken, Alykhan Tejani, Johannes Totz, Zehan Wang, Wenzhe Shi https://arxiv.org/abs/1609.04802

# Wasserstein GAN

简称WGAN，在学习分布上使用了Wasserstein距离，也叫Earth-Mover距离。新模型提高了学习的稳定性，消除了模型崩溃等问题，并给出了在debug或搜索超参数时有参考意义的学习曲线。

本文所介绍repo中的WGAN实现，使用了DCGAN的生成器和辨别器。

**Paper**：

###### Wasserstein GAN Martin Arjovsky, Soumith Chintala, Léon Bottou https://arxiv.org/abs/1701.07875

# Wasserstein GAN GP

WGAN的改进版。

**Paper**：

###### Improved Training of Wasserstein GANs Ishaan Gulrajani, Faruk Ahmed, Martin Arjovsky, Vincent Dumoulin, Aaron Courville https://arxiv.org/abs/1704.00028

###

**GitHub地址**：https://github.com/eriklindernoren/PyTorch-GAN


# 相关

- [18种热门GAN的PyTorch开源代码 | 附论文地址](https://mp.weixin.qq.com/s?__biz=MzIzNjc1NzUzMw==&mid=2247497494&idx=6&sn=a1956065373bfae8ac12463be930cab4&chksm=e8d04064dfa7c9720779aaf3b6224b408133e285be383b90ef4c88a572661c80f5223b737eb2&mpshare=1&scene=1&srcid=0425b5LpFWDDrHiMFV0AXClK#rd)
