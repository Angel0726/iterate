---
title: 称霸Kaggle的十大深度学习技巧
toc: true
date: 2019-11-17
---
# 称霸Kaggle的十大深度学习技巧



他这篇文章发表在FloydHub官方博客上，因为除了来自Fast.ai的技巧之外，他还用了FloydHub的免设置深度学习GPU云平台。

接下来，我们看看他从fast.ai学来的十大技艺：

## 1. 使用Fast.ai库

```
这一条最为简单直接。from fast.ai import *
```

**Fast.ai库**是一个新手友好型的深度学习工具箱，而且是目前复现最新算法的首要之选。

每当Fast.ai团队及AI研究者发现一篇有趣论文时，会在各种数据集上进行测试，并确定合适的调优方法。他们会把**效果较好的模型实现**加入到这个函数库中，用户可以快速载入这些模型。

于是，Fast.ai库成了一个功能强大的工具箱，能够快速载入一些当前最新的算法实现，如带重启的随机梯度下降算法、差分学习率和测试时增强等等，这里不逐一提及了。

下面会分别介绍这些技术，并展示如何使用Fast.ai库来快速使用它们。

这个函数库是基于**PyTorch**构建，构建模型时可以流畅地使用。

Fast.ai库地址：
https://github.com/fastai/fastai

# 2. 使用多个而不是单一学习率

