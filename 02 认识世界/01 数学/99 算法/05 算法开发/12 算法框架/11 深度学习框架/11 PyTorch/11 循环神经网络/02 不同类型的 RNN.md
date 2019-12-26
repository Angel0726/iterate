---
title: 02 不同类型的 RNN
toc: true
date: 2019-06-16
---
# 不同类型的 RNN

## Simple RNN（SRN）

SRN 是 RNN 的一种特例，它是一个三层网络，并且在隐藏层增加了上下文单元。上下文单元节点与隐藏层中的节点的连接是固定（谁与谁连接）的，并且权值也是固定的（值是多少）。在每一步中，使用标准的前向反馈进行传播，然后使用学习算法进行学习。

上下文每一个节点保存其连接的隐藏层节点的上一步的输出，即保存上文；并作用于当前步对应的隐藏层节点的状态，即隐藏层的输入由输入层的输出与上一步自己的状态所决定。因此 SRN 能够解决标准的多层感知机（MLP）无法解决对序列数据进行预测的任务。

<span style="color:red;">这是啥呀？看了这一段有点云里雾里。什么是上下文单元？怎么就固定连接了？什么样的固定方式？怎么更新权重的？怎么保存上一步的输出的？感觉啥都没讲。。</span>

## Bidirectional RNN

Bidirectional RNN 是双向 RNN，当前的输出和之前的序列元素，以及之后的序列元素都是有关系的。例如：预测一个语句中缺失的词语就需要根据上下文来进行预测。

Bidirectional RNN 是一个相对较简单的 RNN，是由两个 RNN 上下叠加在一起组成的。输出是由这两个 RNN 的隐藏层的状态决定的。

Bidirectional RNN模型如图所示。

<center>

![](http://images.iterate.site/blog/image/20190616/Rnmg03O3EORh.png?imageslim){ width=55% }

</center>

<span style="color:red;">感觉也是啥都没讲。。</span>

## 深层双向 RNN

深层双向 RNN 和双向 RNN 比较类似，区别只是每一步/每个时间点设定为多层结构，如图所示：


<center>

![](http://images.iterate.site/blog/image/20190616/C5S4yABsG8du.png?imageslim){ width=55% }

</center>

<span style="color:red;">没明白。</span>

## LSTM 神经网络

Long Short Term 网络叫作 LSTM，是一种 RNN 特殊的类型。LSTM 由 Hochreiter & Schmidhuber（1997）提出，并被 Alex Graves 进行了改良和推广。LSTM 精确解决了 RNN 的长短记忆问题。



在 LSTM 中，每个神经元是一个“记忆细胞”，细胞里面有一个 “输入门”（input gate），一个 “遗忘门”（forget gate），一个 “输出门”（output gate），俗称 “三重门”。与一般神经网络的神经元相比，LSTM 神经元多了一个遗忘门。


结构如图所示：

<center>

![](http://images.iterate.site/blog/image/20190616/SCQUWXg7Ds3t.png?imageslim){ width=55% }

</center>


它与一般的 RNN 结构在本质上并没有什么不同，只是使用了不同的函数去计算隐藏层的状态，如图所示：

<center>

![](http://images.iterate.site/blog/image/20190616/bbDTaLKDsvJS.png?imageslim){ width=55% }

</center>

在 LSTM 中，i 结构被称为 cells，可以把 cells 看作是黑盒，用以保存当前输入 $x_t$ 之前保存的状态 $h_{t-1}$，这些 cells 以一定的条件决定哪些 cell 抑制，哪些 cell 兴奋。它们可以结合前面的状态、当前的记忆与当前的输入。该网络结构在对长序列依赖问题中非常有效。


LSTM神经元的输出除了与当前输入有关外，还与自身记忆有关。RNN 的训练算法也是基于传统 BP 算法，并且增加了时间考量，称为 BPTT （Back-propagation Through Time）算法。Google 推出的邮件智能回复也是基于 LSTM 模型，只不过是用了一对，一个用来编码邮件，一个用来解码回复。<span style="color:red;">这个地方怎么又提到 Google 的邮件智能回复了？什么是使用了一对？怎么就用来解码回复了？
</span>

LSTM 元素的图标：

<center>

![](http://images.iterate.site/blog/image/20190616/q3Op1neMBFs5.png?imageslim){ width=55% }

</center>

介绍如下：

- Neural NetWork Layer：表示一个神经网络层。
- Pointwise Operation：表示一种数学操作。
- Vector Tansfer：表示每条线代表一个向量，从一个节点输出到另一个节点。
- Concatenate：表示两个向量的合并。
- Copy：表示复制一个向量变成相同的两个向量。





# 相关

- 《深度学习框架 Pytorch 快速开发与实战》
