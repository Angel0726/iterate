---
title: 06 MR-CNN
toc: true
date: 2018-09-22
---
# MR-CNN


2015年巴黎科技大学提出 MR-CNN 算法，结合样本区域本身的特征，利用样本区域周围采样的特征和图像分割的特征来提高识别率，可将检测问题分解为分类和定位问题。

![](http://images.iterate.site/blog/image/180922/0EfgDdLe1E.png?imageslim){ width=55% }



分类问题由 Multi-Region CNN Model和 Semantic Segmentation-Aware CNN Model组成。前者的候选框由 Selective Search得到，对于每一个样本区域，取 10 个区域分别提取特征后拼接，这样可以强制网络捕捉物体的不同方面，同时可以增强网络对于定位不准确的敏感性，其中 adaptive max pooling即 ROI max pooling；后者使用 FCN 进行目标分割，将最后一层的 feature map和前者产生的 feature map拼接，作为最后的 feature map。

为了精确定位，采用三种样本边框修正方法，分别为 Bbox regression、Iterative localization以及 Bounding box voting。Bbox regression：在 Multi-Region CNN Model中整幅图经过网路的最后一层卷积层后，接一个 Bbox regression layer，与 RPN 不同，此处的 regression layer是两层 FC 以及一层 prediction layer，为了防止 Selective Search得到的框过于贴近物体而导致无法很好的框定物体，将候选框扩大为原来的 1.3倍再做。Iterative localization：初始的框是 Selective Search得到的框，然后用已有的分类模型对框做出估值，低于给定阈值的框被筛掉，剩下的框用 Bbox regression的方法调整大小，并迭代筛选。Bounding box voting：首先对经过 Iterative localization处理后的框应用 NMS, IOU = 0.3，得到检测结果，然后对于每一个框，用每一个和其同一类的而且 IOU >0.5的框加权坐标，得到最后的目标样本框。
