---
title: 32 条件随机场 CRF
toc: true
date: 2018-08-21 18:16:23
---
# 条件随机场 CRF


## 可以补充进来的

- 有人搜了下： Conditional Random Fields as Recurrent Neural Networks  确认下，CRF是不是可以与 rnn 关联？
- 视频要重新看一下啊，有很多的推理没有弄明白。



对条件随机场进行总结


## 前置知识

- 贝叶斯网络
- 马尔科夫毯
- Logistic 回归
- 几率


# CRF介绍

Conditional random field  CRF

# 贝叶斯网络与 CRF 有什么关系？


贝叶斯网络能够做的事情是集合 A，条件随机场能够做的事情是集合 B，那么 A 和 B 是有交集的，也有各自的东西。有各自的优点和缺点。

我们先从一个基本的例子来介绍下，为什么贝叶斯网络最后发展成了条件随机场：

假设这是一幅图像，每个 X 就是一个像素点，假定每个像素只与他旁边的像素有关，与斜对角的无关，因此可以建立一个二维的贝叶斯网络，如下图左侧所示。OK，然后我们考察一下 X8 对应的马尔科夫毯（Markov blanket），如下图左侧部分的画圈的几个：


![](http://images.iterate.site/blog/image/180728/bdG8fa6GiL.png?imageslim){ width=55% }

![](http://images.iterate.site/blog/image/180728/k14ICCIG0i.png?imageslim){ width=55% }

OK，我们把 X8 对应的马尔科夫毯的关键的部分拿出来看，如左图所示，它包括 x4,x12但是不包括 x2,x14。这样的话感觉看起来就不大对称，比如如果做旋转或者别的操作，感觉这里面得出的结论就会产生变化。**有严格的说法吗？**

也就是说，这个网络本身实际上存在一些问题。

因此如果把网络中的箭头都去掉，并且假定一个结点只和它的相邻结点产生关系，那么上面的右图这个就感觉比较合理。

而这，就是有些问题使用无向图而不是有向图的原因。**感觉还需要补充，有些不严谨吧？**

OK，现在我们从另外一个角度观察这个事情：

网络模型比较 HMM/MEMM/CRF/RVM：

![](http://images.iterate.site/blog/image/180728/eB3jmhE47F.png?imageslim){ width=55% }

对于左上角的是 HMM 模型，如果我们反着理解，因为 y 和 x 谁是因谁是果其实是说不清的，看起来只是有联系而已，是的，甚至有可能它们之间不是直接相关的，只是有一定的条件非独立性 ，嗯 是呀。利害的。所以 y 到 x 的箭头实际上是可以倒过来的，x 是原因， y 是结果，而且假设 y 还有一个全局状态 $x_g$ 对它进行影响。那么我们就写成了右上角的网络。

这个右上角的网络其实就是最大熵模型的马尔科夫网络，**为什么呢？**




右上角的图中如果 y_{t-1}已经计算确定了，那么 $x_{t-1}$ 与 $y_t$ 就没有关系了，


把这个模型中的所有箭头都去掉就变成了左下角的这个网络。也就是条件随机场。

而右下角的图是相对向量机 RVM 这个没有提。**PRML里面有提到。 是一个 SVM 的改进，SVM大概是从 90 年代末到 07 年左右，在深度学习火起来之前就是 SVM，看一下 RVM。**



可见，贝叶斯网络是一个很庞大的体系，有专门的书讲贝叶斯网络的。或者概率图模型。

所有的上面这些都可以看作是 pgm，概率图模型。





有向的图又名贝叶斯网络。无向的图又名马尔科夫网络。对马尔科夫网络进行有条件的去做，就是条件随机场。


# 条件随机场的用处


条件随机场的用处非常大，只要是与分类有关的，都可以用条件随机场。

比如说词性标注问题：


![](http://images.iterate.site/blog/image/180728/f0fJHBefki.png?imageslim){ width=55% }

每个词对它标注词性，这是一个分类问题。

还有中文分词，可以使用 HMM，但是 CRF 也是绝对可以的，现在最好的中文分词的手段就是条件随机场，并且它可以发现新词。在分词上，比较主流的看法是 HMM 和 CRF 都是好的分词手段，但是 CRF 寄予的希望更大。



OK，我们从 Logistic 回归谈起：

在回归那一张，我们讲到了 Logistic 回归和几率。并且我们知道：

* Logistic回归是一种广义的线性回归
* 几率是一个对数线性模型。

![](http://images.iterate.site/blog/image/180728/GKbaCk3Eg6.png?imageslim){ width=55% }

![](http://images.iterate.site/blog/image/180728/diGab2eKif.png?imageslim){ width=55% }

因此，我们对刚才的模型做一个理论层面的提升：

![](http://images.iterate.site/blog/image/180728/9H45mbCFAj.png?imageslim){ width=55% }

Logistic回归的特征 $x_1,x_2,\cdots ,x_n)$ 是 $\theta_1x1$ 和\theta_2x2 和\theta_nxn ，其实 Logistic 回归里面的特征是样本的所有的维度各自作为一个特称然后做一个加权的乘积，把它加起来。

对于 Logistic 回归来说：   \(\frac{1}{1+e^{-\theta^Tx} }\) 上下同时乘以 \(e^{\theta^Tx}\)  那么下面就可以看作是一个归一化因子，上面可以看作是权值 w 与特征 x 的乘积，即一般形式为：![](http://images.iterate.site/blog/image/180728/h5fe2AH0Ej.png?imageslim){ width=55% } w就是权值 F 就是与权值相关的特征

而这个里面的 Z 是可以表达出来的， 两边同时对 y 求加和， 左边对所有的 y 求加和是 1 ，右边的话把 Z(x,w)放到左边，就是：![](http://images.iterate.site/blog/image/180728/m6i43jhJd3.png?imageslim){ width=55% }



所以，我们本质上做的什么事情呢？

我们拿到手了 x，并且训练好了 w 之后，看一下在这个 x 之上产生某一个标记 y 的概率。

把所有的 y 都遍历一遍

![](http://images.iterate.site/blog/image/180728/fH7gfAJdBc.png?imageslim){ width=55% }

那个值是最大的，把那个东西取出来作为我们预测的标记。

特征函数的选择：

我们看一下特征函数 $F_j(x,y)$ 可以选什么？如果我们直接去选它的各个维度，即还是原来的 x_j 那么这个模型就是 Logistic 回归。


## 如果选择别的特征呢？


![](http://images.iterate.site/blog/image/180728/4D73j7L0gg.png?imageslim){ width=55% }

所以会有好多的特征。

比如词性标注：

![](http://images.iterate.site/blog/image/180728/F21jl8k9Ca.png?imageslim){ width=55% }

词性 POS

利害，这个多特征计算的时候会不会很慢？

能想到的特征都可以加进去，不用害怕特征是不是不合理，因为有权重 w 来调节。只要有这个特征，他做了就可以了。

今天用到的做特征的情况，要么这个特征只与当前词性有关，或者与相邻的词性有关。什么意思？


![](http://images.iterate.site/blog/image/180728/LG2KjaCI3b.png?imageslim){ width=55% }中的 y 是我们想要做的词性，x是我们观察到的词，因此 y_t这个词性只与相邻的词性有关。但是他可以与所有的词产生关系。什么意思？

之前提到说 HMM 和 CRF 那个号，因为 CRF 可以做特征，所以他有很大的发展潜力。

只要是有好的手段来选择特征，那么我就能让这个模型更好。

所以现在其实就产生了这么一个：


![](http://images.iterate.site/blog/image/180728/gAKmBK8e90.png?imageslim){ width=55% }

x是观测到的，词性是相邻的差生关系的，这个模型就是条件随机场。

之所以说是结构化预测，是因为，比如说对于 Logistic 回归而言：


![](http://images.iterate.site/blog/image/180728/g4ECLGH2K4.png?imageslim){ width=55% }中的某个样本被分为正例还是负例可以看作是独立的，但是对于词性标注而言不是这样子，前一个词的词性会极大的影响到后一个词的词性。  是呀。


可见他与之前的对某个样本的进行分类是不一样的。

句子的长度有长有短，因此不方便将所有的句子统一乘同长度向量。



比如我们之前做朴素贝叶斯的时候，其实我们是吧文章映射到 V 维的，V是词典的维数，这个代价是很高的，如果用这个来做词性标注，那么就很难办了，因此句子的长度不一样是要解决的。

假定有 a 个词性，Y1就有 a 中可能，Y2.。。。Yn也都有 a 种可能，因此标注的阶级与句子长度是指数级增长的，因此穷举计算几乎不实用的。



因此词性标注就有这三个问题需要解决，

而条件随机场其实是可以解决的，这个问题不是 NP？ 的，可以把它降到 P。什么是 NP 什么是 P


![](http://images.iterate.site/blog/image/180728/l1JbKK63De.png?imageslim){ width=55% }

Y是链状的。

X是给定的样本集合

\(F_j(\overline{x},\overline{y})\) 这个特征可能非常大，10万个都是有可能的。这个特征是我们可以自己手动做出来的，或者通过深度学习做出来。也就是说这个相当于是已知的。

因此只有 w 是未知的。



因此，可以对应上 HMM 里面的三个问题：


1. 给你 w，如何算概率 p
2. 给你样本，如何算哪一个 w 是最好的。 这个是学习问题
3. 给你 w，给你 x，如何算哪一个 y 是最好的。 这个是预测问题


几乎任何一个模型拿到手，尤其是跟概率有关的，都这个干。


![](http://images.iterate.site/blog/image/180728/GKJbjFAhfC.png?imageslim){ width=55% }你可以把分好写成逗号，写成逗号意味着，x和 w 是条件，y是预测的值，如果你认为 w 是条件，那么你就是一个贝叶斯学派的人，因为你认为 w 是条件。


如果你写成分号，就意味着这个 y 和 x 是有关的，w是系统中存在的某一个值，不管 w 知道不知道，但是我知道 w 是有关的，我把分号把 w 暴露出来而已。这个是频率学派的想法。

这两种写法其实都对。



现在已经有了特征，其实这个特征\(F_j(\overline{x},\overline{y})\)是比较粗的。它是指的整个句子本身的特征，它跟 X 有关，跟 Y 有关，我们看下能不能写开：


![](http://images.iterate.site/blog/image/180728/8KcJdfab4L.png?imageslim){ width=55% }



![](http://images.iterate.site/blog/image/180728/Dacjih5l4e.png?imageslim){ width=55% }
![](http://images.iterate.site/blog/image/180728/IkHH70C9m7.png?imageslim){ width=55% }这个所有的观测的样本还是放在这里，j是第几号特征 i是指这个句子的第几个词，y_i指的是句子的第几个词的词性


**为什么与后面的一个词没有关系？不是说相邻吗？**

之所以每写后面的邻居，因为 j+1的时候，就相当于 j 的时候的 i 和 i+1了。

因为是无向图，所以只要有一方产生联系就行。

**没明白，什么叫句子的第 j 个特征？**

这样做了之后，我们发现![](http://images.iterate.site/blog/image/180728/iiHa07D277.png?imageslim){ width=55% }这个已经解决了训练样本不等长的问题了，即句子不等长的问题。为什么？



OK，我们看下 w 如何计算：

![](http://images.iterate.site/blog/image/180728/BecA1i86eJ.png?imageslim){ width=55% }

这个求法涉及到两个难点：

给定了 w，能不能告诉我那个 y 是最优的？


![](http://images.iterate.site/blog/image/180728/LF4eejff03.png?imageslim){ width=55% }

这个


![](http://images.iterate.site/blog/image/180728/G9FmIgKAGb.png?imageslim){ width=55% }

是对所有可能的 y 做加和。

![](http://images.iterate.site/blog/image/180728/2alA69mj4j.png?imageslim){ width=55% }


![](http://images.iterate.site/blog/image/180728/5kE07glAb6.png?imageslim){ width=55% }

我让权值直接作用于次特征。



对于 y_{i-1}来说有 a 种情况，对于 y_i来说有 a 种情况，那么这个 g 就是 a*a阶的矩阵



现在我们给出一个类似于 HMM 里面的前向概率


![](http://images.iterate.site/blog/image/180728/1KBBmI14m1.png?imageslim){ width=55% }



![](http://images.iterate.site/blog/image/180728/Df0h9e6kId.png?imageslim){ width=55% }应该是叫做前向得分，因为这个时候还没有除以 Z，除以 Z 之后就是概率了。




第 k 个词被标记为 V 的概率。

前面的 i 从 1 到 k-1 的这些正常去做就行，但是要求第 k 个一定是 v。那么有 k-1种情况，因此遍历，找出 max。、

实际上，假如说第 k-1个词 标记成 y_{k-1}，然后第 k 个词标记成 v，由于


![](http://images.iterate.site/blog/image/180728/He1G4f35j4.png?imageslim){ width=55% }

这个由上个式子是可以算出来的，因此假定他有 a 种情况，都进行遍历，看看这个 y_{k-1} 的哪一种词性使得 k 标记成 v 的概率最大。



因此它满足这个递推公式：


![](http://images.iterate.site/blog/image/180728/L1fECjfHIm.png?imageslim){ width=55% }


假定标记数目有 m 个，那么


![](http://images.iterate.site/blog/image/180728/eGEFalAALC.png?imageslim){ width=55% }

就要做 m 次，而 v 可以取 m 个，因此

![](http://images.iterate.site/blog/image/180728/CCCHDd595F.png?imageslim){ width=55% }

要算 m 回，因此这个有 m^2次训练，假定单词的数目是 n 个，因此时间复杂度是


![](http://images.iterate.site/blog/image/180728/9a5hK5lbam.png?imageslim){ width=55% }



如果 m 比较小，那么虽然 m^2=100，但是它对于 n 来说仍然是线性的，即对于句子的单词数仍然是线性的。



这个就解决了第一个问题，即那个标记序列 y 的概率最大的问题。







OK 我们来解决第二个问题：

我们想给定 w 和 x 之后 y 的概率是多大？

![](http://images.iterate.site/blog/image/180728/g2lKj0Dj2b.png?imageslim){ width=55% }

对

![](http://images.iterate.site/blog/image/180728/4CmLFe320h.png?imageslim){ width=55% }

两边全部做对 i 的加和，那么就可以替换

![](http://images.iterate.site/blog/image/180728/ckfE0j5Gmi.png?imageslim){ width=55% }

把指数拿进来，所以加和就变成了乘积



![](http://images.iterate.site/blog/image/180728/934HH8790G.png?imageslim){ width=55% }

我们把


![](http://images.iterate.site/blog/image/180728/GlFHd1GB1G.png?imageslim){ width=55% }

即


![](http://images.iterate.site/blog/image/180728/ED2C8acGc0.png?imageslim){ width=55% }

 ，然后求指数做成一个矩阵。



然后我们任选一个起始状态，stop也是强制给的一个状态。



从 start 变道 q，然后从 q 变到 v。


![](http://images.iterate.site/blog/image/180728/HcCHGEa5D7.png?imageslim){ width=55% }

从 start 到 v 的归一化因子。

第一个矩阵是 a*b  第二个矩阵是 b*c 那么计算的次数是 a*b*c 如果想要更快的话用分治法，把它降到 m^2.81，而不是 m^3



这里直接取 m^3.



因此本来要求的 z 是 m^n 单线现在已经降到了 m^3.



因此这个第二个难题也解决了。没明白。






![](http://images.iterate.site/blog/image/180728/E054Fb9e1H.png?imageslim){ width=55% }

因为想用它的梯度下降算法，因此要求梯度。



我们这个东西求驻点


![](http://images.iterate.site/blog/image/180728/Im9Em5GgeC.png?imageslim){ width=55% }

![](http://images.iterate.site/blog/image/180728/e8cIIcE121.png?imageslim){ width=55% }

这个是当前的第 j 号的情况


![](http://images.iterate.site/blog/image/180728/EKKGbFFCdi.png?imageslim){ width=55% }

这个是所有的归化因子，



所以这个东西就是在当前的攒书 w 的时候给定的 x 预测的 y。

![](http://images.iterate.site/blog/image/180728/cceJE0c8h4.png?imageslim){ width=55% }

而

![](http://images.iterate.site/blog/image/180728/0c43jg64mg.png?imageslim){ width=55% }

是特征，因此

![](http://images.iterate.site/blog/image/180728/Hf7l865D1i.png?imageslim){ width=55% }

这个就是当前的 w 值，可以对它利用这个概率值求它的特征的期望，然后再看一下第 j 个特征到底是几。


![](http://images.iterate.site/blog/image/180728/aEJd1376B8.png?imageslim){ width=55% }

这个只是一个记号。

因此这个就是我们的梯度，因此使用梯度上升方法进行训练就行。

到这一步为止，我们已经解决了上面的左右所有的问题。

所以这些就是条件随机场的最核心的所有的内容。

![](http://images.iterate.site/blog/image/180728/977FbJIHAI.png?imageslim){ width=55% }

主题模型的 LDA 和 HMM 都是生成模型，因为我们的最终目的都是给定一个 x 然后看看 y 的标记是什么，但是在 HMM 种我们认为 y 是不可观测的。


![](http://images.iterate.site/blog/image/180728/G8Bahm0Kag.png?imageslim){ width=55% }

按道理，看到了 x 是想推测这个 y，但是在 HMM 种，我们认为看到的这个 x 是生成的东西，是被隐变量 y 产生得到的，箭头是从 y 指向 x 的，所以这是一个生成模型。



反过来我们想直接判断，比如给定了 x 它是 y 的概率有多大，它是直接进行判断的，因此是一个判别模型。



因此，只要看一下 x 和 y 谁生成谁，就可以知道了。


OK 到这里为止，做事情已经足够了。


# 下面介绍一些理论问题：


- 无向图模型
    - 马尔科夫随机场
- 团、最大团
- Hammersley-Clifford 定理



## 无向图模型


有向图模型，又称作贝叶斯网络(Directed GraphicalModels, DGM, Bayesian Network)。事实上，在有些情况下，强制对某些结点之间的边增加方向是不合适的。

使用没有方向的无向边，形成了无向图模型(Undirected Graphical Model,UGM), 又称马尔科夫随机场或马尔科夫网络(Markov Random Field, MRFor Markov network)

注：概率有向图模型/概率无向图模型

注意缩写：BN 是 BayesNetwork 贝叶斯网络，NB 是 Naive Bayes 朴素贝叶斯。


## 条件随机场


设\(X=(X_1 ,X_2 …X_n )\)和\(Y=(Y_1 ,Y_2 …Y_m )\)都是联合随机变量，若随机变量 Y 构成一个无向图 G=(V,E)表示的马尔科夫随机场(MRF)，则条件概率分布 P(Y|X)称为条件随机场(Conditional Random Field, CRF)




  * X称为输入变量、观测序列


  * Y称为输出序列、标记序列、状态序列


  * 大量文献将 MRF 和 CRF 混用，包括经典著作。


  * 一般而言，MRF是关于隐变量(状态变量、标记变量)的图模型，而给定观测变量后考察隐变量的条件概率，即为 CRF。


  * 但这种混用，类似较真总理和周恩来的区别。


    * 有时候没必要区分的那么严格


    * 混用的原因：在计算 P(Y|X)时需要将 X 也纳入 MRF 中一起考虑





有些文献里面把条件随机场和马尔科夫随机场是混用的






## 从有向图到无向图如何去做：


按照比较约定俗成的方案是这样做的：

DGM转换维 UGM

![](http://images.iterate.site/blog/image/180728/km3e3gGD5e.png?imageslim){ width=55% }


1. 第一步：只要是它们有共同孩子的，都连起来。比如 UW。
2. 第二步：把所有的原始的箭头都去掉。


这就是从贝叶斯网络到马尔客服随机场的

其实这个信息量是有变化的，但是一般是这样做的，**为什么是有变化的？**

DGM转换为 UGM


![](http://images.iterate.site/blog/image/180728/2B5eGAD2dF.png?imageslim){ width=55% }

比如上面这个图，如果给定了 2，那么 4 和 5 是独立的。

而在下面这个：给定了 2，的时候 4和 5 有条边，这个边的影响就使信息发生了变化。

这种信息变化的程度可能使最少的，因此平时都这么做，

但是一旦这么做了，信息的独立性已经发生了变化。


## 条件独立的破化


靠考察是否有\(A\perp B|C\) ，则计算 U 的祖先图(ancestral graph)：\(U=A\cup B\cup C\)


![](http://images.iterate.site/blog/image/180728/JEiF9LH7I5.png?imageslim){ width=55% }



![](http://images.iterate.site/blog/image/180728/1kkLl1AkBl.png?imageslim){ width=55% }

什么情况下需要做 DGM-UGM的变换？

没有说一定要什么时候去做。比如中文分词，可以直接使用条件随机场，没有必要先做一个贝叶斯网络再换成条件随机场。没有这个必要，只是从理论上说这个事情的时候说一下。








# MRF的性质

相对于贝叶斯网络而言，马尔科夫随机场也有三个性质：

  * 成对马尔科夫性 parewise Markov property
  * 局部马尔科夫性 local Markov property
  * 全局马尔科夫性 global Markov property

![](http://images.iterate.site/blog/image/180728/agm0C6lAEC.png?imageslim){ width=55% }

记号：随机变量\(Y=(Y_1 ,Y_2 …Y_m )\)构成无向图\(G=(V,E)\)，结点(集)v对应的(联合)随机变量是\(Y_v) 。

比如 1和 7 这两个结点使一对，如果给定所有结点的时候它们的边使相连的

如果给定其它所有结点的时候，1和 7 使独立的。

要想让 1 和其它所有的都是独立的，那么把它相邻的拿出来，那么 1 和其它的就独立了。

我可以给定 3.4.5这个集合，那么就回分差鞥两个节点集合。而这两个结点集合是独立的。这个就是全局马尔科夫性。


## 成对马尔科夫性


设 u 和 v 是无向图 G 中任意两个没有边直接连接的结点，G中其他结点的集合记做 O；则
在给定随机变量 Yo 的条件下，随机变量 Yu 和 Yv 条件独立。


![](http://images.iterate.site/blog/image/180728/K8gJbI3Ih5.png?imageslim){ width=55% }




## 局部马尔科夫性


设 v 是无向图 G 中任意一个结点，W是与 v 有边相连的所有结点，G中其他结点记做 O；则在给定随机变量 Yw 的条件下，随机变量 Yv 和 Yo 条件独立。


![](http://images.iterate.site/blog/image/180728/aA4e1mAba5.png?imageslim){ width=55% }




## 全局马尔科夫性


设结点集合 A，B是在无向图 G 中被结点集合 C 分开的任意结点集合，则在给定随机变量\(Y_C\) 的条件下，随机变量\(Y_A\) 和\(Y_B\)  条件独立。

即\(P(Y_A , Y_B |Y_C )= P(Y_A | Y_C )* P(Y_B | Y_C )\)


![](http://images.iterate.site/blog/image/180728/fa29accJ9d.png?imageslim){ width=55% }




## 三个性质的等价性

![](http://images.iterate.site/blog/image/180728/34BKAg8aBK.png?imageslim){ width=55% }





  * 根据全局马尔科夫性，能够得到局部马尔科夫性；


  * 根据局部马尔科夫性，能够得到成对马尔科夫性；


  * 根据成对马尔科夫性，能够得到全局马尔科夫性；


事实上，这个性质对 MRF 具有决定性作用：满足这三个性质(或其一)的无向图，称为 MRF。



HMM做了一个很大的假设：当前状态只和前一个状态有关。

而且当前的观测只与之前的隐状态有关。

如果这个假定是对的，那么 OK，但是如果不符合就不行。

但是 CRF 没有这个假设，即假设的前提比较少，以后的发展就比较有前途，但是算的也比较慢



贝叶斯网络与条件随机场

在研究贝叶斯网络的过程中，重点考察了 LDA 模型、HMM模型，使用 Markov 模型进行了 MCMC；

在条件随机场的研究学习中，将重点考察线性链 MRF(LC-CRF,Linear Chain Conditional Random Field)


## 团和最大团


无向图 G 中的某个子图 S，若 S 中任何两个结点均有边，则 S 称作 G 的团(Clique)。

若 C 是 G 的一个团，并且不能再加入任何一个 G 的结点使其称为团，则 C 称作 G 的最大团(Maximal Clique)。

团：{1,2}, {1,3}, {2,3}, {2,4},{3,4}, {3,5},{1,2,3}, {2,3,4}

最大团：{1,2,3}, {2,3,4}, {3,5}


![](http://images.iterate.site/blog/image/180728/IE476D818d.png?imageslim){ width=55% }

团这个概念有什么作用呢？



为什么要提出这么一个概念呢？因为我们想提出这么一个定理：

Hammersley-Clifford定理

UGM的联合分布可以表示成最大团上的随机变量的函数的乘积的形式；这个操作叫做 UGM 的因子分解(Factorization)。


![](http://images.iterate.site/blog/image/180728/982eE4Dmmj.png?imageslim){ width=55% }

![](http://images.iterate.site/blog/image/180728/d4h0c2H0bk.png?imageslim){ width=55% }

条件随机场的结点的联合概率，可以认为是 123 这个最大团和 234 这个最大团和 35 这个最大团的乘积

这个函数 是叫势函数？还是我听错了，要确认下 就是 \Phi


![](http://images.iterate.site/blog/image/180728/F3hBF2ib2J.png?imageslim){ width=55% }

把它所有最大团上的势函数的乘积然后除以归一化因子就形成了 y 这个概率分布。

如果我把势函数写成指数线性的或者对数线性的，其实就是我们的对数线性模型。

线性链条件随机场


![](http://images.iterate.site/blog/image/180728/hHHHdalH05.png?imageslim){ width=55% }

线性链条件随机场


![](http://images.iterate.site/blog/image/180728/LmAiDh9LL4.png?imageslim){ width=55% }

Y是马尔科夫场 X是条件，对于这个图来说，它的最大团就是：

Y1Y2 一个函数  Y2Y3 一个函数.。等等

注意：X是条件，不属于随机场，所以它的最大团跟 X 没有关系。

这个东西就是我们之前将的次特征 这个函数



线性链条件随机场的定义


![](http://images.iterate.site/blog/image/180728/eefCm1haD8.png?imageslim){ width=55% }

线性链条件随机场的参数化形式


![](http://images.iterate.site/blog/image/180728/5JHGeb6B1k.png?imageslim){ width=55% }

参数说明


![](http://images.iterate.site/blog/image/180728/gKEag9gG8G.png?imageslim){ width=55% }

### 条件随机场举例

![](http://images.iterate.site/blog/image/180728/eCfhC8dA32.png?imageslim){ width=55% }

NN、NNS、NNP、NNPS、PRP、DT、JJ分别代表普通名词单数形式、普通名词复数形式、专有名词单数形式、专有名词复数形式、代词、限定词、形容词

![](http://images.iterate.site/blog/image/180728/216d3e4Hh7.png?imageslim){ width=55% }

条件随机场举例

![](http://images.iterate.site/blog/image/180728/2ff04i5lI2.png?imageslim){ width=55% }

条件随机场举例




  * t1=t1(y1=1,y2=2,x,2) λ 1 =1


  * t1=t1(y2=1,y3=1,x,3) λ 1 =1


  *  t2=t2(y1=1,y2=1,x,2) λ 2 =0.5


  * t3=t3(y2=2,y3=1,x,3) λ 3 =1


  * t4=t4(y1=2,y2=1,x,2) λ 4 =1


  * t5=t5(y2=2,y3=2,x,3) λ 5 =0.2


  * s1=s1(y1=1,x,1) μ l =1


  * s2=s2(y2=2,x,i) μ2=0.5


  * s3=s3(y1=1,x,i) μ 3 =0.8


  * s4=s4(y3=2,x,i) μ 4 =0.5


则标记序列为 y=(1,2,2)的非规范化概率为：


![](http://images.iterate.site/blog/image/180728/Ggd0076jDk.png?imageslim){ width=55% }

使用统一的函数记号表达特征


![](http://images.iterate.site/blog/image/180728/0LigdG42lj.png?imageslim){ width=55% }

CRF的简化形式


![](http://images.iterate.site/blog/image/180728/faG8eEfb28.png?imageslim){ width=55% }

![](http://images.iterate.site/blog/image/180728/AiAfmmLe1f.png?imageslim){ width=55% }

CRF 的矩阵形式


![](http://images.iterate.site/blog/image/180728/0BalEd06DB.png?imageslim){ width=55% }

CRF 的矩阵乘积和条件概率


![](http://images.iterate.site/blog/image/180728/DgE3Dg6dfB.png?imageslim){ width=55% }

CRF的三个问题

  * CRF的概率计算问题
    * 前向后向算法

  * CRF的参数学习问题


    * IIS：改进的迭代尺度算法





  * CRF的预测算法


    * Viterbi算法





CRF的概率计算问题

给定条件随机场\(P(Y|X)\)，输入序列 x 和输出
序列 y，计算：


  * 条件概率\(P(Y_i =y_i |x)\)


  * 条件联合概率\(P(Y_i-1 =y_i-1 , Y_i =y_i |x)\)


  * 手段：前向后向算法


前向向量：


![](http://images.iterate.site/blog/image/180728/5eE14Gh4ei.png?imageslim){ width=55% }

后向向量


![](http://images.iterate.site/blog/image/180728/8G0FJahc05.png?imageslim){ width=55% }

归一化因子

![](http://images.iterate.site/blog/image/180728/BEA3B79AK1.png?imageslim){ width=55% }

概率计算


![](http://images.iterate.site/blog/image/180728/BlhBleCcEf.png?imageslim){ width=55% }

CRF的参数学习问题

![](http://images.iterate.site/blog/image/180728/iG2jk4EJc7.png?imageslim){ width=55% }

改进的迭代尺度算法


![](http://images.iterate.site/blog/image/180728/fifhlDdHCd.png?imageslim){ width=55% }

变化率δ的函数

![](http://images.iterate.site/blog/image/180728/05c6Kc5G5I.png?imageslim){ width=55% }

参数学习：改进的迭代尺度算法 IIS


![](http://images.iterate.site/blog/image/180728/EHGIm96Ekg.png?imageslim){ width=55% }


### CRF的预测算法


CRF的预测问题，是给定条件随机场 P(Y|X)和输入序列(观测序列)x，求条件概率最大的输出序列(标记序列)y*，即对观测序列进行标注。

* Viterbi算法

最优路径的目标函数


![](http://images.iterate.site/blog/image/180728/CCBBb0gKba.png?imageslim){ width=55% }

最优路径的目标函数


![](http://images.iterate.site/blog/image/180728/1CE2E7lf3i.png?imageslim){ width=55% }



![](http://images.iterate.site/blog/image/180728/9F1ma3i9dg.png?imageslim){ width=55% }

状态预测：Viterbi算法


![](http://images.iterate.site/blog/image/180728/FL333K4Ha8.png?imageslim){ width=55% }




### CRF总结


条件随机场是给定输入的条件下，关于输出的条件概率分布模型，根据 Hammersley-Clifford定理，可以分解成若干关于最大团的非负函数的乘积，因此，常常将其表示为参数化的对数线性模型。

线性链条件随机场使用对数线性模型，关注无向图边的转移特征和点的状态特征，并对每个特征函数给出各自的权值。




  * 概率计算常常使用前向-后向算法；


  * 参数学习使用 MLE 建立目标函数，采用 IIS 做参数优化；


  * 线性链条件随机场的应用是标注/分类，在给定参数和观测序列(样本)的前提下，使用 Viterbi 算法进行标记的预测。






我们一直所说的次特征，如果一直是 y_{i-1} 和 y_i之间的关系的额时候，可以看成是 HMM 里面的状态转移概率。

只有 y_i时候的次特征，可以看作是它的发射概率

虽然我们没有说如何转移如何发射，但是特征已经隐含这些东西了。

我们之前讲的是概率计算的前向算法，我们用后向一样可以做。



我们的标记序列 Y 要求是链状的，但是 X 没有要求，比如可以是一维的词性标注，一维的音频数据，一维的总问分词，也可以是离散数据，比如用户的手机号，或者是二维的图像数据。

我不要求 X 是什么，所以条件随机场这个模型对数据的要求是非常低的，它仅仅是假定了为止的标记可以和前一个标志有关，可以和所有的 X 有关。

它的缺点是：要把所有的 Y 都算完才能算出归一化因子 Z。



比如十万的参数，HMM 模型运行几个小时就可以进行分词，但是用 CRF 需要一两天。

慢的程度与深度学习差不多了。







## 需要消化的：

下面几篇可以看下：

- [https://www.zhihu.com/question/35866596](https://www.zhihu.com/question/35866596)
- [http://www.cnblogs.com/Determined22/p/6915730.html](http://www.cnblogs.com/Determined22/p/6915730.html)
- [https://blog.csdn.net/u014688145/article/details/58055750](https://blog.csdn.net/u014688145/article/details/58055750)




# 相关

- 七月在线 机器学习
- [NLP —— 图模型（二）条件随机场（Conditional random field，CRF）](http://www.cnblogs.com/Determined22/p/6915730.html)
