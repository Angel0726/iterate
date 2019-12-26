---
title: 04 Functional 函数
toc: true
date: 2019-06-07
---

# 可以补充进来的


- 感觉这本书的这一张各种混乱，还是要融合几本进来。
- 感觉还是有很多可以补充进来的，这里只是大概讲了下皮毛和简单的函数的参数。


# Functional 函数


`torch.nn.functional` 包里面提供的函数如下。

- Convolution 函数
- Pooling 函数
- 非线性激活函数
- Normalization 函数 <span style="color:red;">话说好像真的很少看到有使用 Normalization 函数的，啥情况？这个不是应该经常使用的吗？</span>
- 线性函数
- Dropout 函数
- 距离函数（Distance Functions）
- 损失函数（Loss Functions）<span style="color:red;">看到这个就想知道要怎么自定义这些？</span>
- Vision Functions <span style="color:red;">这个 vision functions 是什么函数？好像没有看到使用过</span>


`nn.functional` 中的函数仅仅定义了一些具体的基本操作，不能构成 PyTorch 中的一个 Layer。<span style="color:red;">对了，为什么感觉这个 function 和 一些 layer 是重复的？到底是使用 function 还是 layer？还是说实际上是么有重复的？确认下。</span>

当你需要自定义一些非标准 Layer时，可以在其中调用 nn.functional 中的操作。<span style="color:red;">嗯嗯，这个还挺好。</span>比如 F.relu 仅仅是一个函数，参数包括输入和计算所需参数，返回计算结果，它不能存储任何上下文信息。所有的 Function 函数都从基础类 Function 派生，实现 Forward 和 Backward 静态方法。而在 Forward和 Backward 实现内部，调用了 C 的后端实现。<span style="color:red;">非常想知道 forward 和 backward 是怎么实现的？而且非常想知道是怎么调用 c 的后端实现的？</span>

`Torch.nn` 包里面只是包装好了神经网络架构的类，`nn.functional` 与 `Torch.nn` 包相比，`nn.functional` 是可以直接调用函数的。<span style="color:red;">什么意思？为什么要提到这个？是用来区分什么场景下的使用吗？</span>

我们来具体看一看下面这个函数：

```
torch.nn.functional.conv1d(input,weight,bias=None,stride=1,padding=0, dilation=1,groups=1)
```

对输入的数据进行一维卷积。卷积的本质就是用卷积核的参数来提取原始数据的特征，通过矩阵点乘的运算，提取出和卷积核特征一致的值，如果卷积层有多个卷积核，则神经网络会自动学习卷积核的参数值，使每个卷积核可以代表一个特征。<span style="color:red;">每个卷积核代表一个特征？不对吧？只能代表是抽取的某种类型的特征。</span>


参数说明如下。

- input 输入的 Tensor 数据，格式为 (batch,channels,W)，三维数组，第一维度是样本数量，第二维度是通道数或者记录数，第三维度是宽度。<span style="color:red;">一直对这个通道的概念理解的不深，到底什么是通道？为什么会有通道这个维度？对于某种特定的应用场景来说，它的维度是什么？</span>
- weight：过滤器，也叫卷积核权重。是一个三维数组，(out_channels, in_channels/groups,kW)。out_channels 是卷积核输出层的神经元个数，也就是这层有多少个卷积核；in_channels 是输入通道数；kW 是卷积核的宽度。<span style="color:red;">卷积核的宽度是什么？groups 是什么？</span>
- bias：位移参数。
- stride：滑动窗口，默认为 1，指每次卷积对原数据滑动 1 个单元格。
- padding：是否对输入数据填充 0。Padding 可以将输入数据的区域改造成是卷积核大小的整数倍，这样对不满足卷积核大小的部分数据就不会忽略了。通过 Padding 参数指定填充区域的高度和宽度，默认为 0。
- dilation：卷积核之间的空格，默认为 1。
- groups：将输入数据分成组，in_channels 应该被组数整除，默认为 1。<span style="color:red;">groups 是什么？为什么一定要分组？</span>


