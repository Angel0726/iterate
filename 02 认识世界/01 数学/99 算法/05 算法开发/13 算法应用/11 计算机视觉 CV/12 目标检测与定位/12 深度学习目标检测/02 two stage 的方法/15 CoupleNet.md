---
title: 15 CoupleNet
toc: true
date: 2018-09-22
---
# CoupleNet

针对 R-FCN算法没有考虑到 region proposal的全局信息和语义信息的问题，2017年中科院自动化所提出 CoupleNet 算法，其在原来 R-FCN的基础上引入了 proposal 的全局和语义信息，通过结合局部、全局以及语义的信息，提高了检测的精度。


CoupleNet结构利用三支并行网络实现检测，上面的支路网络使用原本的 R-FCN结构的位置敏感分布图提取目标的局部信息；中间的支路网络用于提取目标的全局信息，对于一个 region proposal，依次通过 K×K的 ROI Pooling，K×K的 conv 以及 1×1的 conv；下面的支路网络用于提取目标的语义信息，对于一个 region proposal，首先选择以这个 proposal 为中心，面积是原来 2 倍的 proposal，同样依次通过 K×K的 ROI Pooling，K×K的 conv 以及 1×1的 conv。最后先各自通过 1×1的 conv 调整激活值的尺寸，然后把 Local FCN和 Global FCN结果对应位置元素相加，再通过一个 softmax 实现分类。
