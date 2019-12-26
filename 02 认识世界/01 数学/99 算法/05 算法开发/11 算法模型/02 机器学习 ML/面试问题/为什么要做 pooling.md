---
title: 为什么要做 pooling
toc: true
date: 2019-07-31
---
# 为什么要做 pooling？不做不行吗？

看到图像分割中的 FCN，突然想，到底为什么要做 pooling？导致图像信息丢失了？这些信息还要从前面的卷积层与 upsampling 之后的卷积层进行合并，不是有点多此一举吗？


1. 减小计算量计算量与通道数、特征图长和特征图宽的乘积呈正比，若输入通道数、特征图宽和特征图高分别为 c、w、h，则经过 stride 为 2 的 pooling 层，输出为 c、w/2、h/2，计算量减小为 1/4。<span style="color:red;">这个计算量是很多的计算量吗？</span>
2. 减小内存消耗网络模型的内存消耗与通道数、特征图长和特征图宽的乘积呈正比，若输入通道数、特征图宽和特征图高分别为 $c$、$w$、$h$，则经过 stride 为 $2$ 的 pooling 层，输出为 $n$、$w/2$、$h/2$，内存消耗量减小为 $1/4$。<span style="color:red;">嗯，这个也是，不过 batch 改小一点也可以吧？</span>
3. 提高感受野大小 $stride>1$ 的 pooling 可以极大地提高感受野大小，

- 图 3.1是一个有 5 层卷积的简单神经网络，
- 图 3.2在图 3.1的基础上，添加了 4 层 pooling。

图 3.1的网络输出特征的感受野为 $11*11$，而图 3.2的网络输出特征的感受野为 $93*93$，是图 3.1的 $81$ 倍。

<center>

![mark](http://images.iterate.site/blog/image/20190731/HfpSTkU1ejjV.png?imageslim){width=75%}

</center>

> 图 3.1 没有 pooling 的网络


<center>

![mark](http://images.iterate.site/blog/image/20190731/vUrYENSRpIlw.png?imageslim){width=75%}

</center>

> 图 3.2 有 pooling 的网络


<span style="color:red;">是的，但是这个是以信息的丢失为代价的吧？那么这样的话再大的感受野又有什么用呢？而且，为什么不能通过 conv 实现同等的扩大感受野的效果？</span>

4. 如果下一网络层的参数数量与特征图大小相关（例如全连接层），pooling 可以减小参数个数如果下一网络层为全连接层，前一层通道数为 $c1$，下一层通道数为 $c2$ ，则下一层参数数量为 $c1*w*h*c2$；而在这两层之间添加一个 $stride=2$ 的 pooling 层后，下一层参数量为 $c1*w/2*h/2*c2$ ，为未添加 pooling 层参数量的 $1/4$。<span style="color:red;">这个倒是，但是前面不是已经丢失了信息了吗？这样的 trade-off 是值得的吗？</span>
5. 增加平移不变性平移不变性的意思是，输入有一些细微的平移，输出特征几乎不变。不过这个用处貌似不太大，ResNet 除了第一层的降采样使用了 pooling 之外，其他降采样层，都用的是 stride=2 的 conv；darknet53 所有的降采样层使用的都是 stride=2 的 conv。然而 ResNet和 darknet53 效果比使用 pooling 的网络并没有差。<span style="color:red;">是呀，增加平移不变性这个跟 conv 没什么区别吧。</span>


也就是说，减小计算量和减小内存消耗使得设计更深的网络成为可能，而深对于提高网络的表达能力非常重要。<span style="color:red;">是的，这个目前还是很关键的。</span>



## convolution 也能对 input 做压缩，为什么非要用 pooling layer?

因为 pooling layer 的工作原理，在压缩上比 convolution 更专注和易用





# 相关

- [CNN网络的 pooling 层有什么用？](https://www.zhihu.com/question/36686900)
