---
title: Rosetta
toc: true
date: 2019-11-17
---
# Rosetta

大规模图像文本提取和识别系统

- 论文来源：KDD 2018
- 论文作者：Facebook



Rosetta 每天可以处理 Facebook 社交网络上的数亿张图像，提取文字信息，为下游任务提供大量数字化资源，如图像搜索。

目前 Rosseta 已经作为云 API 被实现和部署到客户端应用程序中，并将逐渐覆盖所有客户。本文是 Rosseta 系统的技术解读，论文已被 KDD 2018 接收。


人们在社交网络上分享和获取信息的主要途径之一是视觉媒介，如照片和视频。近年来，上传至社交媒体的照片数量成指数级增长，每天可达数亿张 [27]，处理日渐增多的视觉信息成为一大技术挑战。图像理解的挑战之一是从图像中检索文本信息，也叫光学字符识别（OCR），表示将包含键入、印刷或场景文本的电子图像转换成机器编码文本的过程。从图像中获取此类文本信息很重要，因为这可以促进很多不同的应用，如图像搜索和推荐。



在 OCR 任务中，给出一张图像，OCR 系统可以准确地提取出印刷或嵌入图像中的文本。随着大量字体、语言、词典和其他语言变体（包括特殊符号、不在词典内的单词，以及 URL 和电邮 id 等特殊信息）出现在图像中，图像质量随着文本出现的背景不同而出现变化，OCR 任务的难度增大。另一个原因是每天上传至社交媒体、需要处理的图像规模非常巨大。由于下游应用的本质，人们对 OCR 任务的期待是实时处理，这要求我们花费大量时间优化系统各部分，以在合理的延迟时间内执行 OCR 任务。因此，OCR 任务的相关问题可以描述如下：构建稳健、准确率高的 OCR 系统，能够实时处理每天数以亿计的图像。



今天介绍的这篇论文展示了 Facebook 的可扩展 OCR 系统 Rosetta，该系统已经被实现和部署在生产中，并主导了 Facebook 内的下游应用。Rosetta 遵循当前最优 OCR 系统的架构，分为文本检测阶段和文本识别阶段两部分。文本检测方法基于 Faster-RCNN 模型 [24]，负责检测图像中包含文本的区域。文本识别方法使用全卷积字符识别模型，处理检测到的区域，并识别这些区域所包含的文本内容。图 1 展示了 Rosetta 生成的一些结果。



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gWiccFjVhMdUlpAUK4lzV6EMfaiaLsp8BJCpos8dvNXL4YfibiaZiaGh9TqGjPx6NcIXjxzKGbhlQgRiaglg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*图 1：使用 Rosetta 系统的 OCR 文本识别。基于 Faster-RCNN 的方法检测出单个单词，然后全卷积 CNN 生成每个单词的转录。*



**论文：Rosetta: Large Scale System for Text Detection and Recognition in Images**



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gWiccFjVhMdUlpAUK4lzV6EMflPm3rWictINdYsZ1icQqsykPFHSAtlH3NC155WEeorqbF6CQKStojFwA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



- 论文链接：http://www.kdd.org/kdd2018/accepted-papers/view/rosetta-large-scale-system-for-text-detection-and-recognition-in-images

- PDF链接：http://delivery.acm.org/10.1145/3220000/3219861/p71-borisyuk.pdf?ip=222.249.170.76&id=3219861&acc=OPENTOC&key=4D4702B0C3E38B35%2E4D4702B0C3E38B35%2E4D4702B0C3E38B35%2E054E54E275136550




**摘要**：本论文展示了一个已部署的可扩展光学字符识别（OCR）系统 Rosetta，该系统旨在 Facebook 每天上传图像数量级的图像处理。图像内容分享已经成为目前互联网用户在社交网络上最主要的信息交流方式，理解图像（包括其文本信息）对于搜索和推荐应用来说至关重要。我们展示了高效图像文本检测和识别的建模技术，介绍了 Rosetta 的系统架构。我们对现有技术进行了大量评估，解释了构建大规模 OCR 系统的有效实用方法，并基于 Rosetta 系统的开发和部署过程解释了特定组件的工作原理。



**3. 文本提取模型**



OCR 过程分两个独立步骤：检测和识别。第一步中，我们检测图像中有可能包含文本的矩形区域。第二步执行文本识别，即使用 CNN 对检测出的每一个区域中的文字进行识别和转录。这个流程有诸多好处，如分离训练过程和文本检测、识别模型的部署更新、并行运行文本识别，以及独立支持对不同语言的文本识别。详见图 2。