conv1d 是一维卷积，它和 conv2d 的区别在于只对宽度进行卷积，对高度不卷积。<span style="color:red;">嗯。</span>


例子：
>>> filters = autograd.Variable（torch.randn（33, 16, 3））
>>> inputs = autograd.Variable（torch.randn（20, 16, 50））
>>> F.conv1d（inputs, filters）


## Pooling 函数

Pooling 函数主要是用于图像处理的卷积神经网络中，但随着深层神经网络的发展，Pooling函数相关技术在其他领域，其他结构的神经网络中也越来越受关注。

卷积神经网络中的卷积层是对图像的一个邻域进行卷积得到图像的邻域特征。<span style="color:red;">图像的一个邻域进行采样得到的图像的邻域特征？是这样吗？之前好像没有看到过这个。</span>亚采样层就是使用 Pooling 函数技术将小邻域内的特征点整合得到新的特征。Pooling 函数确实起到了整合特征的作用。<span style="color:red;">嗯，这个倒是。</span>

池化操作是利用一个矩阵窗口在张量上进行扫描，将每个矩阵通过取最大值或者平均值等方法来减少元素的个数，最大值和平均值的方法可以使得特征提取拥有“平移不变性”，也就说图像有了几个像素的位移情况下，依然可以获得稳定的特征组合，平移不变性对于识别十分重要。<span style="color:red;">嗯，平移不变性，但是对于定位还是有点影响的吧，精确定位某种物体可以使用这种平移不变性吗？怎样才能即稳定的定位，又精确的定位？</span>

Pooling 函数的结果是特征减少，参数减少，但 Pooling 的目的并不仅在于此。Pooling 函数的目的是保持某种不变性（旋转、平移、伸缩等），常用的有 Mean-Pooling 函数，Max-Pooling 函数和 Stochastic-Pooling 函数三种。我们以一维平均池化为例进行说明：<span style="color:red;">Pooling 有保持旋转和伸缩的不变性吗？</span>


```
torch.nn.functional.avg_pool1d(input,kernel_size,stride=None,padding=0,ceil_mode=False,count_include_pad=True)
```

对由几个输入平面组成的输入信号进行一维平均池化。avg_pool1d，即对邻域内特征点只求平均：假设 Pooling 的窗大小是 2×2，在 Forward 的时候，就是在前面卷积完的输出上依次不重合地取 2×2 的窗平均，得到一个值就是当前 avg_pool1d 之后的值。<span style="color:red;">嗯，是的，但是为什么要在这个地方说呢？</span>

参数说明如下。

- kernel_size：窗口的大小。
- stride：窗口的步长，默认值为 kernel_size。
- padding：是否对输入数据填充 0。Padding可以将输入数据的区域改造成是卷积核大小的整数倍，这样对不满足卷积核大小的部分数据就不会忽略了。通过 Padding 参数指定填充区域的高度和宽度。
- ceil_mode：当为 True 时，将使用 Ceil 代替 Floor 来计算输出形状。
- count_include_pad：当为 True 时，将包括平均计算中的 0 填充。默认为 True。

<span style="color:red;">后面还是要重新整理下，简直了，只是把 API 超在这里而已。</span>

例子：

```py
import torch
import torch.nn.functional as F
import torch.nn as nn
import torch.autograd as Variable

input=Variable(torch.Tensor([[[1,2,3,4,5,6,7]]]))
res=F.avg_pool1d(input,kernel_size=3,stride=2)
print(res)
```

为什么输出的是这个？

```
Traceback (most recent call last):
  File "xxxxxx.py", line 6, in <module>
    input=Variable(torch.Tensor([[[1,2,3,4,5,6,7]]]))
TypeError: 'module' object is not callable
```




# 相关

- 《深度学习框架 Pytorch 快速开发与实战》
