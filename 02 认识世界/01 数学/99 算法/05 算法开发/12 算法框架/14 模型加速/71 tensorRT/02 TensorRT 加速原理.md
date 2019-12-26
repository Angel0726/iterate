---
title: 02 TensorRT 加速原理
toc: true
date: 2019-08-31
---

# TensorRT 加速原理

https://blog.csdn.net/xh_hit/article/details/79769599

在计算资源并不丰富的嵌入式设备上，TensorRT之所以能加速神经网络的的推断主要得益于两点：

- 首先是 TensorRT 支持 int8 和 fp16 的计算，通过在减少计算量和保持精度之间达到一个理想的 trade-off，达到加速推断的目的。

- 更为重要的是 TensorRT 对于网络结构进行了重构和优化，主要体现在一下几个方面。

  (1) TensorRT通过解析网络模型将网络中无用的输出层消除以减小计算。

  (2) 对于网络结构的垂直整合，即将目前主流神经网络的 Conv、BN、Relu三个层融合为了一个层，例如将图 1 所示的常见的 Inception 结构重构为图 2 所示的网络结构。

  (3) 对于网络结构的水平组合，水平组合是指将输入为相同张量和执行相同操作的层融合一起，例如图 2 向图 3 的转化。

<center>

![](http://images.iterate.site/blog/image/20190722/uDHxHbEuLcW9.png?imageslim){ width=55% }

</center>


<center>

![](http://images.iterate.site/blog/image/20190722/6HnqYP2wTzEI.png?imageslim){ width=55% }

</center>


<center>

![](http://images.iterate.site/blog/image/20190722/DokxJcd2ClPQ.png?imageslim){ width=55% }

</center>


以上 3 步即是 TensorRT 对于所部署的深度学习网络的优化和重构，根据其优化和重构策略，第一和第二步适用于所有的网络架构，但是第三步则对于含有 Inception 结构的神经网络加速效果最为明显。

Tips: 想更好地利用 TensorRT 加速网络推断，可在基础网络中多采用 Inception 模型结构，充分发挥 TensorRT 的优势。
