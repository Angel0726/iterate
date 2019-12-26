---
title: 07 QNNPACK
toc: true
date: 2019-08-31
---


# QNNPACK


QNNPACK是 Facebook 在 2018 年发布的 int8 量化低精度高性能开源框架，全称 Quantized Neural Network PACKage，用于手机端神经网络计算的加速，已经被整合到 PyTorch 1.0中，在 Caffe2 里就能直接使用。

![img](https://pic4.zhimg.com/80/v2-8450e9736d639fb60e11c3265a500997_hd.jpg)

这个框架可以为很多运算加速，比如 DW 卷积 (Depthwise Convolution) ，目前支持的列表如下：

![img](https://pic4.zhimg.com/80/v2-953e3473103787cb0327a1ceed027abf_hd.jpg)

项目地址如下。

```text
https://github.com/pytorch/QNNPACK
```



３、GitHub地址：https://github.com/pytorch/QNNPACK　　　　

４、特点：　　　

１）低密度卷积优化函数库；　　　

　	２）可在手机上实时运行 Mask R-CNN 和 DensePose;

３） 能在性能受限的移动设备中用 100ms 以内的时间实施图像分类；　　　

5、QNNPACK 如何提高效率？

1)**QNNPACK 使用与安卓神经网络 API 兼容的线性量化方案**

QNNPACK 的输入矩阵来自低精度、移动专用的计算机视觉模型。其它库在计算 A 和 B 矩阵相乘时，重新打包 A 和 B 矩阵以更好地利用缓存层次结构，希望在大量计算中分摊打包开销，QNNPACK 删除所有计算非必需的内存转换，针对 A和 B 矩阵相乘适用于一级缓存的情况进行了优化。

<center>

![](http://images.iterate.site/blog/image/20190722/z4WoHFj0i7Sd.jpeg?imageslim){ width=55% }

</center>


1）优化了 L1 缓存计算，不需要输出中间结果，直接输出最终结果，节省内存带宽和缓存占用。

具体分析：

- 常规实现：在量化矩阵-矩阵乘法中，8位整数的乘积通常会被累加至 32 位的中间结果中，随后重新量化以产生 8 位的输出。遇到大矩阵尺寸时，比如有时 K 太大，A和 B 的面板无法直接转入缓存，此时，需利用缓存层次结构，借助 GEMM 将 A 和 B 的面板沿着 K 维分割成固定大小的子面板，以便于每个子面板都能适应 L1 缓存，随后为每个子面板调用微内核。这一缓存优化需要 PDOT 为内核输出 32  位中间结果，最终将它们相加并重新量化为 8 位整数。
- 优化实现：由于  ONNPACK 对于面板 A 和 B 总是适应 L1 缓存的移动神经网络进行了优化，因此它在调用微内核时处理整个 A 和 B  的面板。而由于无需在微内核之外积累 32 位的中间结果，QNNPACK 会将 32 位的中间结果整合进微内核中并写出 8  位值，这节省了内存带宽和缓存占用。

<center>

![](http://images.iterate.site/blog/image/20190722/oq38RiYqWSTj.jpeg?imageslim){ width=55% }

</center>


2）取消了矩阵 A 的重新打包。

- 常规实现：

		矩阵 B 包含静态权重，可以一次性转换成任何内存布局，但矩阵  A 包含卷积输入，每次推理运行都会改变。因此，重新打包矩阵 A 在每次运行时都会产生开销。尽管存在开销，传统的 GEMM实现还是出于以下两个原因对矩阵 A 进行重新打包：

		a 缓存关联性及微内核效率受限。如果不重新打包，微内核将不得不读取被潜在的大跨距隔开的几行 A。如果这个跨距恰好是 2 的许多次幂的倍数，面板中不同行 A  的元素可能会落入同一缓存集中。如果冲突的行数超过了缓存关联性，它们就会相互驱逐，性能也会大幅下降。

		b 打包对微内核效率的影响与当前所有移动处理器支持的  SIMD  向量指令的使用密切相关。这些指令加载、存储或者计算小型的固定大小元素向量，而不是单个标量（scalar）。在矩阵相乘中，充分利用向量指令达到高性能很重要。在传统的  GEMM 实现中，微内核把 MR 元素重新打包到向量暂存器里的 MR 线路中。

- 优化实现：

		a 当面板适配一级缓存时，不会存在缓存关联性及微内核效率受限的问题。

		b 在 QNNPACK 实现中，MR  元素在存储中不是连续的，微内核需要把它们加载到不同的向量暂存器中。越来越大的暂存器压力迫使 QNNPACK 使用较小的 MRxNR  拼贴，但实际上这种差异很小，而且可以通过消除打包开销来补偿。例如，在 32 位 ARM 架构上，QNNPACK 使用 4×8 微内核，其中  57% 的向量指令是乘-加；另一方面，gemmlowp 库使用效率稍高的 4×12 微内核，其中 60% 的向量指令是乘-加。微内核加载 A  的多个行，乘以 B 的满列，结果相加，然后完成再量化并记下量化和。A 和 B 的元素被量化为 8 位整数，但乘积结果相加到 32 位。大部分  ARM 和 ARM64 处理器没有直接完成这一运算的指令，所以它必须分解为多个支持运算。QNNPACK  提供微内核的两个版本，其不同之处在于用于乘以 8 位值并将它们累加到 32 位的指令序列。

2)**从矩阵相乘到卷积**

<center>

![](http://images.iterate.site/blog/image/20190722/QilGXIYmoHaS.jpeg?imageslim){ width=55% }

</center>


传统实现：

简单的 1×1  卷积可直接映射到矩阵相乘

但对于具备较大卷积核、padding 或子采样（步幅）的卷积而言则并非如此。但是，这些较复杂的卷积能够通过记忆变换  im2col 映射到矩阵相乘。对于每个输出像素，im2col 复制输入图像的图像块并将其计算为 2D 矩阵。由于每个输出像素都受 KHxKWxC  输入像素值的影响（KH 和 KW 分别指卷积核的高度和宽度，C 指输入图像中的通道数），因此该矩阵的大小是输入图像的 KHxKW  倍，im2col 给内存占用和性能都带来了一定的开销。和 Caffe 一样，大部分深度学习框架转而使用基于 im2col  的实现，利用现有的高度优化矩阵相乘库来执行卷积操作。

优化实现：

Facebook  研究者在 QNNPACK 中实现了一种更高效的算法。

- 他们没有变换卷积输入使其适应矩阵相乘的实现，而是调整 PDOT 微内核的实现，在运行中执行  im2col 变换。这样就无需将输入张量的实际输入复制到 im2col 缓存，而是使用输入像素行的指针设置 indirection  buffer，输入像素与每个输出像素的计算有关。
- 研究者还修改了矩阵相乘微内核，以便从 indirection buffer  加载虚构矩阵（imaginary matrix）A 的行指针，indirection buffer 通常比 im2col buffer  小得多。
- 此外，如果两次推断运行的输入张量存储位置不变，则 indirection buffer  还可使用输入张量行的指针进行初始化，然后在多次推断运行中重新使用。研究者观察到具备 indirection buffer 的微内核不仅消除了  im2col 变换的开销，其性能也比矩阵相乘微内核略好（可能由于输入行在计算不同输出像素时被重用）。

3)**深度卷积**

<center>

![](http://images.iterate.site/blog/image/20190722/M6x8JaLj897M.jpeg?imageslim){ width=55% }

</center>


分组卷积（grouped   convolution）将输入和输出通道分割成多组，然后对每个组进行分别处理。在有限条件下，当组数等于通道数时，该卷积就是深度卷积，常用于当前的神经网络架构中。深度卷积对每个通道分别执行空间滤波，展示了与正常卷积非常不同的计算模式。因此，通常要向深度卷积提供单独实现，QNNPACK  包括一个高度优化版本 3×3 深度卷积。

深度卷积的传统实现是每次都在卷积核元素上迭代，然后将一个卷积核行和一个输入行的结果累加到输出行。对于一个  3×3 的深度卷积，此类实现将把每个输出行更新 9 次。在 QNNPACK 中，研究者计算所有 3×3 卷积核行和 3×3  输入行的结果，一次性累加到输出行，然后再处理下个输出行。

QNNPACK  实现高性能的关键因素在于完美利用通用暂存器（GPR）来展开卷积核元素上的循环，同时避免在 hot loop 中重新加载地址寄存器。32-bit  ARM 架构将实现限制在 14 个 GPR。在 3×3 深度卷积中，需要读取 9 个输入行和 9 个卷积核行。这意味着如果想完全展开循环必须存储  18 个地址。然而，实践中推断时卷积核不会发生变化。因此 Facebook 研究者使用之前在 CxKHxKW 中的滤波器，将它们封装进  [C/8]xKWxKHx8，这样就可以仅使用具备地址增量（address increment）的一个 GPR 访问所有滤波器。（研究者使用数字 8  的原因在于，在一个命令中加载 8 个元素然后减去零，在 128-bit NEON 暂存器中生成 8 个 16-bit 值。）然后使用 9  个输入行指针，指针将滤波器重新装进 10 个 GPR，完全展开滤波器元素上的循环。64-bit ARM 架构相比 32-bit 架构，GPR  的数量翻了一倍。QNNPACK 利用额外的 ARM64 GPR，一次性存储 3×5 输入行的指针，并计算 3 个输出行。



7、性能优势：

测试结果显示出 QNNPACK 在端到端基准上的性能优势。在量化当前最优 MobileNetV2 架构上，基于 QNNPACK 的 Caffe2 算子的速度大约是 TensorFlow Lite 速度的 2 倍，在多种手机上都是如此。除了 QNNPACK 之外，Facebook 还开源了 Caffe2 quantized MobileNet v2 模型，其 top-1 准确率比相应的 TensorFlow 模型高出 1.3%。

**MobileNetV1**

MobileNetV1  架构在使用深度卷积（depthwise convolution）使模型更适合移动设备方面具备开创性。MobileNetV1 包括几乎整个  1×1 卷积和 3×3 卷积。Facebook 研究者将量化 MobileNetV1 模型从 TensorFlow Lite 转换而来，并在  TensorFlow Lite 和 QNNPACK 的 32-bit ARM 设备上对 MobileNetV1 进行基准测试。二者运行时均使用 4  线程，研究者观察到 QNNPACK 的运行速度几何平均值是 TensorFlow Lite 的 1.8 倍。

<center>

![](http://images.iterate.site/blog/image/20190722/uLwdYYJMMa0s.jpg?imageslim){ width=55% }

</center>


**MobileNetV2**

作为移动视觉任务的当前最优架构之一，MobileNetV2  引入了瓶颈构造块和瓶颈之间的捷径连接。研究者在 MobileNetV2 分类模型的量化版上对比基于 QNNPACK 的 Caffe2 算子和  TensorFlow Lite 实现。使用的量化 Caffe2 MobileNetV2 模型已开源，量化 TensorFlow Lite  模型来自官方库：https://github.com/tensorflow/tensorflow/blob/master/tensorflow/contrib/lite/g3doc/models.md。下表展示了二者在常用测试集上的  top1 准确率：

<center>

![](http://images.iterate.site/blog/image/20190722/LLu69BcAfRIt.jpg?imageslim){ width=55% }

</center>


Facebook 研究者利用这些模型建立了 Facebook AI 性能评估平台（https://github.com/facebook/FAI-PEP）的基准，该基准基于 32-bit ARM 环境的大量手机设备。对于 TensorFlow Lite 线程设置，研究者尝试了一到四个线程，并报告了最快速的结果。结果显示 TensorFlow Lite 使用四线程的性能最优，因此后续研究中使用四线程来对比 TensorFlow Lite 和 QNNPACK。下表展示了结果，以及在典型智能手机和高端机上，基于 QNNPACK 的算子速度比 TensorFlow Lite 快得多。

<center>

![](http://images.iterate.site/blog/image/20190722/dqTlghF9W81Y.jpg?imageslim){ width=55% }

</center>


Facebook开源高性能内核库 QNNPACK
https://baijiahao.baidu.com/s?id=1615725346726413945&wfr=spider&for=pc
http://www.sohu.com/a/272158070_610300



支持移动端深度学习的几种开源框架
https://blog.csdn.net/zchang81/article/details/74280019









# 相关

- [DeepLearning-500-questions](https://github.com/scutan90/DeepLearning-500-questions)
- [【移动端 DL 框架】当前主流的移动端深度学习框架一览](https://zhuanlan.zhihu.com/p/67117914)
