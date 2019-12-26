---
title: KDD 2018 首个面向Facebook arXiv网络图类的对抗攻击
toc: true
date: 2019-11-17
---

# KDD 2018 首个面向Facebook arXiv网络图类的对抗攻击

> 8 月 19 日至 23 日，数据挖掘顶会 KDD 2018 在英国伦敦举行，昨日大会公布了最佳论文等奖项。最佳论文来自慕尼黑工业大学的研究者，他们提出了针对图深度学习模型的对抗攻击方法，是首个在属性图上的对抗攻击研究。研究者还提出了一种利用增量计算的高效算法 Nettack。此外，实验证明该攻击方法是可以迁移的。



图数据是很多高影响力应用的核心，比如社交和评级网络分析（Facebook、Amazon）、基因相互作用网络（BioGRID），以及互连文档集合（PubMed、Arxiv）。基于图数据的一个最常应用任务是节点分类：给出一个大的（属性）图和一些节点的类别标签，来预测其余节点的类别标签。例如，你可能想对生物相互作用图（biological interaction graph）中的蛋白质进行分类、预测电子商务网络中用户的类型 [13]，或者把引文网络中的科研论文按主题分类 [20]。



尽管过去已经出现很多解决节点分类问题的经典方法 [8, 22]，但是近年来人们对基于图的深度学习方法产生了极大兴趣 [5, 7, 26]。具体来说，图卷积网络 [20, 29] 方法在很多图学习任务（包括节点分类）上达到了优秀性能。



这些方法的能力超出了其非线性、层级本质，依赖于利用图关系信息来执行分类任务：它们不仅仅独立地考虑实例（节点及其特征），还利用实例之间的关系（边缘）。换言之，实例不是被分别处理的，这些方法处理的是某种形式的非独立同分布（i.i.d.）数据，在处理过程中利用所谓的网络效应（如同质性（homophily）[22]）来支持分类。



但是，这些方法存在一个大问题：人们都知道用于分类学习任务的深度学习架构很容易被欺骗／攻击 [15, 31]。即使是添加轻微扰动因素的实例（即对抗扰动／样本）也可能导致结果不直观、不可信，也给想要利用这些缺陷的攻击者开了方便之门。目前基于图的深度学习方法的对抗扰动问题并未得到解决。这非常重要，尤其是对于使用基于图的学习的领域（如 web），对抗非常常见，虚假数据很容易侵入：比如垃圾邮件制造者向社交网络添加错误的信息；犯罪分子频繁操控在线评论和产品网站 [19]。



该论文试图解决这一问题，作者研究了此类操控是否可能。用于属性图的深度学习模型真的很容易被欺骗吗？其结果可信程度如何？



答案难以预料：一方面，关系效应（relational effect）可能改善鲁棒性，因为预测并未基于单独的实例，而是联合地基于不同的实例。另一方面，信息传播可能带来级联效应（cascading effect），即操纵一个实例会影响到其他实例。与现有的对抗攻击研究相比，本论文在很多方面都大不相同。



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW9AeVpb6o9ibfGptoUa0m2tMHj9NZGjMH0Ooicah5JqbiaKadbJQ9ibRicOoxtr70Z9fcWb7a4O3CO4r4Q/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*图 1：对图结构和节点特征的极小扰动导致目标误分类。*



该论文提出一个对属性图进行对抗扰动的原则，旨在欺骗当前最优的图深度学习模型。具体来说，该研究主要针对基于图卷积网络（如 GCN [20] 和 Column Network（CLN）[29]）的半监督分类模型，但提出的方法也有可能适用于无监督模型 DeepWalk [28]。研究者默认假设攻击者具备全部数据的知识，但只能操纵其中的一部分。该假设确保最糟糕情况下的可靠脆弱性分析。但是，即使仅了解部分数据，实验证明本研究中的攻击仍然有效。该论文的贡献如下：



- 模型：该研究针对节点分类提出一个基于属性图的对抗攻击模型，引入了新的攻击类型，可明确区分攻击者和目标节点。这些攻击可以操纵图结构和节点特征，同时通过保持重要的数据特征（如度分布、特征共现）来确保改变不被发现。
- 算法：该研究开发了一种高效算法 Nettack，基于线性化思路计算这些攻击。该方法实现了增量计算，并利用图的稀疏性进行快速执行。
- 实验：实验证明该研究提出的模型仅对图进行稍微改动，即可恶化目标节点的分类结果。研究者进一步证明这些结果可迁移至其他模型、不同数据集，甚至在仅可以观察到部分数据时仍然有效。整体而言，这强调了应对图数据攻击的必要性。



**论文：Adversarial Attacks on Neural Networks for Graph Data**



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW9AeVpb6o9ibfGptoUa0m2tMPeIEYzicITLxDLxPN7wRleS9k091fagKK6lw4xeIiaasjvIrpDFHTRnQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



论文链接：https://arxiv.org/pdf/1805.07984.pdf



