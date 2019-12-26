---
title: 04 LSTM 的变体
toc: true
date: 2019-06-16
---
# 可以补充进来的

- 这么多年过去了，现在应该有很多各种变体了。

# LSTM 的变体


## GRU

Gated Recurrent Unit，也称为 GRU，由 Cho 等人（2014）提出，是 LSTM 的变体。遗忘门和输入门结合作为“更新门”（update gate），同时还做了其他的一些改变。

与 RNN 不同的是，序列中不同的位置的单词对当前隐藏层的状态的影响不同，越靠前面的影响越小，即每个前面状态对当前的影响进行了距离加权，距离越远，权值越小。<span style="color:red;">嗯，这样挺好。</span>另外，在产生误差 error 时，误差可能是由某一个或者几个单词引发的，所以应当仅仅对对应的单词 weight 进行更新。

GRU 的结构如图：


<center>

![](http://images.iterate.site/blog/image/20190616/Mp0GAYolBRXg.png?imageslim){ width=55% }

</center>

GRU 首先根据当前输入单词向量 word vector 在前一个隐藏层的状态中计算出 update gate 和 reset gate。再根据 reset gate、当前 word vector 以及前一个隐藏层计算新的记忆单元内容（New Memory Content）。当 reset gate 为 1 的时候，前一个隐藏层计算新的记忆单元内容忽略之前的所有记忆单元内容，最终的记忆是之前的隐藏层与新的记忆单元内容的结合。<span style="color:red;">嗯。</span>


$$
z_{t}=\sigma\left(W^{(z)} x_{t}+U^{(z)} h_{t-1}\right)
$$

$$
r_{t}=\sigma\left(W^{(r)} x_{t}+U^{(r)} h_{t-1}\right)
$$


$$
\tilde{h}_{t}=\tanh \left(W x_{t}+r_{t} U h_{t-1}\right)
$$


$$
h_{t}=z_{t} h_{t-1}+\left(1-z_{t}\right) \tilde{h}_{t}
$$


## CW-RNN

CW-RNN 是较新的一种 RNN 模型，其论文发表于 2014 年 Beijing ICML。是一种使用时钟频率来驱动的 RNN。<span style="color:red;">哇塞，使用时钟频率来驱动的 RNN ，是怎么设计的？</span>

它将隐藏层分为几组，每一组按照自己规定的时钟频率对输入进行处理。并且为了降低标准 RNN 的复杂性，CW-RNN 减少了参数的数目，提高了网络性能，加快了网络的训练速度。CW-RNN通过不同的隐藏层模块工作，在不同的时钟频率下来解决长时间依赖问题。将时钟时间进行离散化，然后在不同的时间点，不同的隐藏层组中工作。因此，所有的隐藏层组不会在每一步都同时工作，这样便会加快网络的训练。并且，时钟周期短的组的神经元不会连接到时钟周期长的组的神经元上，只会让周期长的连接到周期短的上，周期长的速度慢，周期短的速度快，那么便是速度慢的连速度快的。<span style="color:red;"> 听着感觉很厉害，没有怎么理解。</span>

CW-RNN 包括输入层、隐藏层、输出层。输入层到隐藏层的连接，隐藏层到输出层的连接为前向连接，如图所示：

<span style="color:red;">想更多的知道这个</span>

<center>

![](http://images.iterate.site/blog/image/20190616/wQ7S2JKsJMVy.png?imageslim){ width=55% }

</center>






# 相关

- 《深度学习框架 Pytorch 快速开发与实战》
