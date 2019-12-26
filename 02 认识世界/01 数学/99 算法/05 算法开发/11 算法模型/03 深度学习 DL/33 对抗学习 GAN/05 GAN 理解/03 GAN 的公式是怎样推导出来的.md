---
title: 03 GAN 的公式是怎样推导出来的
toc: true
date: 2019-11-17
---
# GAN 的公式是如何推导出来的？


昨天，谷歌大脑研究科学家、生成对抗网络GAN的提出者Ian Goodfellow在Twitter推荐了他最喜欢的两个机器学习的Theory Hacks，利用这两个技巧，他在著名的GAN论文中推导了公式。



**GAN论文****地址：**https://papers.nips.cc/paper/5423-generative-adversarial-nets.pdf

![img](https://mmbiz.qpic.cn/mmbiz_png/UicQ7HgWiaUb2Zl8hzDTU1arclFr63TXIyibgNITktgAZLic6SS9ic3icCHL77ENnIiaq7F1XicqtYFiciceBYa0EnNmrE4w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



昨天，谷歌大脑研究科学家、《深度学习》的作者之一Ian Goodfellow在Twitter推荐了他最喜欢的两个机器学习“黑魔法”（Theory Hack）。Ian Goodfellow还是生成对抗网络GAN的提出者，利用这两个技巧，他在著名的GAN论文中推导了一个公式。





![img](https://mmbiz.qpic.cn/mmbiz_png/UicQ7HgWiaUb0XwQlLrJUk2J6AbHE3zlcwN0ZMaUzcICibibewF0X02wLtNMsrV9n9XspWhsNzT1zkyGv3xH2viaJKA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



很多时候，我们想用**代数/微积分**来分析神经网络的最优行为。神经网络模型通常非常复杂，用代数方法来实现权重衰减或许可行，但想用代数方法来解决神经网络中大多数函数的参数优化问题就会太过复杂。



为了得到一个不那么复杂的模型，一个常见的直觉方法是使用**线性模型**。线性模型很好，因为它能很好的解决凸优化问题。但线性模型也有缺点：它过于简单，很多神经网络能做的事情线性模型不能做。这样，解决方法就简化了。



**Theory Hack＃1：****将神经网络建模为一个任意函数**（因此可以优化所有函数f的空间，而不是特定神经网络架构的参数theta）。与使用参数和特定的架构相比，这种方法非常简洁。



将神经网络视为一个函数，保留了线性模型的主要优点：多种凸函数问题。例如，分类器的交叉熵损失在函数空间中是凸的。



这个假设不是太准确，特别是与线性模型假设相比。但根据万能逼近定理（universal approximator theorem），神经网络可以较好地近似任意函数。



**Theory Hack＃2：**如果你在同一空间优化所有函数时遇到困难，可以将函数想象成一个包含很多项（entries）的向量。评估函数f(x)，其中x在R ^ n中，设想成在一个向量中查找f_x，其中x是一个整数索引。



有了Theory Hack＃2，现在对函数进行优化就变成了一个常规的微积分问题。这种方法很直观，但不是100％准确。有关更多正式版本和关于何时可以使用的限制信息，请参阅《深度学习》书的19.4.2部分：http://www.deeplearningbook.org/contents/inference.html



利用这两个 theory hack，我和共同作者推导了GAN论文（Generative Adversarial Nets）中的第2个公式：https://papers.nips.cc/paper/5423-generative-adversarial-nets.pdf ...





![img](https://mmbiz.qpic.cn/mmbiz_png/UicQ7HgWiaUb0XwQlLrJUk2J6AbHE3zlcwibRQ3PszIMl00Zzet7hrh4r2DyUU12pVicaXHg3OVFxDgcIMQwJG0Wsw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



最后，*Convex Optimization* 这本书的3.2节有更多这样的theory hacks

PDF版电子书地址：https://web.stanford.edu/~boyd/cvxbook/bv_cvxbook.pdf


# 相关

- [Ian Goodfellow：生成对抗网络 GAN 的公式是怎样推导出来的](https://mp.weixin.qq.com/s?__biz=MzI3MTA0MTk1MA==&mid=2652018813&idx=3&sn=4b23c96d1af6ff76850ca3cf96b76872&chksm=f121ec8cc656659a64c0ffcae60051b17250d7addda0f2cb0b26768966e2bb5e411968a65091&mpshare=1&scene=1&srcid=0517cau5jHt990PVHGfmHaUS#rd)
