---
title: 07 R-SSD
toc: true
date: 2018-09-22
---
# R-SSD

2017年首尔大学提出了 R-SSD算法，解决了 SSD 算法中不同层 feature map都是独立作为分类网络的输入，容易出现相同物体被不同大小的框同时检测出来的情况，还有对小尺寸物体的检测效果比较差的情况。

R-SSD算法一方面利用分类网络增加不同层之间的 feature map联系，减少重复框的出现；
另一方面增加 feature pyramid中 feature map的个数，使其可以检测更多的小尺寸物体。特征融合方式采用同时利用 pooling 和 deconvolution 进行特征融合，这种特征融合方式使得融合后每一层的 feature map个数都相同，因此可以共用部分参数，具体来讲就是 default boxes的参数共享。
![](http://images.iterate.site/blog/image/180922/ca9lLf3057.png?imageslim){ width=55% }
