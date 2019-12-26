---
title: Darts
toc: true
date: 2019-11-17
---
# Darts



﻿

![img](https://mmbiz.qpic.cn/mmbiz_png/ptp8P184xjwmxLdIhgIpa58pOiaGPwnBvMO3WiaHqFFabiaEguQ3MQDib3ibicy8UK1fdlVmRCibJ4O6zUT1lrhdic2WnA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



Darts 是 CMU 联合DeepMind 团队研发的一种可微分的卷积循环网络结构，它能够基于结构表征的连续性，通过梯度下降法来更有效地进行结构搜索。在CIFAR-10，ImageNet，Penn Treebank 和WikiText-2 等大型数据库的实验验证了这种结构在卷积图像分类和循环语言建模方面的高效性能。



> 论文链接：
>
> https://arxiv.org/pdf/1806.09055.pdf
>
> Github 链接：
>
> https://github.com/quark0/darts




卡耐基梅隆大学（CMU）在读博士刘寒骁、DeepMind 研究员 Karen Simonyan 以及 CMU 教授杨一鸣提出的「可微架构搜索」DARTS(Differentiable Architecture Search)方法基于连续搜索空间的梯度下降，可让计算机更高效地搜索神经网络架构。



据论文所述，DARTS在发现高性能的图像分类卷积架构和语言建模循环架构中皆表现优异，而且速度比之前最优的不可微方法快了几个数量级，所用 GPU 算力有时甚至仅为此前搜索方法的 700 分之 1，这意味着单块 GPU 也可以完成任务。



![img](https://mmbiz.qpic.cn/mmbiz_png/KfLGF0ibu6cJIePQXz322mD60pK0huE9Ha18H1E8mwxReTfssK4NLh982d6L8pjRJJ47JaKGWW60AsIBC6Zt6xQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



论文链接：

https://arxiv.org/abs/1806.09055

项目地址：

https://github.com/quark0/darts
