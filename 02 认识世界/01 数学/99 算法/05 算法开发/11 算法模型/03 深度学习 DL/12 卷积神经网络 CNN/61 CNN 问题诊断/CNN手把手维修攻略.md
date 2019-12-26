---
title: CNN手把手维修攻略
toc: true
date: 2019-11-17
---
你的CNN还健在么？

Github用户**Zeyad Emam**贡献了一篇**CNN故障排除攻略**，详细介绍了CNN的常见故障和调教方法，量子位编译了中文版。

欢迎收藏并食用~

# 介绍

本文是一篇给卷积神经网络排查故障的攻略，主要来自于作者此前的经验和包括斯坦福CS231n课程笔记在内的线上资源。

本文主要针对使用深度神经网络进行的监督学习。虽然本攻略假设你用的是TensorFlow和Python3.6，不过本文内容编程语言无关，你可以当成是一篇通用的指南。

首先，假设我们现在有一个CNN，测试后发现它的表现比我们预想的差很多。于是，你就可以按照本攻略的步骤，一步一步来完成故障排除，之后你的神经网络可能就更合你心意了。

![img](https://mmbiz.qpic.cn/mmbiz_gif/YicUhk5aAGtC96DwSbWcbYNjNcF8rrSMo2Vma587Y0L81wXIwCqBQf9ZHzKA6pSHkBPM6Km9OEdaUwIjKKDrIOg/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

**△** 咦？这张图为什么点不开

第一部分是故障排除开始之前的必备事项，后面的则是针对具体问题的解决办法，问题主要集中在相对更为常见的方面，每个部分会优先给出针对该问题最容易实现的解决方法。

# 前菜：故障排除前

首先，给大家列一下完成深度学习算法时要遵循的最佳实践。

**1.使用适当的日志和有意义的变量名**。在TensorFlow中，你可以通过名称来跟踪不同的变量，并在TensorBoard中可视化图形。最重要的是，在每个训练步骤中，你都能记录相关的值，比如：step_number、accuracy、loss、learning_rate，甚至有时候还包括一些更具体的值，比如mean_intersection_over_union。之后，就可以画出每一步的损失曲线。

**2.确保您的网络连接正确**。使用TensorBoard或其他debug技术确保图中的每个操作的输入和输出都准确无误，还要确保在将数据和标签送入网络之前对其进行适当的预处理和配对。

**3.实施数据增强技术**。虽然这一点并不是对所有情况都适用，不过如果你在搞图像相关的神经网络，用简单的数据增强技术处理一下图像，例如镜像、旋转、随机裁剪和重新缩放、添加噪声、弹性变形等，大部分时候出来的效果都有巨大提升。

而且，TensorFlow内置了大多数基本的图像处理功能，十分良心了。

![img](https://mmbiz.qpic.cn/mmbiz_gif/YicUhk5aAGtC96DwSbWcbYNjNcF8rrSMorArcQdmuywB3UBPaekyLYXsJQEkkHIvLrPicCMREUq1S9mNoHF4ibTcA/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

**4.对所有层使用权重初始化和正则化**。不要把权重初始化为相同的值，当然你要是把它们都初始化成0……那就更糟了，这可能会引入对称性，并且导致梯度消失，大多数时候都会导致糟糕的结果。

一般情况下，如果你在权重初始化时遇到问题，你可以考虑在神经网络中添加**批量标准化层（Batch Normalization Layer）**。关于批量标准化层，可以看这篇名为《Batch Normalization: Accelerating Deep Network Training by Reducing Internal Covariate Shift》的论文，链接：arxiv.org/abs/1502.03167

**5.确保正则化条款不会压倒损失函数中的其他项**。关闭正则化，找出损失的数量级，然后适当调整正则化权重。确保在增加正则化强度时，损失也在增加。

**6.尝试过拟合一个小数据集**。关闭正则化/丢失/数据增强，拿出训练集的一小部分，让神经网络练它几个世纪，确保可以实现零损失，不然就很可能是错误的。

在某些情况下，将损失驱动为零非常具有挑战性，例如，如果您的损失涉及每个像素的softmax-ed logits和ground truth labels之间的交叉熵，那么在语义分割中可能真的难以将其降低到0。相反，你应该争取达到接近100％的准确度。

可以在tf.metrics.accuracy这里了解如何通过获取softmax-ed logits的argmax并将其与ground truth labels进行比较来计算。

**7.在过拟合上述小数据集的同时，找到合理的学习率**。Yoshua Bengio的论文中给到了结论：**最佳学习率通常接近最大学习率的一半，不会引起训练标准的差异**，这个观察结果是设置学习率的启发。例如，从较大的学习率开始，如果训练标准发散，就用最大学习率除以3再试试，直到观察不到发散为止。

**8.执行梯度检查**。如果您在图表中使用自定义操作，则梯度检查尤其重要。斯坦福CS231n中介绍了梯度检查的方法。

> 故障排除前的步骤主要来自于下面三篇资料，需要的朋友可复制链接查看：
>
> · **斯坦福CS231n中数据预处理部分**
> cs231n.github.io/neural-networks-2
> · **斯坦福CS231n中训练神经网络部分**
> cs231n.github.io/neural-networks-3
> · **Practical Recommendations for Gradient-Based Training of Deep Architectures**
> Yoshua Bengio
> arxiv.org/pdf/1206.5533v2.pdf

现在，开始进食主菜：

![img](https://mmbiz.qpic.cn/mmbiz_gif/YicUhk5aAGtC96DwSbWcbYNjNcF8rrSMoPSnwSPlTBeEsltreywUb1JJzpO25QnUXuEgyeUGAgsjFzuICmYxEWw/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

# 损失没有改善怎么办？

如果，训练了好几个Epoch，损失还是没有变小，甚至还越来越大，就要：

**1.****确认你用的损失函数是合适的，你优化的张量也是对的**。常用损失函数列表传送门：t.cn/RkZXji1。

**2.****用个好点的优化器**。这里也有常见优化器的列表：t.cn/RDKwbNA。

**3.****确认变量真的在训练**。要检查这个，就得看**张量板**的直方图。

或者写个脚本，在几个不同的训练实例 (training instances) 中，算出每个张量的**范数**。

如果变量**没在训练**，请看下节，“变量没在训练怎么办？”。

**4.****调整初始学习率**，实施适当的学习率计划。

如果损失**越来越大**，可能是初始学习率**太大**；如果损失**几乎不变**，可能是初始学习率**太小**。

![img](https://mmbiz.qpic.cn/mmbiz_gif/YicUhk5aAGtC96DwSbWcbYNjNcF8rrSMo3ia0uZMlKOvQV0KDy8zd0lmgmTzTeyM7SS5VBtRhAAhZrfGJcwkTV3w/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

不管怎样，一旦找到好用的初始学习率，就要进行**学习率衰减**计划。

像AMA这样的优化器，**内部**就有学习率衰减机制。但它可能衰减得不够激烈，还是自己做一个比较好。

**5****.确认没有过拟合**。做个**学习率 vs 训练步数**的曲线，如果像是**抛物线**，可能就过拟合了。解决方法参见下文“过拟合怎么办？”章节

# 变量没在训练怎么办？

像上文说的，看了**张量板**直方图，或者写了脚本来算每个张量的**范数**之后，把**范数是常数**的那些张量拎出来。

![img](https://mmbiz.qpic.cn/mmbiz_gif/YicUhk5aAGtC96DwSbWcbYNjNcF8rrSMoEIhvFChprG9jFiam0icibaicuA0pvNUvxBUkEWanxKdDJLL1h1PrYx8NsA/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

如果有个变量不训练：

**1.确认TF把它看做可训练 (trainable) 的变量**。详情可以查看**TF GraphKeys**：t.cn/R18Do6Y。

**2.****确认没发生梯度消失**。

如果**下游变量** (更靠近output的变量) 训练**正常**，而**上游变量**不训练，大概就是梯度消失了。

解决方案见下文，“梯度消失/梯度爆炸”章节。

**3.****确认ReLU (线性整流函数) 还在放电**。

如果**大部分**神经元电压都保持在**零**了，可能就要改变一下**权重初始化**策略了：尝试用一个**不那么激烈**的学习率衰减，并且减少**权重衰减正则化**。

# 梯度消失/梯度爆炸

**1.****考虑用个好点的权重初始化策略**。尤其是在，训练之初梯度就不怎么更新的情况下，这一步尤为重要。

**2.****考虑换一下激活函数**。比如**ReLU**，就可以拿**Leaky ReLU**或者**MaxOut**激活函数来代替。

**3.****如果是RNN (递归神经网络) 的话，就可以用LSTM block**。详情可参照此文：t.cn/RI6Qe7t

![img](https://mmbiz.qpic.cn/mmbiz_gif/YicUhk5aAGtC96DwSbWcbYNjNcF8rrSMoFwMgLy4o2rfmibTZ8WLbI3icN6LaX7HLoXBbJfk7KgTywzGRAMO03ooA/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

# 过拟合怎么办？

过拟合，就是神经网络**记住**训练数据了。如果网络在**训练集**和**验证集**上，准确度差别很大，可能它就过拟合了。详情可见 (*Train/Val accuracy*) ：t.cn/RAkUzJP。

**1.**做个**数据扩增**。可上翻至本文第一节。

**2.**做个**Dropout**。在训练的每一步，都抛弃一些神经元。详情请见：t.cn/RkZodZo。

**3.****增加****正则化**。

**4.**做个**批量归一化**。详情请见：t.cn/RNunyfR。

**5.**做个**早停止 (early stopping)** 。因为，过拟合可能是训练了**太多Epoch**造成的。详情可见：t.cn/RkZKjEQ。

**6.**还不行的话，就用个**小一点的网络**吧。不过，没到万不得已，还是别这样。

![img](https://mmbiz.qpic.cn/mmbiz_gif/YicUhk5aAGtC96DwSbWcbYNjNcF8rrSMoicxnThBbrAPuYqiaLuEubYMvjvuRJ752GINiaZMLrJaZ7Xp4bT5YJt4Kg/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

# 还能调些什么？

**1.****考虑用个带权重的损失函数**。

比如，在图像的**语义分割**中，神经网络要给每一个像素归类。其中一些类别，可能很少有像素属于它。

如果，给这些**不常被光顾**的类别，加个权重，mean_iou这项指标就会好一些。

**2.**改变**网络架构**。之前的网络，可能太深，可能太浅了。

**3.**考虑**把几个模型集成起来用**。

**4.**用**跨步卷积 (strided convolution)** 来代替最大池化/平均池化。

**5.**做个完整的**超参数搜索**。

**6.**改变**随机种子 (random seeds)** 。

**7.**上面的步骤全都不管用的话，还是再去**多找点数据**吧。

![img](https://mmbiz.qpic.cn/mmbiz_jpg/YicUhk5aAGtC96DwSbWcbYNjNcF8rrSMoricL2HrNBISBukQ1rgXiaLpGmRnZGgC8UDibz0xvF5Jt8AF10v25eQX5w/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

最后，祝大家的CNN都能吃喝不愁，健康成长。

原文链接：

https://gist.github.com/zeyademam/0f60821a0d36ea44eef496633b4430fc#before-troubleshooting




# 相关

- [CNN手把手维修攻略：你的网络不好好训练，需要全面体检](https://mp.weixin.qq.com/s?__biz=MzIzNjc1NzUzMw==&mid=2247502664&idx=4&sn=90216832c1995df9347a0eddc4a4312c&chksm=e8d07c3adfa7f52c6fb7ad79150e9eca89b06098a5dd9fbf3ba466435591060ea8f13e115ef6&mpshare=1&scene=1&srcid=0817jgx6G53uK6rnlnZrt5nV#rd)
