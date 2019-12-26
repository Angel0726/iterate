---
title: FAIR提出用聚类方法结合卷积网络 实现无监督端到端图像分类
toc: true
date: 2019-11-17
---
# FAIR提出用聚类方法结合卷积网络 实现无监督端到端图像分类

> 聚类是一种在计算机视觉被广泛应用和研究的无监督学习方法，但几乎未在大规模数据集上的视觉特征端到端训练中被采用过。在本文中，Facebook AI 研究院提出了深度聚类（DeepCluster），一种联合学习神经网络参数和获取特征的聚类分配的聚类方法。在 ImageNet 和 YFCC100M 等典型规模数据集上的卷积神经网络的无监督训练的实验结果表明，该方法在所有基准性能中都远远优于目前的技术。



预训练的卷积神经网络，或称卷积网络，已经成为大多数计算机视觉应用的基础构建模块 [1,2,3,4]。它们能提取极好的通用特征，用来提高在有限数据上学习的模型的泛化能力 [5]。大型全监督数据集 ImageNet[6] 的建立促进了卷积网络的预训练的进展。然而，Stock 和 Cisse [7] 最近提出的经验证据表明，在 ImageNet 上表现最优的分类器的性能在很大程度上被低估了，而且几乎没有遗留错误问题。这在一定程度上解释了为什么尽管近年来出现了大量新架构，但性能仍然饱和 [2,8,9]。事实上，按照今天的标准，ImageNet 是相对较小的；它「仅仅」包含了一百万张涵盖各个领域的分类图片。所以建立一个更大更多样化，甚至包含数十亿图片的数据集是顺理成章的。而这也将需要大量的手工标注，尽管社区多年来积累了丰富的众包专家知识 [10]，但通过原始的元数据代替标签会导致视觉表征的偏差，从而产生无法预测的后果 [11]。这就需要在无监督的情况下对互联网级别的数据集进行训练的方法。



无监督学习在机器学习社区 [12] 中得到了广泛的研究，在计算机视觉应用中也经常使用聚类、降维或密度估计算法 [13,14,15]。例如，「特征包」模型使用手工标注的描述符的聚类来生成良好的图像级特征 [16]。它们取得成功的一个关键原因是，它们可以应用于任何特定的领域或数据集，如卫星或医学图像，或使用一种新的模态 (如物体深度) 获取的图像，在这种模式下，无法获得大量的标注。有几项研究表明，可以将基于密度估计或降维的无监督方法应用到深度模型中 [17,18]，从而产生良好的通用视觉特征 [19,20]。尽管聚类方法在图像分类方面取得了初步的成功，但很少有人提出将其用于对卷积网络进行端到端训练 [21,22]，而且未成规模。问题是，聚类方法主要是为固定特征的线性模型设计的，如果必须同时学习特征，那么它们几乎不起作用。例如，使用 k-means 学习一个卷积网络将得到零特征的平凡解，并且聚类会坍缩成单个实体。



在本文中，FAIR 的研究者提出了一种为卷积网络进行大规模端到端训练的聚类方法。他们证明了用聚类框架获得有用的通用视觉特征是可实现的。该方法如图 1 所示，是在图像描述符的聚类和通过预测聚类分配来更新卷积网络的权值之间进行交替。简单起见，我们将研究重点放在 k-means 上，但其他聚类方法也适用，比如幂迭代聚类 (PIC)[23]。整个过程重用许多常见的技巧，与卷积网络的标准监督训练十分相似 [24]。与自监督方法 [25,26,27] 不同，聚类的优点是不需要太多专业知识，也不需要输入特定信号 [28,29]。尽管此方法很简单，但它在 ImageNet 分类和迁移任务上都比以前提出的非监督方法有更好的表现。



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8xAuhgicibWKTfGF6GiaWRuLB8TUgUTlORGF1wuO5y7aiaxlfRohbdwlqBoTfl2mkboEzoD8hHEqsyxA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*图 1：本文提出方法的图示：对深层特征进行迭代地聚类，并使用聚类赋值作为伪标签来学习卷积网络的参数。*



最后，通过修改实验方案，特别是训练集和卷积网络的结构，研究者对框架的鲁棒性进行了探究。得到的实验集对 Doersch 等人 [25] 的讨论做了扩展，即关于这些选择对无监督方法性能的影响。他们证明了本文的方法使架构更具鲁棒性。用 VGG[30] 代替 AlexNet 可以显著提高特征质量和迁移性能。更重要的是，他们讨论使用 ImageNet 作为非监督模型的训练集。虽然它有助于理解标签对网络性能的影响，但是 ImageNet 有一个基于细粒度图像分类挑战的特定图像分布集：它由均衡的类组成，例如包含各类犬种。作为替代方案，可以从 Thomee 等人的 YFCC100M 数据集中选择随机的 Flickr 图片 [31]。他们的方法在对这种未确定的数据分布进行训练时有当前最佳的性能。最后，目前的基准测试侧重于无监督卷积网络捕捉类级信息的能力。研究者还建议在图像检索基准上对它们进行评估，以测量它们捕捉实例级信息的能力。



