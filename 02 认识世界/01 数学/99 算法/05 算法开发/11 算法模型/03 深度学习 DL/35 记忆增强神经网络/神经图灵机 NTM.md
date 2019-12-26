---
title: 神经图灵机 NTM
toc: true
date: 2019-11-17
---
# 神经图灵机 NTM

> 在篇篇论文都是 state-of-the-art 的年代，论文的可复现性与官方代码一直受到研究者的关注，我们希望知道实际的模型性能或改进方法到底有没有原论文写的那么好。最近都柏林圣三一学院的研究者通过一篇论文描述如何复现 14 年提出的神经图灵机，并同时公开了源代码。他们表示原论文没有提供源代码，且其它研究者复现的代码在速度和性能上都有缺陷，因此他们重新使用 TensorFlow 仔细复现了这篇论文。目前该论文已被 ICANN 2018 接收。



项目地址：https://github.com/MarkPKCollier/NeuralTuringMachine



神经图灵机（NTM）[4] 是几种新的神经网络架构 [4, 5, 11] 的一个实例，这些架构被分类为记忆增强神经网络（MANN）。MANN 的典型特征是存在外部记忆单元。这与门控循环神经网络（如长短期记忆单元（LSTM），其记忆是一个在时间维度上保持不变的内部向量）不同。LSTM 已在许多商业上重要的序列学习任务中取得了当前最优性能，如手写体识别 [2]、机器翻译 [12] 和语音识别 [3]。但是，已经证明了 MANN 在一些需要一个大型存储器和/或复杂的存储器访问模式的人工序列学习任务上优于 LSTM，如长序列记忆和图遍历 [4, 5, 6, 11]。



NTM 文章的原作者没有提供其实现的源码。NTM 的开源实现是存在的，但是其中一些实现报告显示，在训练期间，它们的实现梯度有时会变成 NaN，导致培训失败。然而其他开源代码会报告收敛缓慢或不报告其实现的学习速度。缺乏可靠的 NTM 开源实现使得从业者更难将 NTM 应用于新问题，使得研究者更难去改进 NTM 框架。



本文定义了一个成功的 NTM 实现，该实现学会完成三个基准的序列学习任务 [4]。作者指定了控制 NTM 实现的可选参数集合，并对其他开源的 NTM 实现中的许多记忆内容初始化方案进行了经验对比，发现如何选择 NTM 记忆内容初始化方案是能否成功实现 NTM 的关键。作者在另一个开源的 NTM 实现上建立了 Tensorflow 实现，但在得出实验结果之后，作者对记忆内容初始化、控制器头部参数计算和接口进行了重大改变，从而使其能更快地收敛，更可靠地优化，并且更容易与现有的 Tensorflow 方法集成。



这个存储库包含神经图灵机的一个稳定、成功的 Tensorflow 实现，已经在原论文的 Copy，Repeat Copy 和 Associative Recall 任务上进行了测试。



**应用**





```
from ntm import NTMCell

cell = NTMCell(num_controller_layers, num_controller_units, num_memory_locations, memory_size,
 num_read_heads, num_write_heads, shift_range=3, output_dim=num_bits_per_output_vector,
 clip_value=clip_controller_output_to_value)

outputs, _ = tf.nn.dynamic_rnn(
 cell=cell,
 inputs=inputs,
 time_major=False)
```



该实现源自另一个开源 NTM 实现 https://github.com/snowkylin/ntm。作者对链接的代码做了微小但有意义的更改，使得实现中的训练变得更加可靠，收敛更加快速，同时更容易与 Tensorflow 集成。该论文的贡献是：



- 作者比较了三种不同的记忆初始化方案并发现将神经图灵机的记忆内容初始化为小的常数值比随机初始化或通过记忆初始化的反向传播效果要好。
- 作者将 NTM 控制器的输出剪切到一个范围内，有助于解决优化的困难。
- NTMCell 实现了 Tensorflow RNNCell 接口（https://www.tensorflow.org/api_docs/python/tf/contrib/rnn/RNNCell），因此可以直接与 tf.nn.dynamic_rnn 等一起使用。
- 从未像其他一些实现一样，看到损失出现 NaN 的情况。
- 作者实现了 NTM 论文中 5 个任务中的 3 个。与 LSTM、DNC 和 3 个记忆内容初始化方案相比，作者进行了很多实验并报告了实现的收敛速度和泛化性能。



