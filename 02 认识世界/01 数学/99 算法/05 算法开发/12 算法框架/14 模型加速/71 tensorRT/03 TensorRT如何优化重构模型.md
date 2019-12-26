---
title: 03 TensorRT如何优化重构模型
toc: true
date: 2019-08-31
---

# TensorRT如何优化重构模型？

| 条件                                     | 方法                                                         |
| ---------------------------------------- | ------------------------------------------------------------ |
| 若训练的网络模型包含 TensorRT 支持的操作   | 1、对于 Caffe 与 TensorFlow 训练的模型，若包含的操作都是 TensorRT 支持的，则可以直接由 TensorRT 优化重构 |
|                                          | 2、对于 MXnet, PyTorch或其他框架训练的模型，若包含的操作都是 TensorRT 支持的，可以采用 TensorRT API重建网络结构，并间接优化重构； |
| 若训练的网络模型包含 TensorRT 不支持的操作 | 1、TensorFlow模型可通过 tf.contrib.tensorrt转换，其中不支持的操作会保留为 TensorFlow 计算节点； |
|                                          | 2、不支持的操作可通过 Plugin API实现自定义并添加进 TensorRT 计算图； |
|                                          | 3、将深度网络划分为两个部分，一部分包含的操作都是 TensorRT 支持的，可以转换为 TensorRT 计算图。另一部则采用其他框架实现，如 MXnet 或 PyTorch； |
