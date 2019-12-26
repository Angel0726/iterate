---
title: 01 自动求导机制介绍 autograd
toc: true
date: 2019-12-06
---
# 自动求导机制介绍 autograd

PyTorch 中所有神经网络的核心是 `autograd` 包。`torch.Tensor` 是 `autograd` 包的核心类。

`autograd` 包介绍：


- 它为张量上的所有操作提供了自动求导。
- 它是一个在运行时定义的框架，这意味着反向传播是根据你的代码来确定如何运行，并且每次迭代可以是不同的。<span style="color:red;">什么是运行时框架？是怎么实现的运行时框架？每次迭代可以是不同的是什么意思？</span>



# 相关

- [pytorch-handbook](https://github.com/zergtant/pytorch-handbook)
- `autograd` 和 `Function` 的官方文档 https://pytorch.org/docs/autograd