**论文：Implementing Neural Turing Machines**



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gWibCpOt1xVaP0l1YY7l0KCUaHoEBMyerF7XAQhpfic5AjcQ7Tj9vIzjPmric2vwbicx9iaJD1p1AMs4jqw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



论文地址：https://arxiv.org/abs/1807.08518



神经图灵机（NTM）是记忆增强神经网络的一个实例，是一类新的循环神经网络，通过引入外部记忆单元将计算从存储器中分离。NTM 在一些序列学习任务上的性能要优于长短期记忆单元。存在许多 NTM 的开源实现，但是它们在训练时不稳定，同时/或者无法重现 NTM 该有的性能。本文介绍了成功实现 NTM 的细节。本文的实现学习去解决 NTM 原文中的 3 个序列学习任务。作者发现记忆内容初始化方案的选择对于能否成功实现 NTM 至关重要。记忆内容初始化为小常数值的网络平均收敛速度是第二名记忆内容初始化方案的 2 倍。



**2 神经图灵机**



NTM 由一个控制器网络和一个外部记忆单元组成，控制器网络可以是一个前馈神经网络或一个循环神经网络，外部存储器单元是 N * W 的记忆矩阵，其中 N 表示记忆位置的数量，W 表示每个记忆单元的维度。无论控制器是否是循环神经网络，整个架构都是循环的，因为记忆矩阵的内容不随时间而变化。控制器具有访问记忆矩阵的读写头。在一个特定记忆单元上读或写的影响通过一个软注意力机制进行加权。这种寻址机制类似于神经机器翻译中使用的注意力机制 [1, 9]，不同之处在于，它将基于位置的寻址与这些注意力机制中基于内容的寻址相结合。



**5 结果**



5.1 记忆初始化方案对比



作者根据常数初始化方案初始化的 NTM 收敛到接近 0 误差，比学习的初始化方案快约 3.5 倍，而随机初始化方案无法在分配的时间内解决 Copy 任务（图 1）。



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gWibCpOt1xVaP0l1YY7l0KCUa4d8rQxKxdaiahia7IMjZlUXetHkgDfLtEP4ydOnWNKW4vdnOA8bOGQqQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*图 1：Copy 任务不同记忆初始化方案下，学习曲线的对比。对于根据常数、学习的和随机的初始化方案初始化的每一个 NTM 来说，误差是每训练 10 次后取中值。*



根据常数初始化方案初始化的 NTM 收敛到接近 0 的误差，比学习的初始化方案快约 1.15 倍，比随机初始化方案快 5.3 倍（图 3）。



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gWibCpOt1xVaP0l1YY7l0KCUaKFmrjI6GMDajj2ZXRvuibr59IUTQ9L7fIdNwaKPul1MibQkzcuHOAN2Q/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*图 3：Associative Recall 任务在不同记忆初始化方案下，学习曲线的对比。对于根据常数、学习的和随机的初始化方案初始化的每一个 NTM 来说，误差是每训练 10 次后取中值。*



5.2 架构比较



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gWibCpOt1xVaP0l1YY7l0KCUa0E2mhUIY8Nedq6w4m7p1EatQoyCoQzM7LiciaGWyddwSAKeIlO9P80HA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*图 4：Copy 任务采用不同架构时，学习曲线的对比。对于 DNC，NTM 和 LSTM 来说，误差是每训练 10 次后取中值。*



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gWibCpOt1xVaP0l1YY7l0KCUagicicApichbpu1P1RxYXNcSVMBWViasfajNL6HBK7JAU4pMJCwb25Qzic0w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*图 6：Associative Recall 任务采用不同架构时学习曲线的对比。对于 DNC、NTM 和 LSTM 来说，误差是每训练 10 次后取中值。*![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8Zfpicd40EribGuaFicDBCRH6IOu1Rnc4T3W3J1wE0j6kQ6GorRSgicib0fmNrj3yzlokup2jia9Z0YVeA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)


# 相关

- [学界 | 老论文没有源码？14年神经图灵机的复现被接收为大会论文](https://mp.weixin.qq.com/s?__biz=MzA3MzI4MjgzMw==&mid=2650747047&idx=4&sn=fad73f1facf78147fb5f904011433db9&chksm=871af4d9b06d7dcf61db41968ff0541343130a97dbb81680eda5145f2a1cd0c47f6ad4d57108&mpshare=1&scene=1&srcid=0815Pxft29CkZ1Fb18sTXV2S#rd)
