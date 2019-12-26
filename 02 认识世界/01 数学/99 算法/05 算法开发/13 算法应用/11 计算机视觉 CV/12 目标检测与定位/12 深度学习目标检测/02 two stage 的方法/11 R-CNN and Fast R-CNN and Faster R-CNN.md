---
title: 11 R-CNN and Fast R-CNN and Faster R-CNN
toc: true
date: 2018-09-22
---
# R-CNN

论文地址：

http://www.cv-foundation.org/openaccess/content_cvpr_2014/papers/Girshick_Rich_Feature_Hierarchies_2014_CVPR_paper.pdf



2014年加州大学伯克利分校的 Ross B. Girshick 提出 R-CNN 算法，其在效果上超越同期的 Yann Lecun 提出的端到端方法 OverFeat 算法，其算法结构也成为后续 two stage 的经典结构。

R-CNN 算法利用选择性搜索（Selective Search）算法评测相邻图像子块的特征相似度，通过对合并后的相似图像区域打分，选择出感兴趣区域的候选框作为样本输入到卷积神经网络结构内部，由网络学习候选框和标定框组成的正负样本特征，形成对应的特征向量，再由支持向量机设计分类器对特征向量分类，最后对候选框以及标定框完成边框回归操作达到目标检测的定位目的。


虽然 R-CNN算法相较于传统目标检测算法取得了 50% 的性能提升，但其也有缺陷存在：训练网络的正负样本候选区域由传统算法生成，使得算法速度受到限制；卷积神经网络需要分别对每一个生成的候选区域进行一次特征提取，实际存在大量的重复运算，制约了算法性能。


# Fast R-CNN

论文地址：

 http://www.cv-foundation.org/openaccess/content_iccv_2015/papers/Girshick_Fast_R-CNN_ICCV_2015_paper.pdf


针对 SPP-Net 算法的问题，2015年微软研究院的 Ross B. Girshick 又提出一种改进的 Fast R-CNN 算法，借鉴 SPP-Net 算法结构，设计一种 ROI pooling 的池化层结构，有效解决 R-CNN 算法必须将图像区域剪裁、缩放到相同尺寸大小的操作。


提出多任务损失函数思想，将分类损失和边框回归损失结合统一训练学习，并输出对应分类和边框坐标，不再需要额外的硬盘空间来存储中间层的特征，梯度能够通过 RoI Pooling 层直接传播。


但是其仍然没有摆脱选择性搜索算法生成正负样本候选框的问题。






# Faster R-CNN

论文地址：

https://arxiv.org/pdf/1506.01497.pdf


为了解决 Fast R-CNN算法缺陷，使得算法实现 two stage 的全网络结构，2015年微软研究院的任少庆、何恺明以及 Ross B Girshick等人又提出了 Faster R-CNN 算法。


设计辅助生成样本的 RPN（Region Proposal Networks）网络，将算法结构分为两个部分，先由 RPN 网络判断候选框是否为目标，再经分类定位的多任务损失判断目标类型，整个网络流程都能共享卷积神经网络提取的的特征信息，节约计算成本，且解决 Fast R-CNN 算法生成正负样本候选框速度慢的问题，同时避免候选框提取过多导致算法准确率下降。

但是由于 RPN 网络可在固定尺寸的卷积特征图中生成多尺寸的候选框，导致出现可变目标尺寸和固定感受野不一致的现象。<span style="color:red;">没明白。</span>




看到微信群里有人讨论 RPN，有人问大的图像能输入到 RPN 里面吗，然后有人说 YOLO 和 R-CNN 系列方法不一样，只要求图像的一个边是 32 倍，对图像的大小要求没有那么高。<span style="color:red;">对，我也想问，大的图像到底要怎么处理？直接缩到 227*227 不好吧？如果用大尺寸来输入，那么又没有足够的训练资源，别的网络拿过来又不能用了，怎么办？</span>


对于这块我理解的也不够，因此还是要好好总结下的。







## 需要消化的


- [faster-rcnn 之 RPN网络的结构解析](https://blog.csdn.net/sloanqin/article/details/51545125)
- [faster-rcnn中，对 RPN 的理解](https://blog.csdn.net/ying86615791/article/details/72788414)

- Faster R-CNN: Towards Real-Time Object Detection with Region Proposal Networks http://papers.nips.cc/paper/5638-faster-r-cnn-towards-real-time-object-detection-with-region-proposal-networks.pdf
