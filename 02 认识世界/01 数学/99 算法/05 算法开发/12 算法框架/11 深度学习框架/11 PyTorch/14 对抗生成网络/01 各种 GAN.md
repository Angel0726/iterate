---
title: 01 各种 GAN
toc: true
date: 2019-06-20
---
# 可以补充进来的

- 感觉这本书看起来就像走在云彩里一样，完全不通顺，高一脚底一脚，简直了，拆掉。

# DCGAN 原理

在 GAN 的基础上，DCGAN 的一大特点就是使用了卷积层。


DCGAN 的基本架构就是使用几层 “反卷积”（Deconvolution）网络，如图所示：

<center>

![](http://images.iterate.site/blog/image/20190620/7u2QThS0bDzF.png?imageslim){ width=55% }

</center>


“反卷积” 类似于一种反向卷积，这跟用反向传播算法训练监督的卷积神经网络（CNN）是类似的操作。

DCGAN 对卷积神经网络的结构做了一些改变，以提高样本的质量和收敛的速度，这些改变如下：

- 取消所有 pooling 层。G 网络中使用反卷积（Deconvolutional layer）进行上采样，D 网络中用加入 stride 的卷积代替 pooling。<span style="color:red;">什么意思？怎么使用反卷积来进行上采样的？怎么使用 stride 的卷积来代替 pooling 的？为什么要取消 pooling 层？</span>
- D 和 G 中均使用 batch normalization。<span style="color:red;">为什么要使用 batch normalization ？对于图像的网络还需要使用 batch normalization 吗？</span>
- 去掉 FC 层，使网络变为全卷积网络。<span style="color:red;">为什么要去掉 FC 层？为什么一定要是 全卷积网络？不是全卷积网络可以吗？</span>
- G 网络中使用 ReLU 作为激活函数，最后一层使用 tanh。<span style="color:red;">为什么这个地方又使用 ReLU 了？为什么最后一层使用 tanh？</span>
- D 网络中使用 LeakyReLU 作为激活函数，最后一层使用 softmax。<span style="color:red;">为什么使用 LeakyReLU 而不是 ReLU ？</span>

Generative networks 的架构参数：

- 在模型的细节处理上，预处理环节，将图像 `scale` 到 tanh的 `[-1,1]`。
- mini-batch训练，batch size是 128。
- 所有的参数初始化在（0,0.02）的正态分布中随即得到。<span style="color:red;">使用正态分布来初始化参数的原因是啥来着？</span>
- LeakyReLU 的斜率是 0.2。<span style="color:red;">什么时候需要修改 LeakyReLU 的斜率这个超参数？这我感觉改了没啥用呀？嗯，会有什么用处呢？以前没怎么用过这个。</span>
- 虽然之前的 GAN 使用 momentum 来加速训练，DCGAN 使用调好超参的 Adam optimizer。<span style="color:red;">怎么调好超参的？</span>
- learning rate=0.0002。
- 将 momentum 参数 beta 从 0.9 降为 0.5 来防止震荡和不稳定。<span style="color:red;">当震荡时，怎么排查出原因是这个的？而且，一般一个新的模型使用的 beta 是多少？一定要使用吗？</span>

DCGAN 模型：

<center>

![](http://images.iterate.site/blog/image/20190620/XKDV2o3zat7p.png?imageslim){ width=55% }

</center>

下图等式左边都是噪声 z（一般为均匀噪声）经过 G（z）产生的人脸图片：<span style="color:red;">真的假的？是通过均匀噪声生成的吗？配错图了吧？？</span>


<center>

![](http://images.iterate.site/blog/image/20190620/5y39vgvob490.png?imageslim){ width=55% }

</center>


使用 GAN 作为特征提取器分类 CIFAR-10，如图 10.6所示。虽然 2015 年 Dosovitskiy 等提出 DCGAN 的性能仍然比不上典型的 CNN，但仍然取得较好的效果：<span style="color:red;">有点想知道，为什么 DCGAN 的性能比不上典型 CNN 呢？</span>

<center>

![](http://images.iterate.site/blog/image/20190620/VAnMpHiwklwX.png?imageslim){ width=55% }

</center>

## CGAN

在 GAN 的基础上，经过简单的改造，把纯无监督的 GAN 变成半监督或者有监督的，为 GAN 的训练加上束缚。例如 Conditional Generative Adversarial Nets（CGAN）模型。在生成模型（G）和判别模型（D）的建模中加入标签。因此，CGAN 可以看做把无监督的 GAN 变成有监督的模型。<span style="color:red;">啥？没明白怎么简单的改造的？怎么加上束缚的？妈的，说话跟唱戏一样。</span>

CGAN模型：

<center>

![](http://images.iterate.site/blog/image/20190620/if4auKxLtm5c.png?imageslim){ width=55% }

</center>


就是 G 网络的输入在 z 的基础上连接一个输入 y，然后在 D 网络的输入在 x 的基础上也连接一个 y。

从流程图 10.7 可以看出训练方式几乎就是不变的，但是 GAN 从无监督变成了有监督。

我们来看看 CGAN 的应用，如图所示，利用 CGAN 进行文字和位置约束来生成图片。在图片的特定位置约束，同时加上相应的标签文字，作为随机输入，生成图片，然后与真实图片做对比，进行判断图片真假。利用 CGAN 进行文字和位置约束来生成图片：<span style="color:red;">怎么进行文字和位置的约束的？</span>


<center>

![](http://images.iterate.site/blog/image/20190620/LnFQmSAhcWcm.png?imageslim){ width=55% }

</center>

下面是利用 CGAN 进行文字和位置约束来生成图片效果图：

<center>

![](http://images.iterate.site/blog/image/20190620/AtGfBmVT1YYC.png?imageslim){ width=55% }

</center>

<span style="color:red;">擦嘞，真的假的，这么厉害吗？</span>


利用 CGAN 生成关键点多层次参与图片生成模型流程：

<center>

![](http://images.iterate.site/blog/image/20190620/wqUldGNUkwza.png?imageslim){ width=55% }

</center>


比如说我们要在图片的具体位置进行图片的伸缩、平移、扩张等。下图所示是对框架里面的鸟儿进行缩小、平移、伸缩的案例：<span style="color:red;">这么厉害吗？震惊了。</span>

<center>

![](http://images.iterate.site/blog/image/20190620/jq8gfFnsMeKs.png?imageslim){ width=55% }

</center>


Photo-Realistic Single Image Super-Resolution Using a Generative Adversarial Network 这篇文章将对抗学习用于基于单幅图像的高分辨重建。

无论是 GAN 还是 DCGAN，这种对抗学习的方式，是一种比较成功的生成模型，可以从训练数据中学习到近似的分布情况，那么有了这个分布，自然可以应用到很多领域。比如图像的修复，图像的超分辨率，图像翻译等。<span style="color:red;">嗯，厉害。</span>

在 GAN 基础上还有 NIPS2016 的 InfoGAN 模型。InfoGAN 的目标就是通过非监督学习得到可分解的特征表示。使用 GAN 加上最大化生成的图片和输入编码之间的互信息。最大的好处就是可以不需要监督学习，而且不需要大量额外的计算就能得到可解释的特征。<span style="color:red;">什么意思？怎么得到可解释的特征的？话说在做之前的一个项目的时候，一直想知道的就是，如果知道一个图像的特征，那么怎么从大图像中定位这个小图像？</span>

自从 2014 年 Ian Goodfellow 提出 GAN 以来，GAN就存在着训练困难、生成器和判别器的 loss 无法指示训练进程、生成样本缺乏多样性等问题。Wasserstein GAN（下面简称 WGAN）彻底解决了 GAN 训练不稳定的问题，不再需要小心平衡生成器和判别器的训练程度。<span style="color:red;">真的假的？怎么彻底解决的？</span>同时，训练过程中有一个数值指示训练的进程，这个数值越小代表 GAN 训练得越好，代表生成器产生的图像质量越高，只需要最简单的多层全连接网络就可以做到网络结构设计。由于这些网络比较复杂，有兴趣的读者可以自行查阅学习。<span style="color:red;">？这说的是啥？简单还是复杂？</span>





# 相关

- 《深度学习框架 Pytorch 快速开发与实战》
