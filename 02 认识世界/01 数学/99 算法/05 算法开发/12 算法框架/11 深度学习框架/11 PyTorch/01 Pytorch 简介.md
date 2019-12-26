---
title: 01 Pytorch 简介
toc: true
date: 2019-05-28
---
# PyTorch简介

## PyTorch介绍

2017年 1 月份，Facebook开源了 PyTorch。PyTorch 由 Adam Paszke、Sam Gross与 Soumith Chintala 等人牵头开发，其成员来自 Facebook FAIR 和其他多家实验室。PyTorch 的前身是 Torch。Torch是一个科学计算框架，支持机器学习算法，易用而且提供高效的算法实现，这得益于 LuaJIT 和一个底层的 C 实现。Torch由卷积神经网络之父杨乐昆领导开发的框架，是 Facebook 的深度学习开发工具，于 2014 年开源后，迅速传播开来。由于 Torch 由 Lua 语言编写，Torch 在神经网络方面一直表现得很优异，但是 Lua 语言不怎么流行，从而开发者把 Torch 移植到 python 语言中，形成了 PyTorch。所以，也可以说 PyTorch 是 Torch 在 python 上的移植。其图标如图 1.3所示。<span style="color:red;">嗯嗯。</span>


<center>

![](http://images.iterate.site/blog/image/20190525/ufzEDgalWII4.png?imageslim){ width=55% }

</center>

目前 PyTorch 仅支持 Linux,Mac 平台的操作系统，截至 2017年 12 月 PyTorch 最新的版本是 PyTorch 0.2。其应用版本环境如图 1.4所示。

<center>

![](http://images.iterate.site/blog/image/20190525/9BS98dCIa5Fw.png?imageslim){ width=55% }

</center>

PyTorch 的官网地址：http://pytorch.org/，其官网界面如图 1.5所示。

<center>

![](http://images.iterate.site/blog/image/20190525/YotmSq81ol5G.png?imageslim){ width=55% }

</center>

python 作为功能十分强大的高级语言，擅长处理数据分析，拥有众多模块包，是众多数据分析爱好者的选择。PyTorch 支持动态图的创建。

现在的深度学习平台在定义模型的时候主要用两种方式：

- 静态图模型（Static computation graph）
- 动态图模型（Dynamic computation graph）

对比如下：

- 静态图定义的缺陷是在处理数据前必须定义好完整的一套模型，能够处理所有的边际情况。
- 动态图模型能够非常自由地定义模型。

<span style="color:red;">难道就这两点吗？静态图就不够好吗？而且，静态图不够自由吗？</span>

使用和重放 Tape recorder 可以零延迟或零成本地任意改变你的网络的行为。<span style="color:red;">Tape recorder 是什么？</span>动态图模型作为 NumPy 的替代者，使用强大的 GPU，支持 GPU的 Tensor 库，可以极大地加速计算。<span style="color:red;">什么是支持 GPU 的 Tensor 库？</span>PyTorch 的设计思路简单实用，PyTorch 将会直接指向代码定义的确切位置，节省开发者寻找 Bug 的时间。同时根据代码简介，可快速实现神经网络构建，还有 Lua 的社区支持，为 PyTorch 提供各种技术支持和交流。<span style="color:red;">嗯，看来 Lua 还是要掌握的。</span>

如果你使用 NumPy，那么你已经在使用 Tensors（也就是 ndarray）。PyTorch提供的 Tensors 支持 CPU 或 GPU，并为大量的计算提供加速。<span style="color:red;">是这样吗？使用 Numpy 的 ndarray 就相当于 Tensors 吗？</span>

Autograd 实现是非常重要的一个功能。变量和功能是相互关联的，可以建立一个无环图，编码一个完整的历史的计算，并且每个变量都有一个 grad_fn。<span style="color:red;">什么意思？Autograd 到底是干什么的？每个变量都有一个 grad_fn 是什么意思？</span>

PyTorch 提供的功能有强大的 N 维数组，提供大量索引、切片和置换的程序，通过 LuaJIT 实现神奇的 C 接口、线性算术程序、神经网络以及以能源为基础的模型。<span style="color:red;">什么是以能源为基础的模型？</span>

在 2016 年，谷歌开源了数值优化程序 TensorFlow，用 Tensor 的形式，在静态的神经网络上大大提高了运行效率，相比于 PyTorch，它提供了动态的神经网络计算图模块，方便用户随时改变神经网络的结构，同时也提供 GPU 加速，在不影响计算的情况下，实现快速搭建神经网络。许多大公司为了提高科研效率，选择简单而且方便的 python 作为开发工具，比如说谷歌，Facebook。并且 TensorFlow 和 PyTorch 都已经兼容 python 3.5，可以实现快速搭建自己的神经网络。但是 Torch缺乏 TensorFlow 的分布式应用程序管理框架，缺乏多种编程语言的 API，限制了开发人员，影响 PyTorch 在深度学习领域中的地位。<span style="color:red;">现在 PyTroch 还缺少 Tensorflow 的分布式应用程序管理框架吗？而且不是已经支持很多 语言了吗？</span>

PyTorch 支持卷积神经网络、循环神经网络以及长短期记忆网络。同时 PyTorch 提供了大量的图片数据方便用户进行实验，如图 1.6所示。

cifar10内置的图像数据：<span style="color:red;">这个是 PyTorch 内置的数据吗？不是吧？</span>

<center>

![](http://images.iterate.site/blog/image/20190525/SLjIAylgeMOR.png?imageslim){ width=55% }

</center>

我们来看一下 PyTorch 的模型运作流程图（如图 1.8所示）：从输入 input 到输出 output 很简洁，也很直观。

PyTorch的网络模型运作流程图：

<center>

![](http://images.iterate.site/blog/image/20190525/s5imfa5sQlyk.png?imageslim){ width=55% }

</center>

<span style="color:red;">上面这个图中的 i2o 和 i2h 是什么？而且，为什么 hidden 是这样连接的？是表征一个 LSTM 吗？</span>

图 1.9是 PyTorch 与 Torch 训练 RNN 模型按流程的对比图，PyTorch 相比于 Torch 而言更加简洁，如果你想创建一个递归网络，只需多次使用相同的线性层，无须考虑共享权重。<span style="color:red;">没有很明白？</span>


<center>

![](http://images.iterate.site/blog/image/20190525/so0YC5nMDtu2.png?imageslim){ width=55% }

</center>



由于 PyTorch 符合直觉、好理解、易用。越来越受广大程序员的喜爱。

## PyTorch API

查询 PyTorch 的 API，以及对外提供的 API 的详细使用方法，读者可以自己通过链接查询：http://pytorch.org/docs/0.3.0/。在此不再叙述。<span style="color:red;">API 也要总结进来。</span>





# 相关

- 《深度学习框架 PyTorch 快速开发与实战》
