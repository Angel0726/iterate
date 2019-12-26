---
title: DeepMind CNN的变形稳定性和池化无关 滤波器平滑度才是关键
toc: true
date: 2019-11-17
---
# DeepMind论文：CNN的变形稳定性和池化无关，滤波器平滑度才是关键



> 传统观点认为，CNN 中的池化层导致了对微小平移和变形的稳定性。在本文中，DeepMind 的研究者提出了一个反直觉的结果：CNN 的变形稳定性仅在初始化时和池化相关，在训练完成后则无关；并指出，滤波器的平滑度才是决定变形稳定性的关键因素。



**1. 引言**



近年来，卷积神经网络（CNN）在计算机视觉的物体识别方面取得了巨大的成功（Krizhevsky et al., 2012; Simonyan & Zisserman, 2014; He et al., 2016; Russakovsky et al., 2015），然而目前尚不清楚这些模型如此成功的原因。



直到最近，人们才对 CNN 成功的原因有了一个普遍的解释，解释说是因为交错地引入池化层（interleaved pooling layer）才使这些模型对小的平移和变形（translation and deformation）不敏感。视觉域中的许多变化来自视图、物体位置、旋转、尺寸和非刚体变形的微小变化。因此，「对这些变化不太敏感」这一描述有用，但只是看起来合理。此外，长久以来人们都假设引入交错池化层将这种偏差构建到的模型中是有益的 (LeCun et al., 1990; Krizhevsky et al., 2012; Simonyan & Zisserman, 2014; LeCun et al., 2015; Giusti et al., 2013）。然而，这个假设的解释还没有被彻底地验证。



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW92DQfG3m5xcgEVp8mAmf7vHN3Uic4HcDHI1RJJia962EmSfjibpKVZ6N0zc9gS2O2CH8TD4LODDhVFw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*图 1. 变形的 ImageNet 图像示例。左：原始图像，右：变形图像。虽然图像变化了很多，比如，在 L2 度量下，他们可能会被人类赋予相同的标签。*



事实上，最近证明，由交错池化层提供的归纳偏差（inductive bia）并不是良好性能的必要条件，因为最近的一些架构已经减少了交错池化层，而且仍然实现了强大的性能（Springenberg et al., 2014; He et al., 2016）。这引出了本文中的以下问题：



 \1. 池化是否对学习的变形稳定性是否有影响？

\2. 在没有池化的情况下是否能实现变形稳定性？

\3. 如果可以，是如何实现的?



关于池化作用的传统推理是假设混淆变量（nuisance variables）的不变性在总体上有帮助。这里，本文对其有效性做出了一个猜想，并进一步定义了本文将讨论的特定类别的混淆变形（nuisance deformations）。



本文的主要贡献是：



- 学习稳定性：本文展示了没有池化的网络在初始化时对变形敏感，但经过训练学习表征的过程之后对变形是稳定的。作者还表明，即使在有池化的网络中，训练过程中变形稳定性模式也会发生显著变化。此外，在训练过程中，变形稳定性有时会下降，这表明这种稳定性不是单方面的有用（3.2 节）。
- 收敛稳定性：本文表明池化和非池化训练网络的层间变形稳定性模式最终会收敛到相似的结构（3.3 小节）。
- 稳定性的实现：本文表明无论池化还是非池化网络，都可通过滤波器的平滑性实现和调节变形稳定性（第 4 节）。



此外，从理解神经网络的权重和层如何影响整个网络行为的角度来看，此工作提供了一个有潜在价值的重要例子，解释各层中权重的简单性质如何影响网络的整体计算。



从设计神经网络模型的角度来看，这项工作提供了对「指导设计神经网络 20 多年的重要归纳偏差」的洞察。长期以来人们认为池化对实现变形稳定性很重要，认为池化是 CNN 成功的主要因素。这项工作表明，无论看起来多么合理，并通过经验和理论验证来加强，我们对神经网络工作原理的直觉往往是不准确的。



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW92DQfG3m5xcgEVp8mAmf7vVEhcuhgSaYV0VldV7VwU0JehduBflaK7icaBpfDybndKCib7cBmVBWRw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*图 2：生成变形图像：为了使图像随机变形，我们：（a）从固定均匀间隔的控制点网格开始（这里是 4x4 个控制点），然后在点邻域内为每个控制点选择一个随机源；(b) 然后使用薄板插值平滑得到的矢量场；(c) 矢量场叠加在原始图像上：使用原始图像中箭头尾部附近的值的双线性插值计算箭头顶端的最终结果中的值；(d) 最终结果。*



**3. 在池化和非池化的网络中学习变形稳定性是相似的**



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW92DQfG3m5xcgEVp8mAmf7vnYhlZUj5rRHEdH510ozicltwzicxhqw4hEzLicd1P1JLR0WS3zSIG4Hibg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*图 3：池化在初始化时赋予变形稳定性，但在训练过程中稳定性发生显着变化，而且无论是否池化，都会收敛到类似的稳定性。(a) 在初始化时，最大池化的网络对变形较不敏感。(b) 训练后，池化和非池化的网络对层的变形有非常相似的敏感模式。CIFAR10 有类似的模式： (c) 初始化时，池化对变形的敏感性有显着影响，但 (d) 训练后，下采样层的选择对所有层的变形稳定性几乎没有影响。图层 0 对应于输入图像；这些层包括下采样层；最后一层对应于最后的下采样层。因此对于 CIFAR10，我们一共有 13 层，包括 1 个输入层、8 个卷积层和 4 个池化层。*



**4. 滤波器的平滑度有助于提高变形稳定性**



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW92DQfG3m5xcgEVp8mAmf7vAx7HVf8jN3iabLib41GTFsqLnADq0XsmKA2Uf7d3XHUIdHYva0ibTRYtA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*图 4：使用更平滑的随机滤波器进行初始化会使变形稳定性更好。使用标准偏差σ的高斯滤波器对滤波器进行平滑处理，然后测量对变形的敏感度。当增加σ来增加滤波器的平滑度时，表征对变形的敏感度下降。较深的线条代表更平滑的随机滤波器。*



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW92DQfG3m5xcgEVp8mAmf7vQqfYMnY3cpUkxklBfqiaDufxjVtFc7qA15RCqblUWqqx9mcCZIEAaaA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*图 5：需要更高变形稳定性的任务要用更平滑的滤波器。(a) 生成一个合成任务，每个类基于单个 MNIST 图像，每类的示例通过应用该类图像的强度 C 的随机变形生成。左边的图像使用强度 3 的变形生成，右列图像分别使用强度为 1、2、3、4 的变形生成。(b) 训练后，在强变形训练任务上得到的网络滤波器更平滑。黑色虚线表示初始化的平均值。*



**5：滤波平滑性取决于监督任务类型**



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW92DQfG3m5xcgEVp8mAmf7vpU30HABlhMzZ9wlrUNnfrYClkaKMJxN0mwX6qvnR5V9V7CyfC2sB7Q/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*图 6：训练得到更平滑的滤波器。(a) 和 (b) 在训练之后，滤波器明显更加平滑，不同的架构收敛到类似的滤波平滑度。（c）对随机标签进行训练时，滤波器的平滑性很大程度取决于选择的下采样层。有趣的是，（a）训练 ImageNet 时滤波器的平滑度逐层递增，（b）CIFAR10 则是逐层递减。黑色虚线表示初始化的平均值。*



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW92DQfG3m5xcgEVp8mAmf7vBeUibkKFp9L9XYiab6Jb8zr6LpAFAuBMglNhtaTIxKiaX61VJ12m4abew/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*图 7：训练随机标签时，变形稳定性依赖于架构类型。*



**论文：Learned Deformation Stability in Convolutional Neural Networks（卷积神经网络的学习变形稳定性）**



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW92DQfG3m5xcgEVp8mAmf7v5Y61OdKJsxyYZd4DkImeJY8OTaNcfneqzXxBUbxa5nicOsuV7BOvTCg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



论文地址：https://arxiv.org/pdf/1804.04438.pdf



传统观点认为，卷积神经网络中的池化层导致了对微小平移和变形的稳定性。此项工作中，我们根据经验探索了这一观点。我们发现，虽然池化层在初始化时赋予网络变形稳定形，但在训练的过程中每层的变形稳定性变化显著，一些层甚至有所减小，这表明变形稳定性不是单方面有帮助的。令人惊讶的是，训练完成之后，层间的变形稳定性模式很大程度上与是否引入池化无关。然后我们在本文展示了决定变形稳定性的一个重要因素是滤波器的平滑度。此外，滤波器的平滑度和变形稳定性不仅是输入图像分布的结果，而且关键地取决于图像和标签的联合分布。本项工作展示了学习变形稳定性等偏差的一种方法，并提供了「理解学习网络权重的简单性质如何有助于对整体网络的计算」的一个例子。![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8Zfpicd40EribGuaFicDBCRH6IOu1Rnc4T3W3J1wE0j6kQ6GorRSgicib0fmNrj3yzlokup2jia9Z0YVeA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)


# 相关

- [学界 | DeepMind论文：CNN的变形稳定性和池化无关，滤波器平滑度才是关键](https://mp.weixin.qq.com/s?__biz=MzA3MzI4MjgzMw==&mid=2650741674&idx=4&sn=8b27bc3a98455c8797ee257e961b86ac&chksm=871adfd4b06d56c295e5c95bf02cc5bd672ed4fef241b947cbf0fcf0829737069d6f00685dfd&mpshare=1&scene=1&srcid=0501GvEEMppdH6w06K5EOzuH#rd)
