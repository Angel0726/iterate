---
title: 02 Word2Vec
toc: true
date: 2019-09-29
---
# Word2Vec


2013年最火的用语言模型做 Word Embedding的工具是 Word2Vec，后来又出了 Glove，Word2Vec是怎么工作的呢？看下图。


<center>

![mark](http://images.iterate.site/blog/image/20190926/Yst8BWrYa9Y0.png?imageslim)

</center>


Word2Vec的网络结构其实和 NNLM 是基本类似的，只是这个图长得清晰度差了点，看上去不像，其实它们是亲兄弟。

不过这里需要指出：尽管网络结构相近，而且也是做语言模型任务，但是其训练方法不太一样。

Word2Vec有两种训练方法：

- 一种叫 CBOW，核心思想是从一个句子里面把一个词抠掉，用这个词的上文和下文去预测被抠掉的这个词；
- 第二种叫做 Skip-gram，和 CBOW 正好反过来，输入某个单词，要求网络预测它的上下文单词。

而你回头看看，NNLM是怎么训练的？是输入一个单词的上文，去预测这个单词。这是有显著差异的。

为什么 Word2Vec 这么处理？原因很简单，因为 Word2Vec 和 NNLM 不一样，**NNLM的主要任务是要学习一个解决语言模型任务的网络结构，语言模型就是要看到上文预测下文，而 word embedding只是无心插柳的一个副产品。** 但是 Word2Vec 目标不一样，它单纯就是要 word embedding的，这是主产品，所以它完全可以随性地这么去训练网络。

为什么要讲 Word2Vec 呢？这里主要是要引出 CBOW 的训练方法，BERT其实跟它有关系，后面会讲它们之间是如何的关系，当然它们的关系 BERT 作者没说，是我猜的，至于我猜的对不对，后面你看后自己判断。

<center>

![mark](http://images.iterate.site/blog/image/20190926/ctU9edpVTcOQ.png?imageslim)

</center>


使用 Word2Vec 或者 Glove，通过做语言模型任务，就可以获得每个单词的 Word Embedding，那么这种方法的效果如何呢？上图给了网上找的几个例子，可以看出有些例子效果还是很不错的，一个单词表达成 Word Embedding后，很容易找出语义相近的其它词汇。

## 学会 Word Embedding 后下游任务是怎么用它的

我们的主题是预训练，那么问题是 Word Embedding这种做法能算是预训练吗？这其实就是标准的预训练过程。要理解这一点要看看学会 Word Embedding后下游任务是怎么用它的。


<center>

![mark](http://images.iterate.site/blog/image/20190926/0gUVRi03sGdb.png?imageslim)

</center>


假设如上图所示，我们有个 NLP 的下游任务，比如 QA，就是问答问题，所谓问答问题，指的是给定一个问题 X，给定另外一个句子 Y，要判断句子 Y 是否是问题 X 的正确答案。

问答问题假设设计的网络结构如上图所示，这里不展开讲了，懂得自然懂，不懂的也没关系，因为这点对于本文主旨来说不关键，关键是网络如何使用训练好的 Word Embedding的。它的使用方法其实和前面讲的 NNLM 是一样的，句子中每个单词以 Onehot 形式作为输入，然后乘以学好的 Word Embedding矩阵 Q，就直接取出单词对应的 Word Embedding了。这乍看上去好像是个查表操作，不像是预训练的做法是吧？其实不然，那个 Word Embedding矩阵 Q 其实就是网络 Onehot 层到 embedding 层映射的网络参数矩阵。

所以你看到了，使用 Word Embedding等价于什么？**等价于把 Onehot 层到 embedding 层的网络用预训练好的参数矩阵 $Q$ 初始化了。**

这跟图像领域的低层预训练过程其实是一样的，区别无非 Word Embedding 只能初始化第一层网络参数，再高层的参数就无能为力了。

下游 NLP 任务在使用 Word Embedding的时候也类似图像有两种做法：

- 一种是 Frozen，就是 Word Embedding那层网络参数固定不动；
- 另外一种是 Fine-Tuning，就是 Word Embedding这层参数使用新的训练集合训练也需要跟着训练过程更新掉。

上面这种做法就是 18 年之前 NLP 领域里面采用预训练的典型做法，之前说过，Word Embedding其实对于很多下游 NLP 任务是有帮助的，只是帮助没有大到闪瞎双眼而已。

那么新问题来了，为什么这样训练及使用 Word Embedding的效果没有期待中那么好呢？

答案很简单，因为 Word Embedding有问题呗。这貌似是个比较弱智的答案，关键是 Word Embedding存在什么问题？这其实是个好问题。

## Word Embedding 存在的问题


<center>

![mark](http://images.iterate.site/blog/image/20190926/kRlmvThCxdKI.png?imageslim)

</center>


这片在 Word Embedding头上笼罩了好几年的乌云是什么？是多义词问题。我们知道，多义词是自然语言中经常出现的现象，也是语言灵活性和高效性的一种体现。

多义词对 Word Embedding来说有什么负面影响？如上图所示，比如多义词 Bank，有两个常用含义，但是 Word Embedding在对 bank 这个单词进行编码的时候，是区分不开这两个含义的，因为它们尽管上下文环境中出现的单词不同，但是在用语言模型训练的时候，**不论什么上下文的句子经过 word2vec，都是预测相同的单词 bank，而同一个单词占的是同一行的参数空间，这导致两种不同的上下文信息都会编码到相同的 word embedding空间里去。所以 word embedding无法区分多义词的不同语义**，这就是它的一个比较严重的问题。

你可能觉得自己很聪明，说这可以解决啊，确实也有很多研究人员提出很多方法试图解决这个问题，但是从今天往回看，这些方法看上去都成本太高或者太繁琐了，有没有简单优美的解决方案呢？

ELMO提供了一种简洁优雅的解决方案。


# 相关

- [从 Word Embedding到 Bert 模型—自然语言处理中的预训练技术发展史](https://zhuanlan.zhihu.com/p/49271699)