摘要：应用到图的深度学习模型已经在节点分类任务上实现了强大的性能。尽管此类模型数量激增，但目前仍未有研究涉及它们在对抗攻击下的鲁棒性。而在它们可能被应用的领域（例如网页），对抗攻击是很常见的。图深度学习模型会轻易地被欺骗吗？在这篇论文中，我们介绍了首个在属性图上的对抗攻击研究，具体而言，我们聚焦于图卷积模型。除了测试时的攻击以外，我们还解决了更具挑战性的投毒/诱发型（poisoning/causative）攻击，其中我们聚焦于机器学习模型的训练阶段。



我们生成了针对节点特征和图结构的对抗扰动，因此考虑了实例之间的依赖关系。此外，我们通过保留重要的数据特征来确保扰动不易被察觉。为了应对潜在的离散领域，我们提出了一种利用增量计算的高效算法 Nettack。我们的实验研究表明即使仅添加了很少的扰动，节点分类的准确率也会显著下降。另外，我们的攻击方法是可迁移的：学习到的攻击可以泛化到其它当前最佳的节点分类模型和无监督方法上，并且类似地，即使仅给定了关于图的有限知识，该方法也能成功实现攻击。



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW9AeVpb6o9ibfGptoUa0m2tMia1ia9jfT7YF4LwMn07LlMncOicotlicwX3VZ9ZYazWOBhQHhs8yp2F1NQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*图 2：随着扰动数量的增长，平均代理损失（surrogate loss）的变化曲线。由我们模型的不同变体在 Cora 数据集上得到，数值越大越好。*



图 3 展示了在有或没有我们的约束下，得到的图的检验统计量 Λ。如图可知，我们强加的约束会对攻击产生影响；假如没有强加约束，损坏的图的幂律分布将变得和原始图更加不相似。类似地，表 2 展示了特征扰动的结果。



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW9AeVpb6o9ibfGptoUa0m2tMaicqepL5yvhcR5YtITSicJsIy409hCCz2KIvCpxsxtlmlvUcgFAiccB7w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*图 3（左）：检验统计量 Λ 的变化（度分布）。图 4（右）梯度 vs. 实际损失。*



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW9AeVpb6o9ibfGptoUa0m2tMmSvibejicwO0oakBicgOVE54Prn6icl074E6LibMDpYy0FnianNygHkyJHBQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*表 2：Cora 上每个类别中的特征扰动 top-10。*



图 6a 评估了两个攻击类型的 Nettack 性能：逃逸攻击（evasion attack），基于原始图的模型参数（这里用的是 GCN [20]）保持不变；投毒攻击（poisoning attack），模型在攻击之后进行重新训练（平均 10 次运行）。



图 6b 和 6c 显示，Nettack 产生的性能恶化效果可迁移至不同（半监督）图卷积方法：GCN [20] and CLN [29]。最明显的是，即使是无监督模型 DeepWalk [28] 也受到我们的扰动的极大影响（图 6d）。



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW9AeVpb6o9ibfGptoUa0m2tMF6oPpOkibyjk7RCx0OLcXs6EQnqpcq0hGkZNibtsib694yLBRclicY6CDw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*图 6：使用不同攻击算法在 Cora 数据上的结果。Clean 表示原始数据。分值越低表示结果越好。*



图 7 分析了攻击仅具备有限知识时的结果：给出目标节点 v_0，我们仅为模型提供相比 Cora 图其尺寸更大的图的子图。



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW9AeVpb6o9ibfGptoUa0m2tMIlzpZ4C74u5wLQAtRricTRE1GmzdCQibhVRF0Ed7CamNlIwNN6jL0sTw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*图 7：具备有限数据知识的攻击。*



表 3 总结了该方法在不同数据集和分类模型上的结果。这里，我们报告了被正确分类的部分目标节点。我们对代理模型（surrogate model）的对抗扰动可在我们评估的这些数据集上迁移至这三种模型。毫不奇怪，influencer 攻击比直接攻击导致的性能下降更加明显。



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW9AeVpb6o9ibfGptoUa0m2tMsq4IAzZg7hicaKPBQyaLIa6iao7fVJnTN5bEfFPvNkScIv9jEIAsRjCw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*表 3：结果一览。数值越小表示结果越好。![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8Zfpicd40EribGuaFicDBCRH6IOu1Rnc4T3W3J1wE0j6kQ6GorRSgicib0fmNrj3yzlokup2jia9Z0YVeA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)*


# 相关

- [KDD 2018 | 最佳论文：首个面向Facebook、arXiv网络图类的对抗攻击研究](https://mp.weixin.qq.com/s?__biz=MzA3MzI4MjgzMw==&mid=2650747343&idx=3&sn=576d9ffc81d25b714cfd5e15192db8bc&chksm=871af5b1b06d7ca7a49c3a1f346baa83fa498524aed6075223a2d31b62b29e694803613158c9&mpshare=1&scene=1&srcid=0822RO4ZBwlJol02aBD6iYQs#rd)
