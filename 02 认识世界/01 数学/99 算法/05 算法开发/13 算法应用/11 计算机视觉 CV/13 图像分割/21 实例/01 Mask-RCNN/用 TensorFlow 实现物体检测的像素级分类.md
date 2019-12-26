---
title: 用 TensorFlow 实现物体检测的像素级分类
toc: true
date: 2019-11-17
---
# TensorFlow 的物体检测 API 模型——Mask-RCNN


实例分割的方法有很多，TensorFlow 进行实例分割使用的是 Mask RCNN 算法。


<center>

![](http://images.iterate.site/blog/image/20191029014401.gif?imageslim)

</center>


##   **Mask R-CNN 算法概述**

![img](https://mmbiz.qpic.cn/mmbiz_jpg/bicdMLzImlibRnHcd2Twd3Dd6Thy2BxOeBcrdoWqVqCAfGXnjGlPIJYwsfTg9SwGibeHq779Xia4hZdIbREjV1ib5Pw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

Mask RCNN 算法架构

在介绍 Mask RCNN 之前，我们先来认识一下 Faster R-CNN。

Faster-RCNN 是一个用于物体检测的算法，它被分为两个阶段：第一阶段被称为「候选区域生成网络」（RPN），即生成候选物体的边框；第二阶段本质上是 Fast R-CNN 算法，即利用 RolPool 从每个候选边框获取对象特征，并执行分类和边框回归。这两个阶段所使用的特征可以共享，以更快地获得图像推算结果。

Faster R-CNN 对每个候选对象都有两个输出，一个是分类标签，另一个是对象边框。而 Mask-RCNN 就是在 Faster R-CNN 的两个输出的基础上，添加一个掩码的输出，该掩码是一个表示对象在边框中像素的二元掩码。但是这个新添加的掩码输出与原来的分类和边框输出不同，它需要物体更加精细的空间布局和位置信息。因此，Mask R-CNN 需要使用「全卷积神经网络」（FCN）。

![img](https://mmbiz.qpic.cn/mmbiz_jpg/bicdMLzImlibRnHcd2Twd3Dd6Thy2BxOeBW1Get8LlJqNSy7nfnNPtdRxWGqzrBFX8OGKvy6YUibGJT7ATC0tq6aQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

全卷积神经网络（FCN）的算法架构

「全卷积神经网络」是「语义分割」中十分常见的算法，它利用了不同区块的卷积和池化层，首先将一张图片解压至它原本大小的三十二分之一，然后在这种粒度水平下进行预测分类，最后使用向上采样和反卷积层将图片还原到原来的尺寸。

因此，Mask RCNN 可以说是将 Faster RCNN 和「全卷积神经网络」这两个网络合并起来，形成的一个庞大的网络架构。

###

##   **实操 Mask-RCNN**

- 图片测试

你可以利用 TensorFlow 网站上的共享代码来对 Mask RCNN 进行图片测试。以下是我的测试结果：

![img](https://mmbiz.qpic.cn/mmbiz_jpg/bicdMLzImlibRnHcd2Twd3Dd6Thy2BxOeBXWGrjkFe8GEU7Cryzzr0GKAaQflz5VPk1E1NGrBXE3kf2icr2rDxgicA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

Mask RCNN on Kites Image

- 视频测试

对我来说，最有意思的是用 YouTube 视频来测试这个模型。我从 YouTube 上下载了好几条视频，开始了视频测试。

视频测试的主要步骤：

> \1. 使用 VideoFileClip 功能从视频中提取出每个帧；
>
> \2. 使用 fl_image 功能对视频中截取的每张图片进行物体检测，然后用修改后的视频图片替换原本的视频图片；
>
> \3. 最后，将修改后的视频图像合并成一个新的视频。

GitHub地址为：https://github.com/priya-dwivedi/Deep-Learning/blob/master/Mask_RCNN/Mask_RCNN_Videos.ipynb

###

##   **Mask RCNN 的深入研究**

下一步的探索包括：

- 测试一个精确度更高的模型，观察两次测试结果的区别；
- 使用 TensorFlow 的物体检测 API 在定制的数据集上对 Mask RCNN 进行测试。


# 相关

- [用 TensorFlow 实现物体检测的像素级分类](https://mp.weixin.qq.com/s?__biz=MjM5ODU3OTIyOA==&mid=2650670816&idx=2&sn=747deed91407319e87c64879148d3dfd&chksm=bec23b9389b5b285d0d1b34ea7beaffa6b19a481a9e20ed8f2ccbed66d4e5e18347d9770563e&mpshare=1&scene=1&srcid=0421BigpEOThOUV7lF3t8cCn#rd)
