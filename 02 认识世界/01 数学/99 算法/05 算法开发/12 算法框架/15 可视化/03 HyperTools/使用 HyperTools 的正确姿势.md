---
title: 使用 HyperTools 的正确姿势
toc: true
date: 2019-11-17
---
# 使用 HyperTools 的正确姿势


Kaggle 推出了基于 Python 的高维数据降维以及可视化处理工具 HyperTools，并将其作为 Kaggle Kernels 的一部分免费提供给开发者

*日前，Kaggle 在博客公布了使用 HyperTools 的官方教程。其中包含两个例子：用 HyperTools 对蘑菇数据做可视化，以及对全球气象数据做可视化。示例包含代码，需要做数据降维可视化的童鞋，这是一篇不错的 HyperTools 上手教程。*




数据科学家、分析师处理的数据集，正在变得越来越复杂。

机器学习的很大一部分专注于从复杂数据中抽取涵义。但是，这一过程中人类仍然扮演很重要的角色。人类的视觉系统非常善于检测复杂结构，并发现海量数据中的微妙模式。人眼睁开的每一秒钟，都有无数的数据点（到达视网膜的光线图形）蜂拥至大脑的视觉区域。对于识别沙滩上的一枚完整贝壳，或是人群中朋友的脸，人脑能轻松完成。这一点其实十分了不起。我们的大脑是无监督模式发现的“狂人”。

另一方面，依赖于我们的视觉系统了来提取信息，有至少一个主要缺陷：至多只能同时感知三个维度。而今天的数据集，有很多的维度比这要高得多。

现在数据科学家普遍面临的问题是：

> 如何驾驭人脑的模式识别超能力，实现复杂、高维数据集的可视化？

##   如何降维？

如同其名，降维是指把高维数据集转化为低维数据集。比如说，把 Kaggle 上针对蘑菇的 UCI ML 数据集组织为矩阵。每一行都包含一系列蘑菇的特征，比如菌盖大小、形状、颜色、气味等等。对这做降维，**最简单的方法是忽略某些特征。**比如挑出你最喜欢的三个特征，去掉其他。但如果忽略的特征包含有价值的甄别信息，比方说要判断蘑菇是否有毒，这就非常有问题了。

一个更复杂的办法，是只考虑主要的东西，来对数据集进行降维。**即将特征进行合并，用合并后的主成分来解释数据集中的大多数变化。**利用一项名为主成分分析（PCA）的技术，我们能够在降维的同时，尽可能保留数据集的宝贵变化。这里的思路是，我们能够创建一系列（更少）新的特征，每一项新特征都由几项旧特征合并得到。举个例子，其中一项新特征也许会同时代表形状和颜色，另一项代表尺寸和毒性。大体上，每一项新特征都会由原始特征的加权和得到。

下面，是一副帮助你直觉性理解数据降维的图示。

假设你有一个三维数据集（左），你想要把它转化为右边的二维数据集。PCA 会在原始 3D 空间找出主要的坐标轴，即点与点之间的差别最大。当我们把两条最能解释数据差异的坐标轴确定下来（左图中的黑线），就可以在这两条坐标轴上重新为数据作图。3D 数据集现在就变成了 2D 的。这里，我们选择的是低维例子，所以我们能看到发生了什么。但是，这项技术能用同样的方式应用于高维数据集。

