---
title: 02 Core ML
toc: true
date: 2019-08-31
---
Core ML是 2017 年 Apple 公司在 WWDC 上与 iOS11 同时发布的移动端机器学习框架，底层使用 Accelerate 和 Metal 分别调用 CPU 和 GPU。Core ML需要将你训练好的模型转化为 Core ML model，它的使用流程如下：

![mark](http://images.iterate.site/blog/image/20190829/acNaIKYMuXAp.png?imageslim)


在一年之后，也就是 2018 年 WWDC 上，Apple发布了 Core ML 2，主要改进就是通过权重量化等技术优化模型的大小，使用新的 Batch Predict API提高模型的预测速度，以及容许开发人员使用 MLCustomLayer 定制自己的 Core ML模型。

项目地址和相关学习资料如下：

```text
https://developer.apple.com/documentation/coreml
https://github.com/likedan/Awesome-CoreML-Models
```


# 相关

- [DeepLearning-500-questions](https://github.com/scutan90/DeepLearning-500-questions)
- [【移动端 DL 框架】当前主流的移动端深度学习框架一览](https://zhuanlan.zhihu.com/p/67117914)
