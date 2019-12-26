---
title: GANimation
toc: true
date: 2019-11-17
---
# GAN如此简单的PyTorch实现，一张脸生成72种表情

- 研究展示：http://www.albertpumarola.com/#projects
- 项目地址：https://github.com/albertpumarola/GANimation



GANimation构建了一种人脸解剖结构（anatomically）上连续的面部表情合成方法，能够在连续区域中呈现图像，并能处理复杂背景和光照条件下的图像。


**若是能单凭一张图像就能自动地将面部表情生成动画，那么将会为其它领域中的新应用打开大门**，包括电影行业、摄影技术、时尚和电子商务等等。随着生成网络和对抗网络的流行，这项任务取得了重大进展。像StarGAN这样的结构不仅能够合成新表情，还能改变面部的其他属性，如年龄、发色或性别。虽然StarGAN具有通用性，但它只能在离散的属性中改变面部的一个特定方面，例如在面部表情合成任务中，对RaFD数据集进行训练，该数据集只有8个面部表情的二元标签（binary label），分别是悲伤、中立、愤怒、轻蔑、厌恶、惊讶、恐惧和快乐。



**GANimation的目的是建立一种具有FACS表现水平的合成面部动画模型，并能在连续领域中无需获取任何人脸标志（facial landmark）而生成具有结构性（anatomically-aware）的表情。**为达到这个目的，我们使用EmotioNet数据集，它包含100万张面部表情(使用其中的20万张)图像。并且构建了一个GAN体系结构，其条件是一个一维向量：表示存在/缺失以及每个动作单元的大小。我们以一种无监督的方式训练这个结构，仅需使用激活的AUs图像。为了避免在不同表情下，对同一个人的图像进行训练时出现冗余现象，将该任务分为两个阶段。首先，给定一张训练照片，考虑一个基于AU条件的双向对抗结构，并在期望的表情下呈现一张新图像。然后将合成的图像还原到原始的样子，这样可以直接与输入图像进行比较，并结合损失来评估生成图像的照片级真实感。此外，该系统还超越了最先进的技术，因为它可以在不断变化的背景和照明条件下处理图像。



最终，构建了一种结构上连续的面部表情合成方法，能够在连续区域中呈现图像，并能处理复杂背景和光照条件下的图像。它与其他已有的GAN方法相比，无论是在结果的视觉质量还是生成的可行性上，都是具有优势的。



