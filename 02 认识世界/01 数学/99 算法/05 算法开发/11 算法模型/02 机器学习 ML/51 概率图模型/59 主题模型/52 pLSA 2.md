---
title: 52 pLSA 2
toc: true
date: 2018-08-12 20:04:22
---
# pLSA


# 相关

1. 七月在线 机器学习


# MOTIVE

对 pLSA 做总结，看下是如何把 EM 算法应用在 pLSA 上面的。


# 为什么要引入隐变量？


朴素贝叶斯的分析


![](http://images.iterate.site/blog/image/180728/7H202mH9K6.png?imageslim){ width=55% }

因此加入主题这个隐变量。

增加这个隐变量可以一定程度解决问题。

文档和主题：

![](http://images.iterate.site/blog/image/180728/514Cd066k6.png?imageslim){ width=55% }

不管是 pLSA 还是 LDA，具体的做法都非常简单，但是困难的是中间的推理过程。

为什么这个理论是有道理的。

先讲一些 pLSA 模型。


# pLSA模型


基于概率统计的 pLSA 模型(probabilistic Latent Semantic Analysis，概率隐语义分析)，增加了主题模型，形成简单的贝叶斯网络，可以使用 EM 算法学习模型参数。

![](http://images.iterate.site/blog/image/180728/83bf1CGmJf.png?imageslim){ width=55% }

这个就是增加主题的基本的网络模型。这么简单的一个贝叶斯网络就是 pLSA 模型，分析一下这个样的网络发生什么事情：

![](http://images.iterate.site/blog/image/180728/IAgGGG3eKl.png?imageslim){ width=55% }

每个文档在所有主题上服从多项分布，的意思是：

比如![](http://images.iterate.site/blog/image/180728/3h5B9H2mha.png?imageslim){ width=55% }第一篇文档除了是小说，还是关于民国的。

每个主题在所有词项上服从多项分布：

假如词典有 V 个词，那么主题在所有的词上就服从 v 项分布。

假设主题有 t 个，那么每个文档在所有的主题上服从 t 项分布

**整个文档的生成过程是什么意思？ **




## 现在我们来看一下如何进行这个主题模型的建立




![](http://images.iterate.site/blog/image/180728/fL7KjCmlKF.png?imageslim){ width=55% }

对于![](http://images.iterate.site/blog/image/180728/be1Em9FKGb.png?imageslim){ width=55% }这个解释一下：

由全概率公式得：\(p(w_j)=\sum_{k=1}^{K}p(w_j\mid z_k)*p(z_k)\) 而，这里都加了 d 而已，由于给定 z 得时候，d和 w 是 head-to-tail 得关系，也就是说给定 z 的时候，d和 w 是独立的即![](http://images.iterate.site/blog/image/180728/macJe7IHKE.png?imageslim)，因此![](http://images.iterate.site/blog/image/180728/5Kc018KG5g.png?imageslim){ width=55% }这个里面的 d 就省略的。



所以可见有了贝叶斯网络的基本的东西之后

正常而言，还是用极大似然估计

![](http://images.iterate.site/blog/image/180728/94macfFgC6.png?imageslim){ width=55% }

这个地方解释下，主题有 K 个，词有 M 个，文档有 N 个

可见，这是一个关于 w z d 的部分可观测的目标函数，因此使用的最正的办法就是 EM 算法：

目标函数分析：


![](http://images.iterate.site/blog/image/180728/b49Cb50KEa.png?imageslim){ width=55% }

OK，我们先做第一步：写隐变量主题\(z_k\) 的后验概率:


![](http://images.iterate.site/blog/image/180728/9fi6l32ha6.png?imageslim){ width=55% }


![](http://images.iterate.site/blog/image/180728/9AibdfJd68.png?imageslim){ width=55% }这个式子，把 d 去掉就能看明白了，然后在把 d 加上就可以理解了。，所以上面。这个就是给定样本之下的隐变量的后验概率





## 然后，我们看看这个怎么求期望的极大值：




![](http://images.iterate.site/blog/image/180728/56BBE37mBA.png?imageslim){ width=55% }

看看怎么求的：


![](http://images.iterate.site/blog/image/180728/L19keeLgFa.png?imageslim){ width=55% }

写开之后，后面的部分 \(P(d_i)\) 是文档的概率，\(n(d_i,w_j)\) 是联合起来的数出的个数，也就是说后面的部分是常数，因此只看前面的部分。l_{new}

对\(l_{new}\)求期望之后，\(\sum_{k=1}^{K}P(z_k\mid d_i,w_j)\) 就是在给定 d w 的情况下的主题的条件概率，就是推 EM 算法的时候，里面的 Qi。没明白？

这么一个式子看着很长，其实是一个带有约束的求极值问题。


## 完成目标函数的建立：


![](http://images.iterate.site/blog/image/180728/hCFKb89ld3.png?imageslim){ width=55% }

进行求解：


![](http://images.iterate.site/blog/image/180728/ikGjLGAI0i.png?imageslim){ width=55% }

注意，我们这里求导的时候只关心第 k 个主题，第 j 个词，所以很多的求和符号就都没有了，因为 i 是文档的，所以要保留

分析第一个等式得到：


![](http://images.iterate.site/blog/image/180728/CB2kH0lJlA.png?imageslim){ width=55% }

这个地方的![](http://images.iterate.site/blog/image/180728/dl3kAFel7j.png?imageslim)带入到上面的![](http://images.iterate.site/blog/image/180728/jb3m7DL02H.png?imageslim)里面，就得到了：![](http://images.iterate.site/blog/image/180728/I3mH6a8K8F.png?imageslim){ width=55% }。

注意这里的![](http://images.iterate.site/blog/image/180728/35DJh1cL3G.png?imageslim){ width=55% }下面不是 m 而是 j。

利用这个等于 0 得到主题给定的情况下，词的分布。

同理分析第二个等式


![](http://images.iterate.site/blog/image/180728/kAE0j5i0j9.png?imageslim){ width=55% }

上面这两个就是 M 过程。

而![](http://images.iterate.site/blog/image/180728/g4mKHag40l.png?imageslim){ width=55% }
就是刚刚在![](http://images.iterate.site/blog/image/180728/1HI7dm9L00.png?imageslim){ width=55% }
这里算出来的，所以带入即可。

![](http://images.iterate.site/blog/image/180728/d8Iii37JDa.png?imageslim){ width=55% }
![](http://images.iterate.site/blog/image/180728/BmCLmCJajI.png?imageslim){ width=55% }

这个是给定的样本之下 z_k这个主题出现的概率。


![](http://images.iterate.site/blog/image/180728/CC40a5ge7g.png?imageslim){ width=55% }这个就是看一下 $d_i$ $w_j$ 出现的次数。




上面就是 pLSA 的全部的推导过程，还是要自己推一下。再理解一下。




# pLSA的总结


pLSA应用于信息检索、过滤、自然语言处理等领域，pLSA考虑到词分布和主题分布，使用 EM 算法来学习参数。

虽然推导略显复杂，但最终公式简洁清晰，很符合直观理解，需用心琢磨；此外，推导过程使用了 EM 算法，也是学习 EM 算法的重要素材。

实际上 pLSA 是 10 年前的产物了，而现在已经全部转向 LDA 了。



OK，对于 pLSA 进一步思索：


# pLSA进一步思考：

![](http://images.iterate.site/blog/image/180728/4LiHcc2E4i.png?imageslim){ width=55% }

是的，是不需要先验信息，只需要数出

![](http://images.iterate.site/blog/image/180728/i7e392ejEH.png?imageslim){ width=55% }

就行了，其它的全都是假设的，然后迭代出来的。

这个是它的优势，但是也是它的劣势，因为没办法人工干预他。

而对它的进行的改进，就是这个著名的 LDA。


# COMMENT
