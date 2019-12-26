---
title: 04 TensorRT 的加速效果如何
toc: true
date: 2019-08-31
---


# TensorRT 加速效果如何？

以下是在 TitanX (Pascal)平台上，TensorRT对大型分类网络的优化加速效果：

|  Network  | Precision | Framework/GPU:TitanXP | Avg.Time(Batch=8,unit:ms) | Top1 Val.Acc.(ImageNet-1k) |
| :-------: | :-------: | :-------------------: | :-----------------------: | :------------------------: |
| Resnet50  |   fp32    |      TensorFlow       |           24.1            |           0.7374           |
| Resnet50  |   fp32    |         MXnet         |           15.7            |           0.7374           |
| Resnet50  |   fp32    |       TRT4.0.1        |           12.1            |           0.7374           |
| Resnet50  |   int8    |       TRT4.0.1        |             6             |           0.7226           |
| Resnet101 |   fp32    |      TensorFlow       |           36.7            |           0.7612           |
| Resnet101 |   fp32    |         MXnet         |           25.8            |           0.7612           |
| Resnet101 |   fp32    |       TRT4.0.1        |           19.3            |           0.7612           |
| Resnet101 |   int8    |       TRT4.0.1        |             9             |           0.7574           |
