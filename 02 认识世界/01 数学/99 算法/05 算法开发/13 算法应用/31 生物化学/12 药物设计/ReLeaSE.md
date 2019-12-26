---
title: ReLeaSE
toc: true
date: 2019-11-17
---
# 从零开始自学设计新型药物 UNC提出结构进化强化学习


> 搜索关键词「AI、诊断」，微信上出现一大堆关于 AI 医疗的文章，从失明到肺病再到癌症，AI 似乎无所不能。前不久，来自北卡罗来纳大学埃谢尔曼药学院的一个团队创造了一种人工智能方法 ReLeaSE，能够从零开始自学设计新型药物分子。近日，该研究已被发表在 Science Advances 上。



![img](https://mmbiz.qpic.cn/mmbiz_jpg/KmXPKA19gW8cCllS16e1FuZHMq3Sriam0JqPeydZgI5jzuXVLsuFYNJCfPwEV5jFT5ZYawTEiaLdN2FEF5JyJPJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*生成具备期望属性的新型化合物 SMILES 字符串的深度强化学习算法工作流程。(A) 生成 Stack-RNN 的训练步。(B) 生成 Stack-RNN 的生成器步骤。在训练过程中，输入 token 是一个当前处理的简化分子线性输入系统（SMILES）字符串（来自训练集）中的一个字符。该模型根据前缀（prefix）输出下一个字符的概率向量 pΘ(a_t|s_t − 1)。参数 Θ 的向量通过交叉熵损失函数最小化进行优化。在生成器步骤中，输入 token 是前一步生成的字符。然后从分布 pΘ(a_t| s_t − 1) 中随机采样字符 a_t。(C) 生成新型化合物的强化学习系统的一般流程。(D) 预测模型机制。该模型将 SMILES 字符串作为输入，然后提供一个实数（即估计属性值）作为输出。该模型的参数使用 l2 平方损失函数最小化进行训练。Credit: Science Advances (2018). DOI: 10.1126/sciadv.aap7885*



北卡罗来纳大学埃谢尔曼药学院（UNC Eshelman School of Pharmacy）创造的人工智能方法能够从零开始自学设计新型药物分子，这有望大幅加快新型药物的研发速度。



该系统名为「结构进化强化学习」（Reinforcement Learning for Structural Evolution），又称 ReLeaSE。ReLeaSE 既是一种算法，也是一种计算机程序，它将两种神经网络合二为一，二者可被分别视为老师和学生。老师了解大约 170 万种已知生物活性分子化学结构词汇背后的句法和语言规则。通过与老师合作，学生逐渐学习并提高自己的能力，创造有望作为新药使用的分子。



ReLeaSE 的创造者 Alexander Tropsha、Olexandr Isayev 和 Mariya Popova 均来自 UNC 埃谢尔曼药学院。UNC 已经为该技术申请了专利，该团队上周在 Science Advances 上发表了一份概念验证性研究。



「这一过程可以借鉴语言学习过程来描述：学生掌握分子字母表及语言规则之后，他们就能自己创造新『词』（也就是新分子）。」Tropsha 说，「如果新分子实用且达到预期效果，老师就会批准。反之，老师就会否决，强制学生避开糟糕的分子并去创造有用的分子。」



ReLeaSE 是一种强大的药物虚拟筛选工具，这种计算方法已经被制药业广泛用于确定可用的候选药物。虚拟筛选让科学家可以评估现有的大型化学库，但该方法只对已知的化学物质有效。而 ReLeaSE 具备独特的能力，可以创建和评估新型分子。



「使用虚拟筛选的科学家就像餐馆中点菜的顾客那样，能点的菜通常仅限于菜单上有的。」Isayev 说道，「我们想为科学家提供一个『杂货店』和『个人厨师』，做出任何他们想要的菜式。」



该团队利用 ReLeaSE 生成具有他们指定特性（如生物活性和安全性）的分子，还可以使用该方法设计具有定制物理特性（如熔点、水溶性）的分子，以及设计对白血病相关酶具有抑制活性的新型化合物。



Tropsha 称：「对于一个需要不断寻找新方法来缩短新药进入临床试验所需时间的行业来说，该算法极具吸引力，因为它能设计出具有特定生物活性和最佳安全性的新化学实体。」



**论文：Deep reinforcement learning for de novo drug design**



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW93s9Y6Ljqia0EicbrXtEibh99NbXTpN74rlVjfDGbEk3nnYNFyicJ7Qs7DtO51FxACeZQ2ZlxDSZv27g/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



论文链接：http://advances.sciencemag.org/content/4/7/eaap7885/tab-pdf



摘要：我们设计并实现了一种新的计算策略，用于从零开始设计具有期望属性的分子，称为ReLeaSE（Reinforcement Learning for Structural Evolution，结构进化强化学习）。基于深度学习和强化学习方法，ReLeaSE集成了两个深度神经网络——生成和预测神经网络，这两个神经网络被单独训练，但都用于生成新的目标化学库。ReLeaSE仅使用简化分子线性输入系统（SMILES）字符串来表示分子。生成模型通过堆栈增强的记忆网络来训练，以产生化学上可行的SMILES字符串，预测模型则用来预测新生成化合物的期望属性。在该方法的第一阶段，使用监督学习算法分别训练生成模型和预测模型。在第二阶段，两种模型使用RL方法一起训练，以偏向于产生具有所需物理和/或生物特性的新化学结构。在该概念验证研究中，我们使用ReLeaSE方法设计化学库，该化学库偏向于结构复杂性，偏向于具有最大、最小或特定物理属性范围的化合物，如熔点或疏水性，或者偏向于对Janus蛋白激酶2具有抑制活性的化合物。本文提出的方法可用于找到产生对单一或多个期望属性进行优化了的新化合物的目标化学库。*![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8Zfpicd40EribGuaFicDBCRH6IOu1Rnc4T3W3J1wE0j6kQ6GorRSgicib0fmNrj3yzlokup2jia9Z0YVeA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)*



*原文链接：https://phys.org/news/2018-07-artificial-intelligence-drugs.html*


# 相关

- [学界 | 从零开始自学设计新型药物，UNC提出结构进化强化学习](https://mp.weixin.qq.com/s?__biz=MzA3MzI4MjgzMw==&mid=2650746146&idx=4&sn=dbcdcfa9c5c02f5f9ff904377607febe&chksm=871ae95cb06d604ae9072897916cd73aacc33adb8e27ef240455f40b5aca85cb20b8c2229952&mpshare=1&scene=1&srcid=080239fyX6UUDcZFAWssPjl0#rd)
