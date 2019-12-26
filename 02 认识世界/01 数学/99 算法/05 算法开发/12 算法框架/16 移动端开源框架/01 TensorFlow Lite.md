---
title: 01 TensorFlow Lite
toc: true
date: 2019-08-31
---

# TensorFlow Lite

TensorFlow Lite使用 Android Neural Networks API，默认调用 CPU，目前最新的版本已经支持 GPU。

项目地址和相关学习资源如下。

```text
https://tensorflow.google.cn/lite/
https://github.com/amitshekhariitbhu/Android-TensorFlow-Lite-Example
```




３、GitHub地址：https://github.com/tensorflow/tensorflow/tree/master/tensorflow/contrib/lite

４、简介：

Google 表示 Lite 版本 TensorFlow 是 TensorFlow Mobile 的一个延伸版本。此前，通过 TensorFlow Mobile API，TensorFlow已经支持手机上的模型嵌入式部署。TensorFlow Lite应该被视为 TensorFlow Mobile的升级版。

TensorFlow Lite可以与 Android 8.1中发布的神经网络 API 完美配合，即便在没有硬件加速时也能调用 CPU 处理，确保模型在不同设备上的运行。 而 Android 端版本演进的控制权是掌握在谷歌手中的，从长期看，TensorFlow Lite会得到 Android 系统层面上的支持。

5、架构：

<center>

![](http://images.iterate.site/blog/image/20190722/k8v7ccJBUbtu.jpg?imageslim){ width=55% }

</center>


其组件包括：

- TensorFlow 模型（TensorFlow Model）：保存在磁盘中的训练模型。
- TensorFlow Lite 转化器（TensorFlow Lite Converter）：将模型转换成 TensorFlow Lite 文件格式的项目。
- TensorFlow Lite 模型文件（TensorFlow Lite Model File）：基于 FlatBuffers，适配最大速度和最小规模的模型。



6、移动端开发步骤：

Android Studio 3.0, SDK Version API26, NDK Version 14

步骤：
1. 将此项目导入到 Android Studio：
    https://github.com/tensorflow/tensorflow/tree/master/tensorflow/contrib/lite/java/demo

2. 下载移动端的模型（model）和标签数据（lables）：
    https://storage.googleapis.com/download.tensorflow.org/models/tflite/mobilenet_v1_224_android_quant_2017_11_08.zip

3. 下载完成解压 mobilenet_v1_224_android_quant_2017_11_08.zip文件得到一个 xxx.tflite和 labes.txt文件，分别是模型和标签文件，并且把这两个文件复制到 assets 文件夹下。

4. 构建 app，run……


17.7.9 TensorFlow Lite和 TensorFlow Mobile的区别？

- TensorFlow Lite是 TensorFlow Mobile的进化版。
- 在大多数情况下，TensorFlow Lite拥有跟小的二进制大小，更少的依赖以及更好的性能。
- 相比 TensorFlow Mobile是对完整 TensorFlow 的裁减，TensorFlow Lite基本就是重新实现了。从内部实现来说，在 TensorFlow 内核最基本的 OP，Context等数据结构，都是新的。从外在表现来说，模型文件从 PB 格式改成了 FlatBuffers 格式，TensorFlow的 size 有大幅度优化，降至 300K，然后提供一个 converter 将普通 TensorFlow 模型转化成 TensorFlow Lite需要的格式。因此，无论从哪方面看，TensorFlow Lite都是一个新的实现方案。




# 相关

- [DeepLearning-500-questions](https://github.com/scutan90/DeepLearning-500-questions)
- [【移动端 DL 框架】当前主流的移动端深度学习框架一览](https://zhuanlan.zhihu.com/p/67117914)
