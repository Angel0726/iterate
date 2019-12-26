---
title: 03 ELMO
toc: true
date: 2019-09-29
---


# 从 Word Embedding到 ELMO

ELMO是“Embedding from Language Models”的简称，其实这个名字并没有反应它的本质思想，提出 ELMO 的论文题目：“Deep contextualized word representation”更能体现其精髓，而精髓在哪里？在 deep contextualized这个短语，一个是 deep，一个是 context，其中 context 更关键。

在此之前的 Word Embedding本质上是个静态的方式，所谓静态指的是训练好之后每个单词的表达就固定住了，以后使用的时候，不论新句子上下文单词是什么，这个单词的 Word Embedding不会跟着上下文场景的变化而改变，所以对于比如 Bank 这个词，它事先学好的 Word Embedding中混合了几种语义 ，在应用中来了个新句子，即使从上下文中（比如句子包含 money 等词）明显可以看出它代表的是“银行”的含义，但是对应的 Word Embedding内容也不会变，它还是混合了多种语义。这是为何说它是静态的，这也是问题所在。

ELMO的本质思想是：我事先用语言模型学好一个单词的 Word Embedding，此时多义词无法区分，不过这没关系。在我实际使用 Word Embedding的时候，单词已经具备了特定的上下文了，这个时候我可以根据上下文单词的语义去调整单词的 Word Embedding表示，这样经过调整后的 Word Embedding更能表达在这个上下文中的具体含义，自然也就解决了多义词的问题了。所以**ELMO本身是个根据当前上下文对 Word Embedding动态调整的思路**。

## ELMO 的预训练阶段

<center>

