---
title: 05 BERT
toc: true
date: 2019-09-29
---
# 可以补充进来的

- [BERT相关论文、文章和代码资源汇总](http://www.52nlp.cn/tag/bert%E8%A7%A3%E8%AF%BB)


# Bert


## Bert 的诞生



<center>

![mark](http://images.iterate.site/blog/image/20190927/kGvyBqvsyK6n.png?imageslim)

</center>



我们经过跋山涉水，终于到了目的地 Bert 模型了。

Bert采用和 GPT 完全相同的两阶段模型，首先是语言模型预训练；其次是使用 Fine-Tuning模式解决下游任务。

和 GPT 的最主要不同在于在预训练阶段采用了类似 ELMO 的双向语言模型，当然另外一点是语言模型的数据规模要比 GPT 大。所以这里 Bert 的预训练过程不必多讲了。



## Bert 训练好后如何使用

<center>

![mark](http://images.iterate.site/blog/image/20190927/CM9y5hkQtG6l.png?imageslim)

</center>



第二阶段，Fine-Tuning阶段，这个阶段的做法和 GPT 是一样的。当然，它也面临着下游任务网络结构改造的问题，在改造任务方面 Bert 和 GPT 有些不同，下面简单介绍一下。

<center>

![mark](http://images.iterate.site/blog/image/20190927/nkK6N2vUCGcv.png?imageslim)

</center>


在介绍 Bert 如何改造下游任务之前，先大致说下 NLP 的几类问题，说这个是为了强调 Bert 的普适性有多强。

通常而言，绝大部分 NLP 问题可以归入上图所示的四类任务中：

- 一类是序列标注，这是最典型的 NLP 任务，比如中文分词，词性标注，命名实体识别，语义角色标注等都可以归入这一类问题，它的特点是句子中每个单词要求模型根据上下文都要给出一个分类类别。
- 第二类是分类任务，比如我们常见的文本分类，情感计算等都可以归入这一类。它的特点是不管文章有多长，总体给出一个分类类别即可。
- 第三类任务是句子关系判断，比如 Entailment，QA，语义改写，自然语言推理等任务都是这个模式，它的特点是给定两个句子，模型判断出两个句子是否具备某种语义关系；
- 第四类是生成式任务，比如机器翻译，文本摘要，写诗造句，看图说话等都属于这一类。它的特点是输入文本内容后，需要自主生成另外一段文字。





<center>

![mark](http://images.iterate.site/blog/image/20190927/YcLbfin8qJvr.png?imageslim)

</center>



对于种类如此繁多而且各具特点的下游 NLP 任务，Bert如何改造输入输出部分使得大部分 NLP 任务都可以使用 Bert 预训练好的模型参数呢？

上图给出示例：

- 对于句子关系类任务，很简单，和 GPT 类似，加上一个起始和终结符号，句子之间加个分隔符即可。对于输出来说，把第一个起始符号对应的 Transformer 最后一层位置上面串接一个 softmax 分类层即可。
- 对于分类问题，与 GPT 一样，只需要增加起始和终结符号，输出部分和句子关系判断任务类似改造；
- 对于序列标注问题，输入部分和单句分类是一样的，只需要输出部分 Transformer 最后一层每个单词对应位置都进行分类即可。

从这里可以看出，上面列出的 NLP 四大任务里面，除了生成类任务外，Bert其它都覆盖到了，而且改造起来很简单直观。

尽管 Bert 论文没有提，但是稍微动动脑子就可以想到，其实对于机器翻译或者文本摘要，聊天机器人这种生成式任务，同样可以稍作改造即可引入 Bert 的预训练成果。只需要附着在 S2S 结构上，encoder部分是个深度 Transformer 结构，decoder部分也是个深度 Transformer 结构。根据任务选择不同的预训练数据初始化 encoder 和 decoder 即可。这是相当直观的一种改造方法。当然，也可以更简单一点，比如直接在单个 Transformer 结构上加装隐层产生输出也是可以的。

不论如何，从这里可以看出，NLP四大类任务都可以比较方便地改造成 Bert 能够接受的方式。这其实是 Bert 的非常大的优点，这意味着它几乎可以做任何 NLP 的下游任务，具备普适性，这是很强的。

## Bert 效果如何

<center>

![mark](http://images.iterate.site/blog/image/20190927/U9nXnpBnisuY.png?imageslim)

</center>



Bert采用这种两阶段方式解决各种 NLP 任务效果如何？

在 11 个各种类型的 NLP 任务中达到目前最好的效果，某些任务性能有极大的提升。一个新模型好不好，效果才是王道。



## 从 GPT 和 ELMO 及 Word2Vec 到 Bert ，四者的关系

<center>

![mark](http://images.iterate.site/blog/image/20190927/yPyTkvvqWr4j.png?imageslim)

</center>

到这里我们可以再梳理下几个模型之间的演进关系。从上图可见，Bert其实和 ELMO 及 GPT 存在千丝万缕的关系，比如如果我们把 GPT 预训练阶段换成双向语言模型，那么就得到了 Bert；而如果我们把 ELMO 的特征抽取器换成 Transformer，那么我们也会得到 Bert。

所以你可以看出：Bert最关键两点：

- 一点是特征抽取器采用 Transformer
- 第二点是预训练的时候采用双向语言模型。

那么新问题来了：对于 Transformer 来说，怎么才能在这个结构上做双向语言模型任务呢？

乍一看上去好像不太好搞。我觉得吧，其实有一种很直观的思路，怎么办？看看 ELMO 的网络结构图，只需要把两个 LSTM 替换成两个 Transformer，一个负责正向，一个负责反向特征提取，其实应该就可以。当然这是我自己的改造，Bert没这么做。

那么 Bert 是怎么做的呢？我们前面不是提过 Word2Vec 吗？我前面肯定不是漫无目的地提到它，提它是为了在这里引出那个 CBOW 训练方法，所谓写作时候埋伏笔的“草蛇灰线，伏脉千里”，大概就是这个意思吧？前面提到了 CBOW 方法，它的核心思想是：在做语言模型任务的时候，我把要预测的单词抠掉，然后根据它的上文 Context-Before和下文 Context-after去预测单词。其实 Bert 怎么做的？Bert就是这么做的。从这里可以看到方法间的继承关系。当然 Bert 作者没提 Word2Vec 及 CBOW 方法，这是我的判断，Bert作者说是受到完形填空任务的启发，这也很可能，但是我觉得他们要是没想到过 CBOW 估计是不太可能的。

从这里可以看出，在文章开始我说过 Bert 在模型方面其实没有太大创新，更像一个最近几年 NLP 重要技术的集大成者，原因在于此，当然我不确定你怎么看，是否认同这种看法，而且我也不关心你怎么看。其实 Bert 本身的效果好和普适性强才是最大的亮点。


## Bert 如何构造双向语言模型


<center>

![mark](http://images.iterate.site/blog/image/20190927/dypoe3or9mLA.png?imageslim)

</center>

那么 Bert 本身在模型和方法角度有什么创新呢？

就是论文中指出的 Masked 语言模型和 Next Sentence Prediction。而 Masked 语言模型上面讲了，本质思想其实是 CBOW，但是细节方面有改进。



<center>

![mark](http://images.iterate.site/blog/image/20190927/FAh1x7nb1wTF.png?imageslim)

</center>



Masked双向语言模型向上图展示这么做：

随机选择语料中 15%的单词，把它抠掉，也就是用[Mask]掩码代替原始单词，然后要求模型去正确预测被抠掉的单词。但是这里有个问题：训练过程大量看到[mask]标记，但是真正后面用的时候是不会有这个标记的，这会引导模型认为输出是针对[mask]这个标记的，但是实际使用又见不到这个标记，这自然会有问题。为了避免这个问题，Bert改造了一下，15%的被上天选中要执行[mask]替身这项光荣任务的单词中，只有 80%真正被替换成[mask]标记，10%被狸猫换太子随机替换成另外一个单词，10%情况这个单词还待在原地不做改动。这就是 Masked 双向语音模型的具体做法。



<center>

![mark](http://images.iterate.site/blog/image/20190927/4K82lPceEiYw.png?imageslim)

</center>



至于说“Next Sentence Prediction”，指的是做语言模型预训练的时候，分两种情况选择两个句子，一种是选择语料中真正顺序相连的两个句子；另外一种是第二个句子从语料库中抛色子，随机选择一个拼到第一个句子后面。我们要求模型除了做上述的 Masked 语言模型任务外，附带再做个句子关系预测，判断第二个句子是不是真的是第一个句子的后续句子。之所以这么做，是考虑到很多 NLP 任务是句子关系判断任务，单词预测粒度的训练到不了句子关系这个层级，增加这个任务有助于下游句子关系判断任务。所以可以看到，它的预训练是个多任务过程。这也是 Bert 的一个创新。



<center>

![mark](http://images.iterate.site/blog/image/20190927/B9bIFv8szXjs.png?imageslim)

</center>

上面这个图给出了一个我们此前利用微博数据和开源的 Bert 做预训练时随机抽出的一个中文训练实例，从中可以体会下上面讲的 masked 语言模型和下句预测任务。训练数据就长这种样子。



<center>

![mark](http://images.iterate.site/blog/image/20190927/2q3RcECkiftK.png?imageslim)

</center>

顺带讲解下 Bert 的输入部分，也算是有些特色。它的输入部分是个线性序列，两个句子通过分隔符分割，最前面和最后增加两个标识符号。

每个单词有三个 embedding：

- 位置信息 embedding，这是因为 NLP 中单词顺序是很重要的特征，需要在这里对位置信息进行编码；
- 单词 embedding，这个就是我们之前一直提到的单词 embedding；
- 第三个是句子 embedding，因为前面提到训练数据都是由两个句子构成的，那么每个句子有个句子整体的 embedding 项对应给每个单词。

把单词对应的三个 embedding 叠加，就形成了 Bert 的输入。



<center>

![mark](http://images.iterate.site/blog/image/20190927/vcq9vwvjT3DC.png?imageslim)

</center>

至于 Bert 在预训练的输出部分如何组织，可以参考上图的注释。

## Bert 的有效因子分析

<center>

![mark](http://images.iterate.site/blog/image/20190927/OXRpBQ6NdTKz.png?imageslim)

</center>

我们说过 Bert 效果特别好，那么到底是什么因素起作用呢？如上图所示，对比试验可以证明，跟 GPT 相比，双向语言模型起到了最主要的作用，对于那些需要看到下文的任务来说尤其如此。而预测下个句子来说对整体性能来说影响不算太大，跟具体任务关联度比较高。


## Bert 的评价和意义

<center>

![mark](http://images.iterate.site/blog/image/20190927/iuXc34zxVKGL.png?imageslim)

</center>

最后，我讲讲我对 Bert 的评价和看法，我觉得 Bert 是 NLP 里里程碑式的工作，对于后面 NLP 的研究和工业应用会产生长久的影响，这点毫无疑问。

但是从上文介绍也可以看出，从模型或者方法角度看，Bert借鉴了 ELMO，GPT及 CBOW，主要提出了 Masked 语言模型及 Next Sentence Prediction，但是这里 Next Sentence Prediction基本不影响大局，而 Masked LM明显借鉴了 CBOW 的思想。所以说 Bert 的模型没什么大的创新，更像最近几年 NLP 重要进展的集大成者，这点如果你看懂了上文估计也没有太大异议，如果你有大的异议，杠精这个大帽子我随时准备戴给你。

如果归纳一下这些进展就是：

- 首先是两阶段模型，第一阶段双向语言模型预训练，这里注意要用双向而不是单向，第二阶段采用具体任务 Fine-tuning或者做特征集成；
- 第二是特征抽取要用 Transformer 作为特征提取器而不是 RNN 或者 CNN；
- 第三，双向语言模型可以采取 CBOW 的方法去做（当然我觉得这个是个细节问题，不算太关键，前两个因素比较关键）。

Bert最大的亮点在于效果好及普适性强，几乎所有 NLP 任务都可以套用 Bert 这种两阶段解决思路，而且效果应该会有明显提升。可以预见的是，未来一段时间在 NLP 应用领域，Transformer将占据主导地位，而且这种两阶段预训练方法也会主导各种应用。

## NLP 的预训练这个过程本质上是在做什么事情

另外，我们应该弄清楚预训练这个过程本质上是在做什么事情，本质上预训练是通过设计好一个网络结构来做语言模型任务，然后把大量甚至是无穷尽的无标注的自然语言文本利用起来。

预训练过程就是把大量语言学知识抽取出来编码到网络结构中，当手头任务带有标注信息的数据有限时，这些先验的语言学特征当然会对手头任务有极大的特征补充作用，因为当数据有限的时候，很多语言学现象是覆盖不到的，泛化能力就弱，集成尽量通用的语言学知识自然会加强模型的泛化能力。

如何引入先验的语言学知识其实一直是 NLP 尤其是深度学习场景下的 NLP 的主要目标之一，不过一直没有太好的解决办法，而 ELMO/GPT/Bert 的这种两阶段模式看起来无疑是解决这个问题自然又简洁的方法，这也是这些方法的主要价值所在。

对于当前 NLP 的发展方向，我个人觉得有两点非常重要：

- 一个是需要更强的特征抽取器，目前看 Transformer 会逐渐担当大任，但是肯定还是不够强的，需要发展更强的特征抽取器；
- 第二个就是如何优雅地引入大量无监督数据中包含的语言学知识，注意我这里强调地是优雅，而不是引入，此前相当多的工作试图做各种语言学知识的嫁接或者引入，但是很多方法看着让人牙疼，就是我说的不优雅。目前看预训练这种两阶段方法还是很有效的，也非常简洁，当然后面肯定还会有更好的模型出现。



# 相关

- [从 Word Embedding到 Bert 模型—自然语言处理中的预训练技术发展史](https://zhuanlan.zhihu.com/p/49271699)