![img](https://mmbiz.qpic.cn/mmbiz_png/UicQ7HgWiaUb0eFyX5TeOWqmoj3Pdp4ZbVY89tYJboeJkjuv86Irxp7b3a8eib68FnibXHuECrREubY8w4MYKyHzyg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

图1：根据一张图像生成的面部动画



无监督学习+注意力机制



让我们将一个输入RGB图像定义为![img](https://mmbiz.qpic.cn/mmbiz_png/UicQ7HgWiaUb0eFyX5TeOWqmoj3Pdp4ZbVYK4eBe19wvS9gYzNhy6DleVszKXGia9AZBdMDeGKwx6PMjPcgia5v2EA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)，这是在任意面部表情下捕获的。通过一组N个动作单元![img](https://mmbiz.qpic.cn/mmbiz_png/UicQ7HgWiaUb0eFyX5TeOWqmoj3Pdp4ZbVnIKjRofEncIhxOiaW8j2sXiasXNrLhUyu4V9fJYtH1rkFJSvjO9P9QxA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)对每个手势表达式进行编码，其中每个![img](https://mmbiz.qpic.cn/mmbiz_png/UicQ7HgWiaUb0eFyX5TeOWqmoj3Pdp4ZbVQ0ZnyeI5LUkUXeXEHs471kHRbSSHhCvewUWJWP2VBZmx55icpLh3zVQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)表示0到1之间的归一化值，表示第n个动作单元的大小。值得指出的是，由于这种连续的表示，可以在不同表情之间进行自然插值，从而可以渲染各种逼真、流畅的面部表情。



我们的目标是学习一个映射![img](https://mmbiz.qpic.cn/mmbiz_png/UicQ7HgWiaUb0eFyX5TeOWqmoj3Pdp4ZbVJiacevfqyreH3YMvCTg6Qe3XXxj3KOByO7ImO8226wnMGZuic4PicL7bQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)，将![img](https://mmbiz.qpic.cn/mmbiz_png/UicQ7HgWiaUb0eFyX5TeOWqmoj3Pdp4ZbVZMbBxzib3O5Gb7cFNmsqF4ibbD6QFlGFbrBVernptIfSMSEPcgVefabA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)转换成一个基于动作单元目标![img](https://mmbiz.qpic.cn/mmbiz_png/UicQ7HgWiaUb0eFyX5TeOWqmoj3Pdp4ZbV2jSxEhKBCZyZ6pTZCbzoXwibn41TpWpAB6cIm4XJ6Sw6Fuic5AQ8HtdA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)的输出图像![img](https://mmbiz.qpic.cn/mmbiz_png/UicQ7HgWiaUb0eFyX5TeOWqmoj3Pdp4ZbVuxd2121oRw46KZjica9ibIJLdIgUxgj1uhfAISYMDVbCySbMFEcvHRIw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)，即：我们希望估计映射：

![img](https://mmbiz.qpic.cn/mmbiz_png/UicQ7HgWiaUb0eFyX5TeOWqmoj3Pdp4ZbVibvDia21ICYvJ7tQWf5Dk2lr25dmFIypleApxBaBChtRsHCHvZRgF6hQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



![img](https://mmbiz.qpic.cn/mmbiz_png/UicQ7HgWiaUb0eFyX5TeOWqmoj3Pdp4ZbVz2yRwicBkB69NFsIv5c8qGxVl5pXsVWzAoBriakCTo6E9rvSvcwK8Mkg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

图2. 生成照片级真实条件图像方法的概述



所提出的架构由两个主要模块组成：用于回归注意力和 color mask 的**生成器G**; 用于评估所生成图像的真实度![img](https://mmbiz.qpic.cn/mmbiz_png/UicQ7HgWiaUb0eFyX5TeOWqmoj3Pdp4ZbVG0aFD8Xu7rVHOqj9plwh55xq0icf9bJKhmRP5WGstzppnzWzm5QH4AA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)和表情调节实现![img](https://mmbiz.qpic.cn/mmbiz_png/UicQ7HgWiaUb0eFyX5TeOWqmoj3Pdp4ZbVZlYGYCFVrwUL2NDLYj6ZZiclkjQ9ZYKILdeHiaRa70jdILkHwk7QJdBg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)的**评论家（critic） D**。



我们的系统不需要监督，也就是说，不需要同一个人不同表情的图像对，也不假设目标图像![img](https://mmbiz.qpic.cn/mmbiz_png/UicQ7HgWiaUb0eFyX5TeOWqmoj3Pdp4ZbVuxd2121oRw46KZjica9ibIJLdIgUxgj1uhfAISYMDVbCySbMFEcvHRIw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)是已知的。



**生成器G**



生成器器![img](https://mmbiz.qpic.cn/mmbiz_png/UicQ7HgWiaUb0eFyX5TeOWqmoj3Pdp4ZbVOccPxlsrKaxdYq1RD9dByQ5zEwjqxmH8RwUwlJ3GBpsLkF8XRvR0bw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)被训练来逼真地将图像![img](https://mmbiz.qpic.cn/mmbiz_png/UicQ7HgWiaUb0eFyX5TeOWqmoj3Pdp4ZbVZMbBxzib3O5Gb7cFNmsqF4ibbD6QFlGFbrBVernptIfSMSEPcgVefabA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)中的面部表情转换为期望的![img](https://mmbiz.qpic.cn/mmbiz_png/UicQ7HgWiaUb0eFyX5TeOWqmoj3Pdp4ZbV2jSxEhKBCZyZ6pTZCbzoXwibn41TpWpAB6cIm4XJ6Sw6Fuic5AQ8HtdA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)。



我们系统的一个关键要素是使G只**聚焦于图像的那些负责合成新表情的区域**，并保持图像的其余元素如头发、眼镜、帽子、珠宝等不受影响。为此，我们在生成器中嵌入了一个注意力机制。



![img](https://mmbiz.qpic.cn/mmbiz_png/UicQ7HgWiaUb0eFyX5TeOWqmoj3Pdp4ZbVSYciaBqTmJNVoia1OviaBqYK5ibmqyiblxNibjvgibFMic1k7Mmkd8Xaz2oh8Q/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

图3：Attention-based的生成器



给定一个输入图像和目标表情，生成器在整个图像上回归并注意mask A和RGB颜色变换C。attention mask 定义每个像素强度，指定原始图像的每个像素在最终渲染图像中添加的范围。



具体地说，生成器器不是回归整个图像，而是输出两个mask，一个color mask C和一个attention mask A。最终图像可表示为：

![img](https://mmbiz.qpic.cn/mmbiz_png/UicQ7HgWiaUb0eFyX5TeOWqmoj3Pdp4ZbVAN9uXmCy232nByTfXMrtSqciarNJiatzfKacfPYcCpwKVrUZCKJJRuSw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



实验评估



**首先测试主要组件，即单个和多个AU编辑。然后将我们的模型与离散化情绪编辑任务中的当前技术进行比较，并展示我们的模型处理野外图像的能力，可以生成大量的解剖学面部变换的能力。**最后讨论模型的局限性和失败案例。



值得注意的是，在某些实验中，输入的面部图像是未被裁剪的。在这种情况下，我们首先使用检测器2来对面部进行定位和裁剪，利用（1）式进行表达式的转换，以应用于相关区域。 最后，将生成的面部图像放回原图像中的原始位置。注意力机制（attention mechanism）可以确保经过变换处理的裁剪面部图像和原始图像之间的平滑过渡。



稍后图中可见，与以前的模型相比，经过这三个步骤的处理可以得到分辨率更高的图像（链接见文末）。



![img](https://mmbiz.qpic.cn/mmbiz_png/UicQ7HgWiaUb0eFyX5TeOWqmoj3Pdp4ZbVZF7VlwSDfLY1XwEMKk1iauWf1wp0aeXpJPpPb10wuWZ2lOUicEVFhN6w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

图4：单个动作单元的编辑



随着强度（0.33-1）的增加，一些特定的动作单元被激活。图中第一行对应的是动作单元应用强度为零的情况，可以在所有情况下正确生成了原始图片。



![img](https://mmbiz.qpic.cn/mmbiz_png/UicQ7HgWiaUb0eFyX5TeOWqmoj3Pdp4ZbVDibXibW5cg9PoSPDicbOO5sH6pUCp4QsFicKJaBSMYEJkhztFw6Tn3Xib4A/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

图5： 注意力模型



中间注意力掩模A（第一行）和颜色掩模C（第二行）的细节。 最底下一行图像是经合成后的表达结果。注意掩模A的较暗区域表示图像的这些区域与每个特定的动作单元的相关度更高。 较亮的区域保留自原始图像。



![img](https://mmbiz.qpic.cn/mmbiz_png/UicQ7HgWiaUb0eFyX5TeOWqmoj3Pdp4ZbV36O0icQQxLx8cz6MrlootDmhydIxcMT8lInJFIqAWicaseYWYfsMe0Cg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

 图6： 与当前最先进技术的定性比较



图为面部表情图像合成结果，分别应用DIAT、CycleGAN、IcGAN、StarGAN和我们的方法。可以看出，我们的解决方案在视觉准确度和空间分辨率之间达到了最佳平衡。 使用StarGAN的一些结果则出现了一定程度的模糊。



![img](https://mmbiz.qpic.cn/mmbiz_png/UicQ7HgWiaUb0eFyX5TeOWqmoj3Pdp4ZbVhw8nXOVomuKk1Drroc0nCM6acFiaRxGm3qq9OPIwAGG5GIqs7SsatLw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

图7：采样面部表情分布空间



通过yg向量对活动单元进行参数化，可以从相同的源图像合成各种各样的照片的真实图像。



![img](https://mmbiz.qpic.cn/mmbiz_png/UicQ7HgWiaUb0eFyX5TeOWqmoj3Pdp4ZbVmaDiaP6PWibBlqrXrdtUv1L8rY6MfgH79X1Gj0N0qrykdtleNT77TJpA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

图8：自然图像的定性评估



上图：分别给出了取自电影《加勒比海盗》中的一幅原图像（左）及其用我们的方法生成的图像（右）。 下图：用类似的方式，使用图像框（最左绿框）从《权力的游戏》电视剧中合成了五个不同表情的新图像。



![img](https://mmbiz.qpic.cn/mmbiz_png/UicQ7HgWiaUb0eFyX5TeOWqmoj3Pdp4ZbVLOJ9aTGiaCAvibhxjrKrarglbsZyexFdzdVdzyK8MKTDJVzNR4jic5Ekg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

图9：成功和失败案例



图中分别表示了源图像Iyr，目标Iyg，以及颜色掩膜C和注意力掩模A. 上图是在极端情况下的一些成功案例。 下图是一些失败案例。



文献参考地址

论文：https://arxiv.org/abs/1807.09251

代码：http://www.albertpumarola.com/research/GANimation/


# 相关

- [GAN如此简单的PyTorch实现，一张脸生成72种表情（附代码）](https://mp.weixin.qq.com/s?__biz=MzI3MTA0MTk1MA==&mid=2652023495&idx=2&sn=bbe1500fa0e4f08ccc62009bd6677590&chksm=f121de36c65657203ede5ad0f97d8ee90508bc2fe67e2dece98b920b44de35bfeedaae5ad8e1&mpshare=1&scene=1&srcid=0729Rv9ZghcrP50mw0HbyjPL#rd)