![mark](http://images.iterate.site/blog/image/20190926/D4UxfouyonHV.png?imageslim)

</center>



ELMO采用了典型的两阶段过程：

- 第一个阶段是利用语言模型进行预训练；
- 第二个阶段是在做下游任务时，从预训练网络中提取对应单词的网络各层的 Word Embedding作为新特征补充到下游任务中。

上图展示的是其预训练过程，它的网络结构采用了双层双向 LSTM，目前语言模型训练的任务目标是根据单词 $W_{i}$ 的上下文去正确预测单词 $W_{i}$ ， $W_{i}$ 之前的单词序列 Context-before称为上文，之后的单词序列 Context-after称为下文。

图中左端的前向双层 LSTM 代表正方向编码器，输入的是从左到右顺序的除了预测单词外 $W_{i}$ 的上文 Context-before；右端的逆向双层 LSTM 代表反方向编码器，输入的是从右到左的逆序的句子下文 Context-after；每个编码器的深度都是两层 LSTM 叠加。

这个网络结构其实在 NLP 中是很常用的。使用这个网络结构利用大量语料做语言模型任务就能预先训练好这个网络，如果训练好这个网络后，输入一个新句子 Snew ，句子中每个单词都能得到对应的三个 Embedding：

- 最底层是单词的 Word Embedding，
- 往上走是第一层双向 LSTM 中对应单词位置的 Embedding，这层编码单词的句法信息更多一些；
- 再往上走是第二层 LSTM 中对应单词位置的 Embedding，这层编码单词的语义信息更多一些。

也就是说，ELMO的预训练过程不仅仅学会单词的 Word Embedding，还学会了一个双层双向的 LSTM 网络结构，而这两者后面都有用。

## 训练好后如何使用

<center>

![mark](http://images.iterate.site/blog/image/20190927/kAyhpsjJCia1.png?imageslim)

</center>


上面介绍的是 ELMO 的第一阶段：预训练阶段。那么预训练好网络结构后，如何给下游任务使用呢？

上图展示了下游任务的使用过程，比如我们的下游任务仍然是 QA 问题，此时对于问句 X，我们可以先将句子 X 作为预训练好的 ELMO 网络的输入，这样句子 X 中每个单词在 ELMO 网络中都能获得对应的三个 Embedding，之后给予这三个 Embedding 中的每一个 Embedding 一个权重 a，这个权重可以学习得来，根据各自权重累加求和，将三个 Embedding 整合成一个。然后将整合后的这个 Embedding 作为 X 句在自己任务的那个网络结构中对应单词的输入，以此作为补充的新特征给下游任务使用。

对于上图所示下游任务 QA 中的回答句子 Y 来说也是如此处理。因为 ELMO 给下游提供的是每个单词的特征形式，所以这一类预训练的方法被称为“Feature-based Pre-Training”。

至于为何这么做能够达到区分多义词的效果，你可以想一想，其实比较容易想明白原因。


<center>

![mark](http://images.iterate.site/blog/image/20190927/Q9Sq17B3EXMp.png?imageslim)

</center>


上面这个图是 TagLM 采用类似 ELMO 的思路做命名实体识别任务的过程，其步骤基本如上述 ELMO 的思路，所以此处不展开说了。

TagLM的论文发表在 2017 年的 ACL 会议上，作者就是 AllenAI 里做 ELMO 的那些人，所以可以将 TagLM 看做 ELMO 的一个前导工作。前几天这个 PPT 发出去后有人质疑说 FastAI 的在 18 年 4 月提出的 ULMFiT 才是抛弃传统 Word Embedding引入新模式的开山之作，我深不以为然。首先 TagLM 出现的更早而且模式基本就是 ELMO 的思路；另外 ULMFiT 使用的是三阶段模式，在通用语言模型训练之后，加入了一个领域语言模型预训练过程，而且论文重点工作在这块，方法还相对比较繁杂，这并不是一个特别好的主意，因为领域语言模型的限制是它的规模往往不可能特别大，精力放在这里不太合适，放在通用语言模型上感觉更合理；再者，尽管 ULFMiT 实验做了 6 个任务，但是都集中在分类问题相对比较窄，不如 ELMO 验证的问题领域广，我觉得这就是因为第二步那个领域语言模型带来的限制。所以综合看，尽管 ULFMiT 也是个不错的工作，但是重要性跟 ELMO 比至少还是要差一档，当然这是我个人看法。每个人的学术审美口味不同，我个人一直比较赞赏要么简洁有效体现问题本质要么思想特别游离现有框架脑洞开得异常大的工作，所以 ULFMiT 我看论文的时候就感觉看着有点难受，觉得这工作没抓住重点而且特别麻烦，但是看 ELMO 论文感觉就赏心悦目，觉得思路特别清晰顺畅，看完暗暗点赞，心里说这样的文章获得 NAACL2018 最佳论文当之无愧，比 ACL 很多最佳论文也好得不是一点半点，这就是好工作带给一个有经验人士的一种在读论文时候就能产生的本能的感觉，也就是所谓的这道菜对上了食客的审美口味。

## ELMO 的多义词问题解决了吗

<center>

![mark](http://images.iterate.site/blog/image/20190927/3Fml13mgNE0L.png?imageslim)

</center>


前面我们提到静态 Word Embedding无法解决多义词的问题，那么 ELMO 引入上下文动态调整单词的 embedding 后多义词问题解决了吗？

解决了，而且比我们期待的解决得还要好。

上图给了个例子，对于 Glove 训练出的 Word Embedding来说，多义词比如 play，根据它的 embedding 找出的最接近的其它单词大多数集中在体育领域，这很明显是因为训练数据中包含 play 的句子中体育领域的数量明显占优导致；而使用 ELMO，根据上下文动态调整后的 embedding 不仅能够找出对应的“演出”的相同语义的句子，而且还可以保证找出的句子中的 play 对应的词性也是相同的，这是超出期待之处。

**之所以会这样，是因为我们上面提到过，第一层 LSTM 编码了很多句法信息，这在这里起到了重要作用。**


<center>

![mark](http://images.iterate.site/blog/image/20190927/vpS3JbDT4fWQ.png?imageslim)

</center>


ELMO 经过这般操作，效果如何呢？实验效果见上图，6个 NLP 任务中性能都有幅度不同的提升，最高的提升达到 25%左右，而且这 6 个任务的覆盖范围比较广，包含句子语义关系判断，分类任务，阅读理解等多个领域，这说明其适用范围是非常广的，普适性强，这是一个非常好的优点。<span style="color:red;">厉害！</span>



## ELMO 有什么缺点

<center>

![mark](http://images.iterate.site/blog/image/20190927/SOYaCHurptxx.png?imageslim)

</center>


那么站在现在这个时间节点看，ELMO有什么值得改进的缺点呢？

首先，一个非常明显的缺点在特征抽取器选择方面，ELMO使用了 LSTM 而不是新贵 Transformer，Transformer是谷歌在 17 年做机器翻译任务的“Attention is all you need”的论文中提出的，引起了相当大的反响，**很多研究已经证明了 Transformer 提取特征的能力是要远强于 LSTM 的**。

如果 ELMO 采取 Transformer 作为特征提取器，那么估计 Bert 的反响远不如现在的这种火爆场面。另外一点，ELMO采取双向拼接这种融合特征的能力可能比 Bert 一体化的融合特征方式弱，但是，这只是一种从道理推断产生的怀疑，目前并没有具体实验说明这一点。

我们如果把 ELMO 这种预训练方法和图像领域的预训练方法对比，发现两者模式看上去还是有很大差异的。

除了以 ELMO 为代表的这种基于特征融合的预训练方法外，NLP里还有一种典型做法，这种做法和图像领域的方式就是看上去一致的了，一般将这种方法称为“基于 Fine-tuning的模式”，而 GPT 就是这一模式的典型开创者。


# 相关

- [从 Word Embedding到 Bert 模型—自然语言处理中的预训练技术发展史](https://zhuanlan.zhihu.com/p/49271699)
