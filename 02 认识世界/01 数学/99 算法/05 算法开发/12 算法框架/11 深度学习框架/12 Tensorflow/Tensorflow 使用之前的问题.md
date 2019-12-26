---
title: Tensorflow 使用之前的问题
toc: true
date: 2018-06-12 13:45:12
---



# 相关：

********************************************************************************


## 缘由：


Tensorflow感觉是一个深度学习的大杀器，而且好像 keras 已经合并进去了，这样的话就必须把 Tensorflow 学好，但是在用之前还是有几个问题想确认下。


## 要点：




### 1.使用 Tensorflow 开发的流程是什么样子的？


用 Keras 来写模型，然后再用 TF 的 API 来实现分布式部署。（**从网上看到的，不确定**）


### 2.Tensorflow一定要用 GPU 吗？


不一定，Tensorflow有 CPU 版本，而且支持 GPU 运算的版本 ( 仅限 Linux) 需要 Cuda Toolkit 7.0 和 CUDNN 6.5 V2. ，具体见[下载与安装](http://wiki.jikexueyuan.com/project/tensorflow-zh/get_started/os_setup.html)。




## COMMENT：


**感觉还有很多可以补充的**


## 