在本文中，研究者做出了以下贡献：(i) 提出一种新的无监督方法来实现卷积网络的端到端学习，这种方法可以使用任何标准的聚类算法，比如 k-means，并且只需要很少的额外步骤；(ii) 在使用无监督学习的许多标准迁移任务中达到当前最佳水平；(iii) 对未处理的图像分布进行训练时，表现优于先前的最先进技术水平；(iv) 讨论了无监督特征学习中的目前评估方案。



**论文：Deep Clustering for Unsupervised Learning of Visual Features**



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8xAuhgicibWKTfGF6GiaWRuLByhgHQdZicWbxGd4TvsQg7zHgiaI99ndPovBIpYGox4fs9A7zDT1MaoHA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



论文地址：https://arxiv.org/abs/1807.05520v1



**摘要：**聚类是一种在计算机视觉被广泛应用和研究的无监督学习方法，但几乎未在大规模数据集上的视觉特征端到端训练中被采用过。在本文中，我们提出了深度聚类（DeepCluster），这是一种联合学习神经网络参数和获取特征的聚类分配的聚类方法。深度聚类使用标准的聚类算法 k-means 对特征进行迭代分组，随后使用赋值作为监督来更新网络的权重。我们将深度聚类应用于 ImageNet 和 YFCC100M 这样的大型数据集上的卷积神经网络的无监督训练。最终模型在所有基准性能中都远远优于目前的技术。



**实验**



在初步的实验中，研究团队研究了深度聚类在训练过程中的行为。然后，在标准基准上将其方法与之前最先进的模型进行比较之前，并对深度聚类学习的滤波器进行了定性评估。



**可视化**



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8xAuhgicibWKTfGF6GiaWRuLBUickvK6W5sYbJ1Epd9UeOeVOwX90L5mex8KJJtiadcFdlRF2Ng6E3lAw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*图 3：在原始 RGB 输入 (左) 或 Sobel 滤波 (右) 之后，在无监督的 ImageNet 上训练的 AlexNet 的第一层滤波器的卷积结果。*



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8xAuhgicibWKTfGF6GiaWRuLBNCvXu4qUiaT2cjSic5wSMSmFw5c3SKfrcvTDrtrCyf11YbICYsUxZdcA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*图 4：滤波器可视化和来自 YFCC100M 的 100 万个图像子集中的前 9 个激活图像，用于在 ImageNet 上使用深度聚类训练的 AlexNet 的 conv1、conv3 和 conv5 中的目标滤波器。滤波器的可视化是通过学习一个输入图像来获得的，该图像最大化目标滤波器的响应 [64]。*



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8xAuhgicibWKTfGF6GiaWRuLB26WQS00IBXfM63VH8uiaFL2AheIiaK0JzOBP3sNInfc8nTRzyd3Z3hcg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*图 5：来自 YFCC100M 的 1000 万个图像的随机子集中的前 9 个激活图像，用于最后卷积层中的目标滤波器。顶行对应的是对包含物体的图像敏感的滤波器。底行展示了对风格效果更敏感的滤波器。例如，滤波器 119 和 182 似乎分别被背景模糊和景深效应激活。*



**激活值的线性分类**



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8xAuhgicibWKTfGF6GiaWRuLB4iatnapobHP2fT2iaiafe2fVfEXdJXcTpgVfMqw6D5xPbvY2dTJYW3u1A/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*表 1：使用 AlexNet 的卷积层的激活值作为特征的 ImageNet 和 Places 上的线性分类。报告的分类准确率平均超过 10 种作物。其他方法的数字来自 Zhang et al[43]。*



**Pascal VOC 2007**



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8xAuhgicibWKTfGF6GiaWRuLB9JyGFiazKDpDfFxXflub5VYH3AoUB64xNpCrnT1iblQiaVl8dIuCQBn4Q/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*表 2：对 Pascal VOC 的分类、检测和分割的最新无监督特征学习方法的比较。∗表明 Krahenbuhl 等人使用数据依赖初始化 [68]。其他方法产生的数字被标记为 a †。*![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8Zfpicd40EribGuaFicDBCRH6IOu1Rnc4T3W3J1wE0j6kQ6GorRSgicib0fmNrj3yzlokup2jia9Z0YVeA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)


# 相关

- [学界 | FAIR提出用聚类方法结合卷积网络，实现无监督端到端图像分类](https://mp.weixin.qq.com/s?__biz=MzA3MzI4MjgzMw==&mid=2650746575&idx=3&sn=5ac5ff2284b18b3d948369f63b09ff3a&chksm=871aeab1b06d63a7101f58d372d0e02aec217ce63df72364d198bc746a5975155608f10ca650&mpshare=1&scene=1&srcid=0806P01riMR6I3h3PqTnVzNm#rd)
