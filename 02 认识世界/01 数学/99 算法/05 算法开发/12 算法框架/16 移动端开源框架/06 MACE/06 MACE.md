---
title: 06 MACE
toc: true
date: 2019-08-31
---

# MACE Mobile AI Compute Engine


MACE是 2018 年小米在开源中国开源世界高峰论坛中宣布开源的移动端框架，以 OpenCL 和汇编作为底层算子，提供了异构加速可以方便在不同的硬件上运行模型，同时支持各种框架的模型转换。


项目地址和相关学习资源如下：

```
https://github.com/XiaoMi/mace
https://github.com/XiaoMi/mace-models
```

　

３、GitHub地址：https://github.com/XiaoMi/mace

４、简介：Mobile AI Compute Engine (MACE) 是一个专为移动端异构计算设备优化的深度学习前向预测框架。
MACE覆盖了常见的移动端计算设备（CPU，GPU和 DSP），并且提供了完整的工具链和文档，用户借助 MACE 能够很方便地在移动端部署深度学习模型。MACE已经在小米内部广泛使用并且被充分验证具有业界领先的性能和稳定性。

5、MACE的基本框架：

<center>

![](http://images.iterate.site/blog/image/20190722/npXDYRHunYx3.png?imageslim){ width=55% }

</center>


**MACE Model**

MACE定义了自有的模型格式（类似于 Caffe2），通过 MACE 提供的工具可以将 Caffe 和 TensorFlow 的模型 转为 MACE 模型。

**MACE Interpreter**

MACE Interpreter主要负责解析运行神经网络图（DAG）并管理网络中的 Tensors。

**Runtime**

CPU/GPU/DSP Runtime对应于各个计算设备的算子实现。



6、MACE使用的基本流程

<center>

![](http://images.iterate.site/blog/image/20190722/Heif1tpmw5Ej.png?imageslim){ width=55% }

</center>


**1. 配置模型部署文件(.yml)**

模型部署文件详细描述了需要部署的模型以及生成库的信息，MACE根据该文件最终生成对应的库文件。

**2.编译 MACE 库**

编译 MACE 的静态库或者动态库。

**3.转换模型**

将 TensorFlow 或者 Caffe的模型转为 MACE 的模型。

**4.1. 部署**

根据不同使用目的集成 Build 阶段生成的库文件，然后调用 MACE 相应的接口执行模型。

**4.2. 命令行运行**

MACE提供了命令行工具，可以在命令行运行模型，可以用来测试模型运行时间，内存占用和正确性。

**4.3. Benchmark**

MACE提供了命令行 benchmark 工具，可以细粒度的查看模型中所涉及的所有算子的运行时间。



7、MACE在哪些角度进行了优化?

**MACE** 专为移动端异构计算平台优化的神经网络计算框架。主要从以下的角度做了专门的优化：

* 性能
  * 代码经过 NEON 指令，OpenCL以及 Hexagon HVX专门优化，并且采用
    [Winograd算法](https://arxiv.org/abs/1509.09308)来进行卷积操作的加速。
    此外，还对启动速度进行了专门的优化。
* 功耗

  * 支持芯片的功耗管理，例如 ARM 的 big.LITTLE调度，以及高通 Adreno GPU功耗选项。
* 系统响应
  * 支持自动拆解长时间的 OpenCL 计算任务，来保证 UI 渲染任务能够做到较好的抢占调度，
    从而保证系统 UI 的相应和用户体验。
* 内存占用
  * 通过运用内存依赖分析技术，以及内存复用，减少内存的占用。另外，保持尽量少的外部
    依赖，保证代码尺寸精简。
* 模型加密与保护

  * 模型保护是重要设计目标之一。支持将模型转换成 c++代码，以及关键常量字符混淆，增加逆向的难度。
* 硬件支持范围
  * 支持高通，联发科，以及松果等系列芯片的 CPU，GPU与 DSP(目前仅支持 Hexagon)计算加速。
  * 同时支持在具有 POSIX 接口的系统的 CPU 上运行。


8、性能对比：

MACE 支持 TensorFlow 和 Caffe 模型，提供转换工具，可以将训练好的模型转换成专有的模型数据文件，同时还可以选择将模型转换成 c++代码，支持生成动态库或者静态库，提高模型保密性。

<center>

![](http://images.iterate.site/blog/image/20190722/CdPoUtLI0JxN.jpg?imageslim){ width=55% }

</center>



# 相关

- [DeepLearning-500-questions](https://github.com/scutan90/DeepLearning-500-questions)
- [【移动端 DL 框架】当前主流的移动端深度学习框架一览](https://zhuanlan.zhihu.com/p/67117914)