Figure 2: Two-step model architecture. The first step performs word detection based on Faster-RCNN. The second step performs word recognition using a fully convolutional model with CTC loss. The two models are trained independently.



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gWiccFjVhMdUlpAUK4lzV6EMfjuF2EvosWIibf8nRQvEvxsbIgial8KJTL1ERmOicMJGhpnN0DKib8vJ0OQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*图 2：模型架构（包含两个步骤）。第一步基于 Faster-RCNN 执行文本检测。第二步使用带 CTC 损失的全卷积模型执行文本识别。两个模型分开训练。*



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gWiccFjVhMdUlpAUK4lzV6EMfj5TMUXLykGJQqVlUqL2BRFD4LFUkN1ms5VGQibcRnKKg0qVx2Ta7kFA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*图 3：文本识别模型架构。*



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gWiccFjVhMdUlpAUK4lzV6EMfzZX6qrcvGcMQ3wxhCmeQRqQ4qtMLkzu35f4rR433Sw3zoeY5yEpIyg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*图 4：在 CTC 文本识别模型的训练中，学习率、图像宽度和最大单词长度的行为的可视化图示。*



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gWiccFjVhMdUlpAUK4lzV6EMfFaqLZOXf8CricEVfbOVUdXib6ZX9l5TQm7ibhSsvNy3Cxae3EAlXViaAlQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*算法 1：CTC 文本识别模型的训练过程。*



**4. 系统架构**



这部分主要介绍 Facebook 的实时大规模 OCR 系统 Rosetta 的系统架构。图 5 展示了 Rosetta 的架构。Rosetta 使用拉式模型（pull-based model），将客户端应用程序上传的图像（图 5 第一步）添加到分布式处理序列中。当资源可用时，Rosetta 中的这一推断器承担将图像添加到队列的工作，然后进行异步处理。执行添加队列工作时，用户可以注册回调。每次添加结束后，Rosetta 可以启用回调，实现下游应用对结果的直接使用。该处理序列已经得到优化，以适应高吞吐量和可用性，且使用 RocksDB [8] 保障持续性。这种拉式异步架构具备很多优势，包括更好的负载平衡、请求峰值场景中的限速情况（例如，Facebook 上图像上传数的激增），以及优化系统以适应高吞吐量的能力。



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gWiccFjVhMdUlpAUK4lzV6EMfOQFCpiaxXKbyEYjWiciaQZANwPKXicdIGjrj3Wjy5eiaJoO5WqO1k4JOEkQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*图 5：Rosetta 架构。*



Rosetta 内的在线图像处理流程包含以下步骤：



1. 图像被下载到 Rosetta 集群内的本地机器上，然后执行预处理，如调整图像大小和归一化。
2. 执行文本检测模型（图 5 第 4 步），获取图像中所有单词的位置信息（边界框坐标和得分）。
3. 将单词位置信息传输到文本识别模型（图 5 第 5 步），提取图像中所有单词区域中的字符。
4. 提取出的文本信息和文本区域被存储在 Facebook 的分布式图数据库 TAO [9] 中（图 5 第 6 步）。
5. 下游应用（如 Search）可以直接从 TAO 中获取图像对应的文本信息（图 5 第 7 步）。



**5. 实验**



我们对 Rosetta OCR 系统进行了大量评估。我们首先定义用于判断系统准确率和处理时间的指标，然后介绍用于训练和评估的数据集。我们遵循标准做法，在独立的留出数据集上进行模型训练和评估。我们介绍了模型评估过程，解释了为准确率和推断速度之间的权衡所做的设计决策。最后我们分享了在 Rosetta 开发和部署过程中学到的经验，这些经验帮助我们可以处理 Facebook 级别的图像。



**6. 部署**



Rosetta 服务已在 Facebook 中部署，为图像文本提取提供了一个云 API，处理每天上传至 Facebook 的海量图像。在 Rosetta 中，图像的大小被调整到 800px，然后传输到检测模型中，输出每个单词的边界框坐标。这些单词块被裁剪下来，将高度调整至 32px，保持原来的宽高比，最后使用识别模型进行处理。识别模型的推断运行时间取决于图像中检测到的单词数量。



Rosetta 服务已被逐渐应用到客户端应用程序中，部署计划包括每周增加服务于预定义内部用户的流量，然后向公众流量开放，从 1%、5%、10%、25%、40%、80%，直至 100%。我们继续评估资源使用，并渐进式地向处理机群增加更多机器，直到服务部署范围达到 100%。



Faster-RCNN 检测模型使用近期开源的 Detectron 框架 [5] 训练，它是基于 Caffe2 [2] 构建的。文本识别模型使用 PyTorch [1] 训练，因其在快速原型和序列建模场景中的灵活性。这两个模型都使用 Caffe2 部署到生产，使用中间 ONNX 格式 [4] 将文本识别模型从 PyTorch 转到 Caffe2。


# 相关

- [KDD 2018 | OCR神器来了！Facebook推出大规模图像文本提取系统Rosetta](https://mp.weixin.qq.com/s?__biz=MzU1NTUxNTM0Mg==&mid=2247489183&idx=1&sn=10f28d1bbee25c381bd781a9abb349d3&chksm=fbd27a3ecca5f3287ddfc698ee2125040295c5e6c369b76f8ec1060771fac82ad61ab560fa0b&mpshare=1&scene=1&srcid=0811y1dN56H2jySoSPI7f7nF#rd)
