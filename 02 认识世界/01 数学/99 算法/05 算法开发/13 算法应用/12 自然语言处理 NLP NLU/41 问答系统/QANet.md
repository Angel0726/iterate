---
title: QANet
toc: true
date: 2019-10-13
---
# QANet

ICLR 2018《QANet: Combining Local Convolution with Global Self-Attention for Reading Comprehension》

论文地址：<https://openreview.net/forum?id=B14TlG-RW>

主要看：

- QANet 是一个什么样的模型？
- 如何同时大幅提升了精度和训练时间的？

## QANet 模型

（1）形式化定义

给定一个包含 n 个单词的上下文片段 C={c1,c2,...,cn},我们考虑包含 m 个单词的查询语句 Q={q1,q2,...,qm},模型输出为一个包含 j 个单词的片段 C 中的答案区间 S={ci,ci+1,...,ci+j}。

（2）模型概览

大体上来说，和现有的阅读理解模型相类似，QANet 包含五个主要的组成部分：嵌入层 （embedding layer），嵌入编码层（embedding encoder layer），语境-查询注意力层（context-query attention layer），模型编码层（model encoder）以及输出层（output layer）。

区别于目前大多数包含注意力机制（attention model）和循环神经网络（RNN）的阅读理解模型，QANet 的嵌入编码器和模型编码器摒弃了 RNN 的复杂递归结构，**仅仅使用卷积（convolution）和自注意力机制（self-attention）构建了一个神经网络**，使得模型的训练速率和推断速率大大加快，并且可以并行处理输入的单词。

卷积操作可以对局部相互作用建模（捕获文本的局部结构），而使用自注意力机制则可以对全局交互进行建模（学习每对单词之间的相互作用）。据作者们介绍，这也是领域内首次将卷积和自注意力机制相结合。由于卷积层和自注意力机制都没有消耗时间的递归操作，所以作者们不仅大胆地把模型深度增加到了问答任务中史无前例的超过 130 层，同时还在训练、推理中都有数倍的速度提升。（相较于基于 RNN 的模型，训练速度提升了3-13倍，推理速度提升了 4-9 倍）

![问答系统冠军之路：用 CNN 做问答任务的 QANet](https://static.leiphone.com/uploads/new/article/740_740/201805/5aefe9c47a51c.jpeg)

图 3: 左图为包含多个编码器模块的 QANet 整体架构。右图为基本编码器模块单元，QANet 所使用的所有编码器都是按照这个模式构建的，仅仅修改模块中卷积层的数量。QANet 在每一层之间会使用层正则化和残差连接技术，并且将编码器结构内位置编码（卷积、自注意力、前馈网络等）之后的每个子层封装在残差模块内。QANet 还共享了语境、问题、输出编码器之间的部分权重，以达到知识共享。

以往基于 RNN 的模型受制于训练速度，研究员们其实很少考虑图像识别任务中类似的「用更大的数据集带来更好表现」的思路。那么对于这次的 QANet，由于模型有令人满意的训练速度，作者们得以手脚，使用数据增强技术对原始数据集进行了扩充，大大方方用更多数据训练了模型。

具体来说，他们把英文原文用现有的神经机器翻译器翻译成另一种语言（QANet 使用的是法语）之后再翻译回英语。这个过程相当于对样本进行了改写，这样使得训练样本的数量大大增加，句式更加丰富。

![问答系统冠军之路：用 CNN 做问答任务的 QANet](https://static.leiphone.com/uploads/new/article/740_740/201805/5af0066735f41.jpeg)

图 4: 数据增强示例。k 为 beam width，即 NMT 系统产生的译文规模。

## 详细介绍 self-attention

读罢上文，你可能惊叹于「卷积+自注意力机制」的神奇效果，即便考虑到更多训练数据的帮助，也仍然可以直接挑战使用已久的 RNN 模型。「卷积」是大多数深度学习的研究者十分熟悉的概念，它用于提取局部的特征。那么，自注意力（self-atteition）机制又是何方神圣呢？它在 QANet 中起到了什么关键性的作用？

要想弄清 self-attetion 机制，就不得不从 attention 机制的原理谈起，因为 self-attention 顾名思义，可以看作attention 机制的一种内部形式的特例。

![问答系统冠军之路：用 CNN 做问答任务的 QANet](https://static.leiphone.com/uploads/new/article/740_740/201805/5af008d17e8d7.png?imageMogr2/format/jpg/quality/90)

图 5: attention 机制原理示意图

我们可以将原句中的每一个单词看作一个 <Key,Value> 数据对，即原句可表示为一系列 <Key,Value> 数据对的组合。这时我们要通过计算 Query 和 每一个 Key 的相似性，得到每个 Key 对应的 Value 的权重，再对 Value 进行加权求和，得到 Attention 值。这个过程在于模拟人在看图片或者阅读文章时，由于先验信息和任务目标不同，我们对于文本的不同部分关注程度存在差异。例如：在语句「Father hits me！」中，如果我们关心的是「谁打了我？」那么，Father 的权重应该就要较高。这种机制有利于我们从大量的信息中筛选出有用的部分，而过滤掉对我们的任务不重要的部分，从而提高了模型的效率和准确率。

而区别于一般的编码器-解码器结构中使用的 Attention model（输入和输出的内容不同），self attention 机制并不是输入和输出之间的 attention 机制，而是输入内部的单词或者输出内部单词之间的 attention 机制。Self-attention即K=V=Q，在 QANet 中，作者使得原文中每一对单词的相互作用都能够被刻画出来，捕获整篇文章的内部结构。

使用 self-attention 有以下好处：

（1）在并行方面，self-attention 和 CNN一样不依赖于前一时刻的计算，可以很好的并行，优于RNN。

（2）在长距离依赖上，由于 self-attention 是每个词和所有词都要计算 attention，所以不管他们中间有多长距离，最大的路径长度也都只是 1。可以高效捕获长距离依赖关系。

![问答系统冠军之路：用 CNN 做问答任务的 QANet](https://static.leiphone.com/uploads/new/article/740_740/201805/5af00e4a382b0.jpg?imageMogr2/format/jpg/quality/90)

图6: self-attention 机制示意图

因此，在使用了 self-attention 机制之后，模型可以对单词进行并行化处理，大大提高了运行效率；使得模型能够使用更多数据进行训练，可以捕获长距离的依赖关系，也从另一个方面提升了模型的准确率。





QANet，是第一个利用「卷积+self-attention」机制代替其他模型广泛使用的 RNN 网络的问答系统模型。它并不依赖于 RNN 的递归训练来提升模型性能，反而另辟蹊径，通过较为简单的网络组建节省了计算开销，使得我们使用更多的数据进行训练成为了可能，从而使模型的性能得到了质的飞跃。此外，他们设计的数据增强方法也十分巧妙，并取得了很好的效果。可见，人工智能研究之路本是「八仙过海，各显神通」，广大研究者们还需开阔思路，不囿于前人的思维定式，方可曲线突围！





# 相关

- [问答系统冠军之路：用 CNN 做问答任务的 QANet](https://www.leiphone.com/news/201805/A1mkxTOKWrZOY64l.html)
