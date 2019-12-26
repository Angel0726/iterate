---
title: NCRF 百度利用神经条件随机场检测癌症转移
toc: true
date: 2019-11-17
---
# NCRF： 百度利用神经条件随机场检测癌症转移


百度研究人员提出一种神经条件随机场（neural conditional random field，NCRF）深度学习框架，来检测 WSI 中的癌细胞转移，在提升肿瘤图像准确率的同时也减少了假阳性的出现几率。



NCRF 通过一个直接位于 CNN 特征提取器上方的全连接 CRF，来考虑相邻图像块之间的空间关联。整个深度网络可以使用标准反向传播算法，以最小算力进行端到端的训练。CNN 特征提取器也可以从利用 CRF 考虑空间关联中受益。与不考虑空间关联的基线方法相比，NCRF 框架可获取更高视觉质量的图像块预测概率图。



![img](https://mmbiz.qpic.cn/mmbiz_png/ldSjzkNDxlnyABkicKXelU1B4YCibdWJwA8bwRgIHAjgQnKKtl3ibSTrzUGjiaE3CnFLKcSgOnP5RqibJa1JafTPI0w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



项目地址：

https://github.com/baidu-research/NCRF