![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtAwb0qia3sxLcBUOzDpAlKfJ70pFHVfZ16c53mugufM5V153stL15kiaRVvcYUgKPnNnmx6AqIDmJ5w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**差分学习率**（Differential Learning rates）意味着在训练时变换网络层比提高网络深度更重要。

基于已有模型来训练深度学习网络，这是一种被验证过很可靠的方法，可以在计算机视觉任务中得到更好的效果。

大部分已有网络（如Resnet、VGG和Inception等）都是在ImageNet数据集训练的，因此我们要根据所用数据集**与ImageNet图像的相似性**，来适当改变网络权重。

在修改这些权重时，我们通常要对模型的最后几层进行修改，因为这些层被用于检测基本特征（如边缘和轮廓），不同数据集有着不同基本特征。

首先，要使用Fast.ai库来**获得**预训练的模型，代码如下：

```
from fastai.conv_learner import *

# import library for creating learning object for convolutional #networks
model = VVG16()

# assign model to resnet, vgg, or even your own custom model
PATH = './folder_containing_images'
data = ImageClassifierData.from_paths(PATH)

# create fast ai data object, in this method we use from_paths where
# inside PATH each image class is separated into different folders

learn = ConvLearner.pretrained(model, data, precompute=True)

# create a learn object to quickly utilise state of the art
# techniques from the fast ai library
```

创建学习对象之后（learn object），通过**快速冻结**前面网络层并**微调**后面网络层来解决问题：

```
learn.freeze()

# freeze layers up to the last one, so weights will not be updated.

learning_rate = 0.1
learn.fit(learning_rate, epochs=3)

# train only the last layer for a few epochs
```

当后面网络层产生了良好效果，我们会应用**差分学习率**来改变前面网络层。在实际中，一般将学习率的缩小倍数设置为**10**倍：

```
learn.unfreeze()

# set requires_grads to be True for all layers, so they can be updated

learning_rate = [0.001, 0.01, 0.1]
# learning rate is set so that deepest third of layers have a rate of 0.001, # middle layers have a rate of 0.01, and final layers 0.1.

learn.fit(learning_rate, epochs=3)
# train model for three epoch with using differential learning rates
```

# 3. 如何找到合适的学习率

学习率是神经网络训练中最重要的超参数，没有之一，但之前在实际应用中很难为神经网络选择最佳的学习率。

Leslie Smith的一篇**周期性学习率论文**发现了答案，这是一个相对不知名的发现，直到它被Fast.ai课程推广后才逐渐被广泛使用。

这篇论文是：

Cyclical Learning Rates for Training Neural Networks
https://arxiv.org/abs/1506.01186

在这种方法中，我们尝试使用较低学习率来训练神经网络，但是在每个批次中以指数形式增加，相应代码如下：

```
learn.lr_find()
# run on learn object where learning rate is increased  exponentially

learn.sched.plot_lr()
# plot graph of learning rate against iterations
```

![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtAwb0qia3sxLcBUOzDpAlKfJicibxUenrkuQxebNyGE8f1wn2rPia1w28C2uak24msljicJKZJQdmic9Bug/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

###### **△** 每次迭代后学习率以指数形式增长

同时，记录每个学习率对应的Loss值，然后画出学习率和Loss值的关系图：

```
learn.sched.plot()
# plots the loss against the learning rate
```

![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtAwb0qia3sxLcBUOzDpAlKfJ6duNdBTHhNzPEOwECjYrJGen0Vb9Ij7O3tEt6cEibYBIDh0zfIPaaiaw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

###### **△** 找出Loss值在下降但仍未稳定的点

通过找出学习率最高且Loss值仍在下降的值来确定最佳学习率。在上述情况中，该值将为0.01。

# 4. 余弦退火

在采用**批次随机梯度下降**算法时，神经网络应该越来越接近Loss值的全局最小值。当它逐渐接近这个最小值时，学习率应该变得更小来使得模型不会超调且尽可能接近这一点。

余弦退火（Cosine annealing）利用余弦函数来**降低学习率**，进而解决这个问题，如下图所示：

![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtAwb0qia3sxLcBUOzDpAlKfJSvQXyuDVlVyqmyaKaUY4dL3cAYdU6iciaM1v6Ef1JAsT0f2Yfm9MBatQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

###### **△** 余弦值随着x增大而减小

从上图可以看出，随着x的增加，余弦值首先**缓慢**下降，然后**加速**下降，**再次缓慢**下降。这种下降模式能和学习率配合，以一种十分有效的计算方式来产生很好的效果。

```
learn.fit(0.1, 1)
# Calling learn fit automatically takes advantage of cosine annealing
```

我们可以用Fast.ai库中的**learn.fit()**函数，来快速实现这个算法，在整个周期中不断降低学习率，如下图所示：

![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtAwb0qia3sxLcBUOzDpAlKfJDazKNKWdIawdvHp5KOEkCuQiaX8IeQrkYKqvxtFFl0scclCYeColJmg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

###### **△** 在一个需要200次迭代的周期中学习率不断降低

同时，在这种方法基础上，我们可以进一步引入重启机制。

# 5. 带重启的SGD算法

在训练时，**梯度下降**算法可能陷入局部最小值，而不是全局最小值。

![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtAwb0qia3sxLcBUOzDpAlKfJZ0E5fTPIUEkRWfZPvugGmNS6p95ticqQ5iaI4KBGVicGqpLIOrtzvadBw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

###### **△** 陷入局部最小值的梯度下降算法

梯度下降算法可以通过**突然提高学习率**，来“跳出”局部最小值并找到通向全局最小值的路径。这种方式称为**带重启的**随机梯度下降方法（stochastic gradient descent with restarts, **SGDR**），这个方法在Loshchilov和Hutter的ICLR论文中展示出了很好的效果。

这篇论文是：

SGDR: Stochastic Gradient Descent with Warm Restarts
https://arxiv.org/abs/1608.03983

用Fast.ai库可以快速导入SGDR算法。当调用learn.fit(learning_rate, epochs)函数时，学习率在每个周期开始时重置为参数输入时的初始值，然后像上面余弦退火部分描述的那样，逐渐减小。

![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtAwb0qia3sxLcBUOzDpAlKfJUILfVLRM5WT49z9BL9TCGIDERcq81suEIoKzIESw13PicwJBuw8yEDA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

每当学习率下降到最小点，在上图中为每100次迭代，我们称为一个循环。

```
cycle_len = 1
# decide how many epochs it takes for the learning rate to fall to
# its minimum point. In this case, 1 epoch

cycle_mult=2
# at the end of each cycle, multiply the cycle_len value by 2

learn.fit(0.1, 3, cycle_len=2, cycle_mult=2)
# in this case there will be three restarts. The first time with
# cycle_len of 1, so it will take 1 epoch to complete the cycle.
# cycle_mult=2 so the next cycle with have a length of two epochs,
# and the next four.
```

![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtAwb0qia3sxLcBUOzDpAlKfJUq4vBsSEOdO02VtjsSVQD1FJjHrgpL0BLCOwiavCIxEBib3ZD3l1czlg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

###### **△** 每个循环所包含的周期都是上一个循环的2倍

利用这些参数，和使用差分学习率，这些技巧是Fast.ai用户在图像分类问题上取得良好效果的关键。

Fast.ai论坛有个帖子专门讨论Cycle_mult和cycle_len函数，地址在这里：
http://forums.fast.ai/t/understanding-cycle-len-and-cycle-mult/9413/8

更多关于学习率的详细内容可参考这个Fast.ai课程：
http://course.fast.ai/lessons/lesson2.html

# 6. 人格化你的激活函数

Softmax只喜欢选择一样东西；

Sigmoid想知道你在[-1, 1]区间上的位置，并不关心你超出这些值后的增加量；

Relu是一名俱乐部保镖，要将负数拒之门外。

……

以这种思路对待激活函数，看起来很愚蠢，但是**安排一个角色**后能确保把他们用到正确任务中。

正如fast.ai创始人Jeremy Howard指出，不少学术论文中也把Softmax函数用在多分类问题中。在DL学习过程中，我也看到它在论文和博客中多次使用不当。

# 7. 迁移学习在NLP问题中非常有效

正如预训练好的模型在计算机视觉任务中很有效一样，已有研究表明，自然语言处理（NLP）模型也可以从这种方法中受益。

在Fast.ai第4课中，Jeremy Howard用迁移学习方法建立了一个模型，来判断IMDB上的电影评论是积极的还是消极的。

这种方法的效果立竿见影，他所达到的准确率超过了Salesforce论文中展示的所有先前模型：
https://einstein.ai/research/learned-in-translation-contextualized-word-vectors。

![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtAwb0qia3sxLcBUOzDpAlKfJlbAdXAvKxf89fdgWPFxiatYXdhrWxfX9kva0TpYbLSkMicSBtruRMaNA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

###### **△** 预先存在的架构提供了最先进的NLP性能

这个模型的关键在于先训练模型来获得对语言的一些理解，然后再使用这种预训练好的模型作为新模型的一部分来分析情绪。

为了创建第一个模型，我们训练了一个循环神经网络（RNN）来预测文本序列中的下个单词，这称为**语言建模**。当训练后网络的准确率达到一定值，它对每个单词的编码模式就会传递给用于情感分析的新模型。

在上面的例子中，我们看到这个语言模型与另一个模型集成后用于情感分析，但是这种方法可以应用到**其他任何NLP任务**中，包括**翻译**和**数据提取**。

而且，计算机视觉中的一些技巧，也同样适用于此，如上面提到的冻结网络层和使用差分学习率，在这里也能取得更好的效果。

这种方法在NLP任务上的使用涉及很多细节，这里就不贴出代码了，可访问相应课程和代码。

课程：
http://course.fast.ai/lessons/lesson4.html

代码：

https://github.com/fastai/fastai/blob/master/courses/dl1/lesson4-imdb.ipynb

# 8. 深度学习在处理结构化数据上的优势

Fast.ai课程中展示了深度学习在处理结构化数据上的突出表现，且无需借助特征工程以及领域内的特定知识。

这个库充分利用了PyTorch中**embedding**函数，允许将**分类变量**快速转换为嵌入矩阵。

他们展示出的技术比较简单直接，只需将分类变量转换为数字，然后为每个值分配嵌入向量：

![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtAwb0qia3sxLcBUOzDpAlKfJZn2wqCRmy2AOg0dHIPZ0jgAIybzicd5qWNmhFHc2UMFyGHPGudJX4Ng/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

###### **△** 一周中的每一天都嵌入了四个值

在这类任务上，传统做法是创建虚拟变量，即进行一次热编码。与之相比，这种方式的优点是用四个数值代替一个数值来描述每一天，因此可获得更高的数据维度和更丰富的关系。

这种方法在Rossman Kaggle比赛中获得第三名，惜败于两位利用专业知识来创建许多额外特征的领域专家。

相关课程：
http://course.fast.ai/lessons/lesson4.html

代码：
https://github.com/fastai/fastai/blob/master/courses/dl1/lesson3-rossman.ipynb

这种用深度学习来减少对特征工程依赖的思路，也被Pinterest证实过。他也提到过，他们正努力通过深度学习模型，期望用更少的工作量来获得更好的效果。

# 9. 更多内置函数：Dropout层、尺寸设置、TTA

4月30日，Fast.ai团队在斯坦福大学举办的DAWNBench竞赛中，赢得了基于Imagenet和CIFAR10的分类任务。在Jeremy的夺冠总结中，他将这次成功归功于fast.ai库中的一些额外函数。

其中之一是**Dropout**层，由Geoffrey Hinton两年前在一篇开创性的论文中提出。它最初很受欢迎，但在最近的计算机视觉论文中似乎有所忽略。这篇论文是：

Dropout: A Simple Way to Prevent Neural Networks from Overfitting：
https://www.cs.toronto.edu/~hinton/absps/JMLRdropout.pdf

然而，PyTorch库使它的实现变得很简单，用Fast.ai库加载它就更容易了。

![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtAwb0qia3sxLcBUOzDpAlKfJcgZdBwTPIiassicMQj5mGgu41dFPNKKmiaEbG5g28Yet0uicGiaWsqxseGw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

###### **△** 空格表示Dropout函数的作用点

Dropout函数能**减弱过拟合**效应，因此要在CIFAR-10这样一个相对较小的数据集上取胜，这点很重要。在创建learn对象时，Fast.ai库会自动加入dropout函数，同时可使用ps变量来修改参数，如下所示：

```
learn = ConvLearner.pretrained(model, data, ps=0.5, precompute=True)
# creates a dropout of 0.5 (i.e. half the activations) on test dataset.
# This is automatically turned off for the validation set
```

有一种很简单有效的方法，经常用来处理过拟合效应和提高准确性，它就是**训练小尺寸图像**，然后**增大尺寸**并**再次训练**相同模型。

```
# create a data object with images of sz * sz pixels
def get_data(sz):
    tmfs = tfms_from_model(model, sz)
    # tells what size images should be, additional transformations such
    # image flips and zooms can easily be added here too

    data = ImageClassifierData.from_paths(PATH, tfms=tfms)
    # creates fastai data object of create size

    return data

learn.set_data(get_data(299))
# changes the data in the learn object to be images of size 299
# without changing the model.

learn.fit(0.1, 3)
# train for a few epochs on larger versions of images, avoiding overfitting
```

还有一种先进技巧，可将准确率提高若干个百分点，它就是**测试时增强**（test time augmentation, **TTA**）。这里会为原始图像造出多个不同版本，包括不同区域裁剪和更改缩放程度等，并将它们输入到模型中；然后对多个版本进行计算得到平均输出，作为图像的最终输出分数，可调用learn.TTA()来使用该算法。

```
preds, target = learn.TTA()
```

这种技术很有效，因为原始图像显示的区域可能会缺少一些重要特征，在模型中输入图像的多个版本并取平均值，能解决上述问题。

# 10. 创新力很关键

![img](https://mmbiz.qpic.cn/mmbiz_jpg/YicUhk5aAGtAwb0qia3sxLcBUOzDpAlKfJ5suBgU0Pt7cVj9CyRtQOibxibwDIjIKRBchFW31RtKhvXllk5EyLUVibg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

在DAWNBench比赛中，Fast.ai团队提出的模型不仅速度最快，而且计算成本低。要明白，要构建成功的DL应用，不只是一个利用大量GPU资源的计算任务，而应该是一个需要创造力、直觉和创新力的问题。

本文中讨论的一些突破，包括Dropout层、余弦退火和带重启的SGD方法等，实际上是研究者针对一些问题想到的不同解决方式。与简单地增大训练数据集相比，能**更好地提升准确率**。

硅谷的很多大公司有大量GPU资源，但是，不要认为他们的先进效果遥不可及，你也能靠创新力提出一些新思路，来挑战效果排行榜。

事实上，有时计算力的局限也是一种机会，因为需求是创新的动力源泉。

###### 原文： https://blog.floydhub.com/ten-techniques-from-fast-ai/


# 相关

- [称霸Kaggle的十大深度学习技巧](https://mp.weixin.qq.com/s?__biz=MzIxODM4MjA5MA==&mid=2247487050&idx=2&sn=c06fd79047d23f434c9b030e9f88affb&chksm=97ea242fa09dad391ef63b20f1524786ca4d60992d8d5b31af9902e4dce155fe0cb3c7217bd7&mpshare=1&scene=1&srcid=0807yxYogeSwVavQRCOndTID#rd)
