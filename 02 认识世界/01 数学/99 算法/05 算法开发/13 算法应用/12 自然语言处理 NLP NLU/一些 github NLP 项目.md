---
title: 一些 github NLP 项目
toc: true
date: 2019-11-17
---
# Github 上的一些 PyTorch NLP 代码和模型



## AllenNLP

#### **https://github.com/allenai/allennlp**

![img](https://mmbiz.qpic.cn/mmbiz_png/AefvpgiaIPw1DML1aicDblaKOQSWk4Acx9oGxNt69tOuGkq7uyAIVQA2xFE9kxHV0XWBftOiaoMu8hmBJNmzo4Cdw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

AllenNLP，是AI2公司家的开源项目，致力于成为 PyTorch 下 NLP 算法研究和实现的全能平台。**AllenNLP 的设计理念是: 模块化和轻量级**。它将 NLP中个各种需求进行了非常好的封装，包括：padding, masking 等等。特别的，**AllenNLP 对实验非常友好**，实验流程和参数有Json文件配置，并行、重现完全不是问题，而且每一步都有丰富的 log 记录你想记录的一切。

AllenNLP 自身实现了包括：**命名实体识别、语义角色标注、阅读理解**在内的多种常用算法。AllenNLP是由艾伦人工智能研究所(Allen Institute for Artificial Intelligence)与华盛顿大学(University of Washington)和其他机构的研究人员密切合作建立和维护的。



DrQA 2374 Star



#### https://github.com/facebookresearch/DrQA

![img](https://mmbiz.qpic.cn/mmbiz_png/AefvpgiaIPw1DML1aicDblaKOQSWk4Acx9FoW8icDOOsR1myL2ic2XMWiaEn8ic5YibHjGMpjVT5Iw0TBvonxhtBibxsmA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



DrQA 是 facebook 开源的开放式阅读理解智能问答算法。原论文发布在 ACL2016上， 名字是：Reading Wikipedia to Answer Open-Domain Questions



链接：

http://www.zhuanzhi.ai/paper/ec2483da392a7e054eeb0f0a58d3ddee。



DrQA是一个用于阅读理解的系统，适用于开放领域的问答。特别是，**DrQA致力于解决大规模机器阅读理解**。在这种情况下，算法在一个非常大的非结构化文档语料库中寻找问题的答案。因此，系统必须将文档检索(查找相关文档)与文本的机器理解(识别来自这些文档的答案)结合起来。



faieseq 1711 Star



#### https://github.com/pytorch/fairseq

![img](https://mmbiz.qpic.cn/mmbiz_png/AefvpgiaIPw1DML1aicDblaKOQSWk4Acx9XK6VIEBCJibqf46Mujufp6gTkTxMKyrUiaezfOlOkAUibYOHZy0qRibZOA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

fairseq是facebook 开源的一个序列建模工具包，而并不是单纯的某个模型的实现。



它**允许研究人员和开发人员为机器翻译、自动摘要、语言模型和其他文本生成任务训练自定义模型**。它提供了各种Seq2seq 的模型的实现，包括:

- Convolutional Neural Networks (CNN)

- - Dauphin et al. (2017): Language Modeling with Gated Convolutional Networks
  - Gehring et al. (2017): Convolutional Sequence to Sequence Learning
  - **New** Edunov et al. (2018): Classical Structured Prediction Losses for Sequence to Sequence Learning
  - **New** Fan et al. (2018): Hierarchical Neural Story Generation

- Long Short-Term Memory (LSTM) networks

- - Luong et al. (2015): Effective Approaches to Attention-based Neural Machine Translation
  - Wiseman and Rush (2016): Sequence-to-Sequence Learning as Beam-Search Optimization

- Transformer (self-attention) networks

- - Vaswani et al. (2017): Attention Is All You Need
  - **New** Ott et al. (2018): Scaling Neural Machine Translation



OpenNMT-py 1558 Star



#### https://github.com/OpenNMT/OpenNMT-py



![img](https://mmbiz.qpic.cn/mmbiz_png/AefvpgiaIPw1DML1aicDblaKOQSWk4Acx9W9a900q1bkjwpzCQOagFHqu4QYBNcXoly4ibedg6aL6O6JRTxtUfLXw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



OpenNMT 全称是Open Source Neural Machine Translation in PyTorch (PyTorch 开源神经翻译模型)， 致力于**研究促进新idea 在神经翻译，自动摘要，看图说话，语言形态学和许多其他领域的发展**。



作为自动翻译的平台型项目， OpenNMT 当然也支持各种文本数据预处理，包括**各种 RNN 单元，各种 attention机制，花式日志，语音转文本，看图说话**等等。



DeepNLP-models-Pytorch 1256 Star



#### https://github.com/DSKSD/DeepNLP-models-Pytorch

####

#### 最后，给大家介绍的是DeepNLP-models in PyTorch。这个库，是韩国的Kim Sungdong同学，在看完 CS224的课后，用 PyTorch 将其中的模型都实现了一遍， 包括：



![img](https://mmbiz.qpic.cn/mmbiz_png/AefvpgiaIPw1DML1aicDblaKOQSWk4Acx9v1ECn7WnYcdibL1xUfoXpZTjJiaVXKwicb5zF6TE6rv1LQhGYHr1oJicLg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



# 相关

- [GitHub获赞过千：PyTorch 自然语言处理项目Top 5](https://mp.weixin.qq.com/s?__biz=MzI3MTA0MTk1MA==&mid=2652021834&idx=5&sn=22eb378cd45f4d1bbe2af53135237086&chksm=f121d0bbc65659ad9cb4f699935c3cf36dd10f41f622af4113f0b344d346743a1ad8314fae29&mpshare=1&scene=1&srcid=0710wwMEKM7i2RCJQjUbhLPE#rd)
