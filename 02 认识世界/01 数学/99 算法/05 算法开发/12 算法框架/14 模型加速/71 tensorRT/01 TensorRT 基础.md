---
title: 01 TensorRT 基础
toc: true
date: 2019-08-31
---
# TensorRT基础

TensorRT 是 NVIDIA 推出的深度学习优化加速工具，采用的原理如下图所示，具体可参考[3] [4]：

<center>

![mark](http://images.iterate.site/blog/image/20190829/5IeV8p3ejnJg.png?imageslim)

</center>

TensorRT能够优化重构由不同深度学习框架训练的深度学习模型：

对于 Caffe 与 TensorFlow 训练的模型，若包含的操作都是 TensorRT 支持的，则可以直接由 TensorRT 优化重构；
对于 MXnet, PyTorch或其他框架训练的模型，若包含的操作都是 TensorRT 支持的，可以采用 TensorRT API重建网络结构，并间接优化重构；

其他框架训练的模型，转换为 ONNX 中间格式后，若包含的操作是 TensorRT 支持的，可采用 TensorRT-ONNX接口予以优化 [27]；

若训练的网络模型包含 TensorRT 不支持的操作：

TensorFlow模型可通过 tf.contrib.tensorrt转换，其中不支持的操作会保留为 TensorFlow 计算节点；MXNet也支持类似的计算图转换方式；

不支持的操作可通过 Plugin API实现自定义并添加进 TensorRT 计算图，例如 Faster Transformer的自定义扩展 [26]；

将深度网络划分为两个部分，一部分包含的操作都是 TensorRT 支持的，可以转换为 TensorRT 计算图。另一部则采用其他框架实现，如 MXnet 或 PyTorch；

TensorRT的 int8 量化需要校准（calibration）数据集，一般至少包含 1000 个样本（反映真实应用场景），且要求 GPU 的计算功能集 sm >= 6.1；



# 相关

- []
