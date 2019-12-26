---
title: 22 PyTorch 与 Tensorflow
toc: true
date: 2019-12-06
---
# PyTorch 与 Tensorflow

TensorFlow 和 Caffe 都是命令式的编程语言，而且是静态的，首先必须构建一个神经网络，然后一次又一次使用相同的结构，如果想要改变网络的结构，就必须从头开始。

但是对于PyTorch，通过反向求导技术，可以让你零延迟地任意改变神经网络的行为，而且其实现速度快。正是这一灵活性是PyTorch对比TensorFlow的最大优势。