![img](http://mmbiz.qpic.cn/mmbiz_png/bicdMLzImlibTTYxwnpiamZqjVBUDznAKIFicDLeTYnFcvFzQ3ov7FVlPQ1ibmIwiaYG77mD2AGzGJMicXrTdRmpiaGiaTQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

##   HyperTools

Kaggle 开发了 HyperTools 工具包，来帮助开发者对高维数据进行降维视觉探索。

它基本的流水线，是导入高维数据集（或者一系列高维数据集），在单个函数调用里降维，然后创建图表。该算法包建立在多个大家很熟悉的工具的基础上，比如 matplotlib、scikit-learn 和 seaborn。HyperTools 以易用性为首要设计目标，请见下面的两个例子。

##   用 HyperTools 找毒蘑菇，对静态点云进行可视化

首先，我们来探索下上文提到的蘑菇数据集。从导入相关算法库开始：

> 1. import pandas as pd
> 2. import hypertools as hyp

接下来，把数据读取到 pandas DataFrame:

> 1. data = pd.read_csv('../input/mushrooms.csv')
> 2. data.head()

![img](http://mmbiz.qpic.cn/mmbiz_png/bicdMLzImlibTTYxwnpiamZqjVBUDznAKIFGySbTtpBCv3r2kh0y1IEuvznA1ia5CmicSxDItk1SyUFUrJJgPSyfzYQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)DataFrame 的每一行对应着对某一个蘑菇的观察值，每一列反映出一个蘑菇的描述性特征。这里，仅展示了表单的一部分。现在，我们可以通过把数据导入 HyperTools，把高维数据在低维空间表示出来。为了对文本列进行处理，在降维之前，HyperTools 会先把每个文本列转为一系列二元的假变量。如果“菌盖尺寸”这一列包含“大”和“小”标签，这一列会被转为两个二元列，一个针对“大”，另一个针对“小”。 1 代表该特征（“大”或“小”）的存在，0 代表不存在。（注：详细解释请参考 pandas 文件中的 get_dummies 函数）

> 1. hyp.plot(data, 'o')

   ![img](http://mmbiz.qpic.cn/mmbiz_png/bicdMLzImlibTTYxwnpiamZqjVBUDznAKIFJ7tHiau0JKBnv2xcEVI5P1Lod2dXA7xdwoiaExfcibe98vuk8yicsUkIbg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

在绘制  DataFrame 上，我们实际上创建了一个三维的“蘑菇空间”。具有相似特征的蘑菇，是空间中距离相近的点，特征不同的，则距离更远。用这种方式做 DataFrame 可视化，一件事马上变得很清楚：数据中有多组簇。换句话说，蘑菇特征的所有组合并不是等可能的（equally likely），而特定的组合，会倾向于聚到一起。为更好理解这一空间，我们可以根据所感兴趣的数据特征，对每个点上色。举个例子，根据蘑菇是否有毒/可食用来上色。

> 1. hyp.plot(data,'o', group=class_labels, legend=list(set(class_labels)))



![img](http://mmbiz.qpic.cn/mmbiz_png/bicdMLzImlibTTYxwnpiamZqjVBUDznAKIFibZesUOcBpUASdIb6Hs7RhK0Lm51QleTZJDwjuMEF4rtlicbPVHHnKww/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*红色代表有毒，绿色无毒*

用这种方式可视化，可以清楚看出，每个簇中的蘑菇是否有毒都是稳定的。但不同之处在于簇与簇之间。另外，看起来有好几个十分明确的“有毒”以及“可食用”的簇。我们可以借助 HyperTools 的“聚类”功能，对此进一步探索。它使用了 k-means 聚类方法对观察值上色。数据集的描述表明其有 23 种不同种类的蘑菇，因此，我们把n _clusters 参数设为 23。

> 1. hyp.plot(data, 'o', n_clusters=23)

   ![img](http://mmbiz.qpic.cn/mmbiz_png/bicdMLzImlibTTYxwnpiamZqjVBUDznAKIFUiaLprbAwERtcX5fndFV92Mibt1WD4icfJ6ibHarcW9n5zko2VBGLGE7nw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

To gain access to the cluster labels, the clustering tool may be called directly using hyp.tools.cluster, and the resulting labels may then be passed to hyp.plot:

为访问簇的标签，该聚类工具可用 hyp.tools.cluster 直接调用，相关标签随机被传递到 hyp.plot:

> 1. cluster_labels = hyp.tools.cluster(data, n_clusters=23)
> 2. hyp.plot(data, group=cluster_labels)

在默认设置中，HyperTools 使用 PCA 来进行降维。但只需要额外的几行代码，我们就可以直接从 sklearn 中调用相关函数，以使用其它降维方法。。举个例子，如果我们使用 t-SNE 来给数据降维的话：

> 1. from sklearn.manifold import TSNE
> 2. TSNE_model = TSNE(n_components=3)
> 3. reduced_data_TSNE = TSNE_model.fit_transform(hyp.tools.df2mat(data))
> 4. hyp.plot(reduced_data_TSNE,'o', group=class_labels, legend=list(set(class_labels)))

![img](http://mmbiz.qpic.cn/mmbiz_png/bicdMLzImlibTTYxwnpiamZqjVBUDznAKIFQTLic2jV18Gsb69YnicEPgneLQu4a160mm2E2PpkEib1wQwqCOrboiaAQg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

不同的降维方法，会突出或保留数据的不同方面。这里有一个包含额外示例（包括其它降维方法）的资源库。

对于如同通过降维和可视化暴露出数据的几何机构，上述的数据考察（data expedition）给出了一个例子。蘑菇数据集的观察值形成了独立的簇，我们通过 HyperTools 来发现这些簇。类似这样的探索和可视化，能够指导我哦们的分析决策，比如，是否要用一个特定种类的分类器，来区分有毒 vs 可食用的蘑菇。如果你想要自己试试用 HyperTools 分析这个蘑菇数据集，请戳这里。

##   用 HyperTools 发现全球变暖

上文蘑菇数据集包含的是静态观察值，我们再一起来看看全球气温数据。这个案例会向大家展示，如何利用 HyperTools  使用动态轨迹对时间序列数据进行可视化。

接下来的数据集，是 1875–2013 年间全球 20 个城市每月的气温记录。为了用 HyperTools 来准备数据集，我们创建了一个时间/城市矩阵，每一行是接下来每月的气温记录，每一列是不同城市的气温值。你可以用 Kaggle 上的 Berkeley Earth Climate Change 来重建这一示例，或者克隆这一 GitHub 资源库。

为了对温度变化做可视化，我们会用 HyperTools 来给数据降维，然后把温度随时间的变化用线画出来：

> 1. hyp.plot(temps)

![img](http://mmbiz.qpic.cn/mmbiz_png/bicdMLzImlibTTYxwnpiamZqjVBUDznAKIFsOkSMiafx9Nnzs9BZmP2FkBg1TfobofibqMjwba9xJhWOCEC44CLJ6BA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

这看起来像一团乱麻，是吧？但我们承诺了找出数据的结构——现在就来找吧。

由于每个城市的地理位置不同，它温度时间序列的平均值和方差会比其他城市更高或者更低。这会反过来影响降维时该城市的权重。为了对每个城市在图表中的权重进行标准化处理，我们可设置标准化 flag （默认值是 False）。设置 normalize='across' 。HyperTools 整合了一系列实用的标准化选项，详情请戳这里。

> 1. hyp.plot(temps, normalize='across')

![img](http://mmbiz.qpic.cn/mmbiz_gif/bicdMLzImlibTTYxwnpiamZqjVBUDznAKIFspEYXxbvOmdXWhcAMYWVBs6BOB0p4pg9SNG6zXgxM0cwq7aYXdPjdA/0/mmbizgif?tp=webp&wxfrom=5&wx_lazy=1)

用鼠标旋转该数据图，旋即暴露出的结构很有意思。我们可以按照年份来对线条上色，使其结构更显眼，并帮助我们理解它如何随时间而变化。偏红的线条，意味着时间更久远，偏蓝的线条意味着时间更近。

> 1. hyp.plot(temps, normalize='across', group=years.flatten(), palette='RdBu_r')

![img](http://mmbiz.qpic.cn/mmbiz_png/bicdMLzImlibTTYxwnpiamZqjVBUDznAKIFuicANwMTdnT1MlRwF9FG61el3vYa7hjoAGhnmmXxQKqWwdSBsT1Bjyg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

上色后的线条，暴露出了数据的两个关键结构信息。第一，有系统性的蓝色到红色的色彩渐变，表明全球的整体气温模式有系统性的改变。第二，每种颜色有周期性的模式，反映出季节气候变化。我们也可以用二维图形对这两个现象做可视化：

> 1. hyp.plot(temps, normalize='across', group=years.flatten(), palette='RdBu_r', ndims=2)

![img](http://mmbiz.qpic.cn/mmbiz_png/bicdMLzImlibTTYxwnpiamZqjVBUDznAKIFNrGUL6michATZcfZ8nAoHZ61mY81S9KltPhCfOeI3GAlU7d9pJrfKcA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

现在是压轴戏——在创建静态图形之外，HyperTools 还能创建动图，这有时能显露出数据中的其他模式。创造出动图，只需要在对时间序列数据做可视化时，简单地把 animate=True 传给 hyp.plot。如果你还传了 chemtrails=True，一条数据的低透明度足迹会保留在图形中：

> 1. hyp.plot(temps, normalize='across', animate=True, chemtrails=True)

![img](http://mmbiz.qpic.cn/mmbiz_png/bicdMLzImlibTTYxwnpiamZqjVBUDznAKIFTuzAvXI4uEvEDibIvIQcd4zeZ0jkxAfs1TD28W7nUUW420oLiczLkcaw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

最终的成果，便是该动画。注视着它给你带来的满足感，叫做“全球变暖”。

以上便是用 HyperTools 为气象和蘑菇数据做可视化的例子。想要了解更多的话，请访问项目的 GitHub 资源库，文件阅读网页，Kaggle 研究团队写的论文，以及 notebooks 演示。



# 相关

- [使用 HyperTools 的正确姿势! | Kaggle 实战教程](https://mp.weixin.qq.com/s?__biz=MjM5ODU3OTIyOA==&mid=503180912&idx=2&sn=c42c1a9f14ccaa90e83569df641e6a38&chksm=3ec1d30309b65a15133c26a0141ece4cf76bc4c3b6b9e7bbc92d9e6bb939fbfc65d8ef1dcfc2&mpshare=1&scene=1&srcid=0517xJ89BxO80wC98OYO6o1y#rd)
