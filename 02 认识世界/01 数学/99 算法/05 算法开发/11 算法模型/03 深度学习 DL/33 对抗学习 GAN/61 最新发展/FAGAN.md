---
title: FAGAN
toc: true
date: 2019-11-17
---
# FAGAN：完全注意力机制（Full Attention）GAN，Self-attention+GAN


近期，人工智能专家Animesh Karnewar提出FAGAN——**完全注意力机制（Full Attention）GAN，**实验的代码和训练的模型可以在他的github库中找到：

https://github.com/akanimax/fagan。



这个fagan示例使用了我创建的名为“attnganpytorch”的包，该包在我的另一个存储库中可以找到：

https://github.com/akanimax/attnganpytorch。



![img](https://mmbiz.qpic.cn/mmbiz_png/AefvpgiaIPw2ibly6Ajrw3Kayd0c8paQbGqtbibkID6rHLtXmrxmvEBYibV0If409SrLc6ekaAr7QjsxtLFPNiadWgg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



作者 | Animesh Karnewar

编译 | 专知

整理 | Mandy



**FAGAN: Full Attention GAN**



**介绍**



在阅读了SAGAN (Self Attention GAN)的论文后，我想尝试一下，并对它进行更多的实验。由于作者的代码还不可用，所以我决定为它编写一个类似于我之前的“pro-gan-pth”包的一个package。我首先训练了SAGAN论文中描述的模型，然后意识到，我可以更多地使用基于图像的注意机制。此博客是该实验的快速报告。



SAGAN 论文链接：

https://arxiv.org/abs/1805.08318



**Full Attention 层**



SAGAN体系结构只是在生成器和DCGAN体系结构的判别器之间添加了一个self attention层。此外，为了创建用于self attention的Q、K和V特征库，该层使用(1 x 1)卷积。我立即提出了两个问题：注意力（attention）能否推广到(k x k)卷积? 我们能不能创建一个统一的层来进行特征提取(类似于传统的卷积层)并同时进行attention?

我认为我们可以使用一个统一的注意力和特征提取层来解决这两个问题。我喜欢把它叫做full attention层，一个由这些层组成GAN架构就是一个Full Attention GAN.。



![img](https://mmbiz.qpic.cn/mmbiz_png/AefvpgiaIPw2ibly6Ajrw3Kayd0c8paQbGhcns9Y2M3e4HziaTmzribgd947z7rQwZmZAFAl7AVvt80JT8LqTYatdA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

图2：我所提出的full attention层



图2描述了所提出的full attention层的体系结构。 正如您所看到的，在上面的路径中，我们计算传统的卷积输出，在下面的路径中，我们有一个注意力层，它泛化成(k x k)卷积滤波器，而不仅仅是(1 x 1)滤波器。残差计算中显示的alpha是一个可训练的参数。



现在为什么下面的路径不是self attention？ 其原因在于，在计算注意力图（attention map）时，输入首先由（k×k）卷积在局部聚合，因此不再仅仅是self attention，因为它在计算中使用了一个小的空间邻近区域。 给定足够的网络深度和滤波器大小，我们可以将整个输入图像作为一个接受域进行后续的注意力计算，因此命名为：**全注意力（Full Attention）。**



**我的一些想法**



我必须说，当前的“Attention is all you need”的趋势确实是我这次实验背后的主要推动力。实验仍在进行中，但是我真的很想把这个想法说出来，并得到进一步的实验建议。



我意识到训练模型的alpha残差参数实际上可以揭示注意力机制的一些重要特征; 这是我接下来要做的工作。



attnganpytorch包中包含一个在celeba上训练的SAGAN示例，以供参考。该package包含了self attention、频谱归一化（normalization）和所提出的full attention层的通用实现，以供大家使用。所有这些都可以用来创建您自己的体系结构。



原文链接：

https://medium.com/@animeshsk3/fagan-full-attention-gan-2a29227dc014


# 相关

- [FAGAN：完全注意力机制（Full Attention）GAN，Self-attention+GAN](https://mp.weixin.qq.com/s?__biz=MzU1NTUxNTM0Mg==&mid=2247489198&idx=2&sn=4a870a6fc434a1b76d12917653f7c7ca&chksm=fbd27a0fcca5f3194451c86eba001f210d7c5cef4cb2204cdbd55f25074bcb0b8915c7520ea8&mpshare=1&scene=1&srcid=0814avjKmlk9hjx8RcRoYQXj#rd)
