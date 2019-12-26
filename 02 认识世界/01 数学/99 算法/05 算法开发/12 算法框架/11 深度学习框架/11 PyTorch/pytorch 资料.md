---
title: pytorch 资料
toc: true
date: 2019-07-04
---

本文收集了大量**基于 PyTorch 实现的代码链接，**其中有适用于深度学习新手的“入门指导系列”，也有适用于老司机的论文代码实现，包括 Attention Based CNN、A3C、WGAN等等。**所有代码均按照所属技术领域分类，**包括机器视觉/图像相关、自然语言处理相关、强化学习相关等等。所以如果你打算入手这风行一世的 PyTorch 技术，那么就快快收藏本文吧！

## **PyTorch 是什么？**

PyTorch即 Torch 的 python 版本。Torch 是由 Facebook 发布的深度学习框架，因支持动态定义计算图，相比于 Tensorflow 使用起来更为灵活方便，特别适合中小型机器学习项目和深度学习初学者。但因为 Torch 的开发语言是 Lua，导致它在国内一直很小众。所以，在千呼万唤下，PyTorch应运而生！PyTorch 继承了 Troch 的灵活特性，又使用广为流行的 python 作为开发语言，所以一经推出就广受欢迎！



## **目录：**

1. **入门系列教程**
2. **入门实例**
3. **图像、视觉、CNN相关实现**
4. **对抗生成网络、生成模型、GAN相关实现**
5. **机器翻译、问答系统、NLP相关实现**
6. **先进视觉推理系统**
7. **深度强化学习相关实现**
8. **通用神经网络高级应用**



## **入门系列教程**

**1.**PyTorch Tutorials

[https://github.com/MorvanZhou/PyTorch-Tutorial.git](https://link.zhihu.com/?target=https%3A//github.com/MorvanZhou/PyTorch-Tutorial.git)

著名的“莫烦”PyTorch系列教程的源码。



**2.Deep Learning with PyTorch: a 60-minute blitz**

[http://pytorch.org/tutorials/beginner/deep_learning_60min_blitz.html](https://link.zhihu.com/?target=http%3A//pytorch.org/tutorials/beginner/deep_learning_60min_blitz.html)

PyTorch官网推荐的由网友提供的 60 分钟教程，本系列教程的重点在于介绍 PyTorch 的基本原理，包括自动求导，神经网络，以及误差优化 API。



**3.Simple examples to introduce PyTorch**

[https://github.com/jcjohnson/pytorch-examples.git](https://link.zhihu.com/?target=https%3A//github.com/jcjohnson/pytorch-examples.git)

由网友提供的 PyTorch 教程，通过一些实例的方式，讲解 PyTorch 的基本原理。内容涉及 Numpy、自动求导、参数优化、权重共享等。



## **入门实例**

**1.Ten minutes pyTorch Tutorial**

[https://github.com/SherlockLiao/pytorch-beginner.git](https://link.zhihu.com/?target=https%3A//github.com/SherlockLiao/pytorch-beginner.git)

知乎上“十分钟学习 PyTorch“系列教程的源码。



**2.Official PyTorch Examples**

[https://github.com/pytorch/examples](https://link.zhihu.com/?target=https%3A//github.com/pytorch/examples)

官方提供的实例源码，包括以下内容：

- MNIST Convnets
- Word level Language Modeling using LSTM RNNs
- Training Imagenet Classifiers with Residual Networks
- Generative Adversarial Networks (DCGAN)
- Variational Auto-Encoders
- Superresolution using an efficient sub-pixel convolutional neural network
- Hogwild training of shared ConvNets across multiple processes on MNIST
- Training a CartPole to balance in OpenAI Gym with actor-critic
- Natural Language Inference (SNLI) with GloVe vectors, LSTMs, and torchtext
- Time sequence prediction - create an LSTM to learn Sine waves



**3.PyTorch Tutorial for Deep Learning Researchers**

[https://github.com/yunjey/pytorch-tutorial.git](https://link.zhihu.com/?target=https%3A//github.com/yunjey/pytorch-tutorial.git)

据说是提供给深度学习科研者们的 PyTorch 教程←_←。教程中的每个实例的代码都控制在 30 行左右，简单易懂，内容如下：

- PyTorch Basics
- Linear Regression
- Logistic Regression
- Feedforward Neural Network
- Convolutional Neural Network
- Deep Residual Network
- Recurrent Neural Network
- Bidirectional Recurrent Neural Network
- Language Model (RNN-LM)
- Generative Adversarial Network
- Image Captioning (CNN-RNN)
- Deep Convolutional GAN (DCGAN)
- Variational Auto-Encoder
- Neural Style Transfer
- TensorBoard in PyTorch



**4.PyTorch-playground**

[https://github.com/aaron-xichen/pytorch-playground.git](https://link.zhihu.com/?target=https%3A//github.com/aaron-xichen/pytorch-playground.git)

PyTorch初学者的 Playground，在这里针对一下常用的数据集，已经写好了一些模型，所以大家可以直接拿过来玩玩看，目前支持以下数据集的模型。

- mnist, svhn
- cifar10, cifar100
- stl10
- alexnet
- vgg16, vgg16_bn, vgg19, vgg19_bn
- resnet18, resnet34, resnet50, resnet101, resnet152
- squeezenet_v0, squeezenet_v1
- inception_v3



## **图像、视觉、CNN相关实现**

**1.PyTorch-FCN**

[https://github.com/wkentaro/pytorch-fcn.git](https://link.zhihu.com/?target=https%3A//github.com/wkentaro/pytorch-fcn.git)

FCN(Fully Convolutional Networks implemented) 的 PyTorch 实现。



**2.Attention Transfer**

[https://github.com/szagoruyko/attention-transfer.git](https://link.zhihu.com/?target=https%3A//github.com/szagoruyko/attention-transfer.git)

论文 "Paying More Attention to Attention: Improving the Performance of Convolutional Neural Networks via Attention Transfer" 的 PyTorch 实现。



**3.Wide ResNet model in PyTorch**

[https://github.com/szagoruyko/functional-zoo.git](https://link.zhihu.com/?target=https%3A//github.com/szagoruyko/functional-zoo.git)

一个 PyTorch 实现的 ImageNet Classification 。



**4.CRNN for image-based sequence recognition**

[https://github.com/bgshih/crnn.git](https://link.zhihu.com/?target=https%3A//github.com/bgshih/crnn.git)

这个是 Convolutional Recurrent Neural Network (CRNN) 的 PyTorch 实现。CRNN 由一些 CNN，RNN和 CTC 组成，常用于基于图像的序列识别任务，例如场景文本识别和 OCR。



**5.Scaling the Scattering Transform: Deep Hybrid Networks**

[https://github.com/edouardoyallon/pyscatwave.git](https://link.zhihu.com/?target=https%3A//github.com/edouardoyallon/pyscatwave.git)

使用了“scattering network”的 CNN 实现，特别的构架提升了网络的效果。



**6.Conditional Similarity Networks (CSNs)**

[https://github.com/andreasveit/conditional-similarity-networks.git](https://link.zhihu.com/?target=https%3A//github.com/andreasveit/conditional-similarity-networks.git)

《Conditional Similarity Networks》的 PyTorch 实现。



**7.Multi-style Generative Network for Real-time Transfer**

[https://github.com/zhanghang1989/PyTorch-Style-Transfer.git](https://link.zhihu.com/?target=https%3A//github.com/zhanghang1989/PyTorch-Style-Transfer.git)

MSG-Net 以及 Neural Style 的 PyTorch 实现。



**8.Big batch training**

[https://github.com/eladhoffer/bigBatch.git](https://link.zhihu.com/?target=https%3A//github.com/eladhoffer/bigBatch.git)

《Train longer, generalize better: closing the generalization gap in large batch training of neural networks》的 PyTorch 实现。



**9.CortexNet**

[https://github.com/e-lab/pytorch-CortexNet.git](https://link.zhihu.com/?target=https%3A//github.com/e-lab/pytorch-CortexNet.git)

一个使用视频训练的鲁棒预测深度神经网络。



**10.Neural Message Passing for Quantum Chemistry**

[https://github.com/priba/nmp_qc.git](https://link.zhihu.com/?target=https%3A//github.com/priba/nmp_qc.git)

论文《Neural Message Passing for Quantum Chemistry》的 PyTorch 实现，好像是讲计算机视觉下的神经信息传递。



## **对抗生成网络、生成模型、GAN相关实现**

**1.Generative Adversarial Networks (GANs) in PyTorch**

[https://github.com/devnag/pytorch-generative-adversarial-networks.git](https://link.zhihu.com/?target=https%3A//github.com/devnag/pytorch-generative-adversarial-networks.git)

一个非常简单的由 PyTorch 实现的对抗生成网络



**2.DCGAN & WGAN with Pytorch**

[https://github.com/chenyuntc/pytorch-GAN.git](https://link.zhihu.com/?target=https%3A//github.com/chenyuntc/pytorch-GAN.git)

由中国网友实现的 DCGAN 和 WGAN，代码很简洁。



**3.Official Code for WGAN**

[https://github.com/martinarjovsky/WassersteinGAN.git](https://link.zhihu.com/?target=https%3A//github.com/martinarjovsky/WassersteinGAN.git)

WGAN的官方 PyTorch 实现。



**4.DiscoGAN in PyTorch**

[https://github.com/carpedm20/DiscoGAN-pytorch.git](https://link.zhihu.com/?target=https%3A//github.com/carpedm20/DiscoGAN-pytorch.git)

《Learning to Discover Cross-Domain Relations with Generative Adversarial Networks》的 PyTorch 实现。



**5.Adversarial Generator-Encoder Network**

[https://github.com/DmitryUlyanov/AGE.git](https://link.zhihu.com/?target=https%3A//github.com/DmitryUlyanov/AGE.git)

《Adversarial Generator-Encoder Networks》的 PyTorch 实现。



**6.CycleGAN and pix2pix in PyTorch**

[https://github.com/junyanz/pytorch-CycleGAN-and-pix2pix.git](https://link.zhihu.com/?target=https%3A//github.com/junyanz/pytorch-CycleGAN-and-pix2pix.git)

图到图的翻译，著名的 CycleGAN 以及 pix2pix 的 PyTorch 实现。



**7.Weight Normalized GAN**

[https://github.com/stormraiser/GAN-weight-norm.git](https://link.zhihu.com/?target=https%3A//github.com/stormraiser/GAN-weight-norm.git)

《On the Effects of Batch and Weight Normalization in Generative Adversarial Networks》的 PyTorch 实现。



## **机器翻译、问答系统、NLP相关实现**

**1.DeepLearningForNLPInPytorch**

[https://github.com/rguthrie3/DeepLearningForNLPInPytorch.git](https://link.zhihu.com/?target=https%3A//github.com/rguthrie3/DeepLearningForNLPInPytorch.git)

一套以 NLP 为主题的 PyTorch 基础教程。本教程使用 Ipython Notebook编写，看起来很直观，方便学习。



**2.Practial Pytorch with Topic RNN & NLP**

[https://github.com/spro/practical-pytorch](https://link.zhihu.com/?target=https%3A//github.com/spro/practical-pytorch)

以 RNN for NLP 为出发点的 PyTorch 基础教程，分为“RNNs for NLP”和“RNNs for timeseries data”两个部分。



**3.PyOpenNMT: Open-Source Neural Machine Translation**

[https://github.com/OpenNMT/OpenNMT-py.git](https://link.zhihu.com/?target=https%3A//github.com/OpenNMT/OpenNMT-py.git)

一套由 PyTorch 实现的机器翻译系统。



**4.Deal or No Deal? End-to-End Learning for Negotiation Dialogues**

[https://github.com/facebookresearch/end-to-end-negotiator.git](https://link.zhihu.com/?target=https%3A//github.com/facebookresearch/end-to-end-negotiator.git)

Facebook AI Research 论文《Deal or No Deal? End-to-End Learning for Negotiation Dialogues》的 PyTorch 实现。



**5.Attention is all you need: A Pytorch Implementation**

[https://github.com/jadore801120/attention-is-all-you-need-pytorch.git](https://link.zhihu.com/?target=https%3A//github.com/jadore801120/attention-is-all-you-need-pytorch.git)

Google Research 著名论文《Attention is all you need》的 PyTorch 实现。



**6.Improved Visual Semantic Embeddings**

[https://github.com/fartashf/vsepp.git](https://link.zhihu.com/?target=https%3A//github.com/fartashf/vsepp.git)

一种从图像中检索文字的方法，来自论文：《VSE++: Improved Visual-Semantic Embeddings》。



**7.Reading Wikipedia to Answer Open-Domain Questions**

[https://github.com/facebookresearch/DrQA.git](https://link.zhihu.com/?target=https%3A//github.com/facebookresearch/DrQA.git)

一个开放领域问答系统 DrQA 的 PyTorch 实现。



**8.Structured-Self-Attentive-Sentence-Embedding**

[https://github.com/ExplorerFreda/Structured-Self-Attentive-Sentence-Embedding.git](https://link.zhihu.com/?target=https%3A//github.com/ExplorerFreda/Structured-Self-Attentive-Sentence-Embedding.git)

IBM 与 MILA 发表的《A Structured Self-Attentive Sentence Embedding》的开源实现。



## **先进视觉推理系统**

**1.Visual Question Answering in Pytorch**

[https://github.com/Cadene/vqa.pytorch.git](https://link.zhihu.com/?target=https%3A//github.com/Cadene/vqa.pytorch.git)

一个 PyTorch 实现的优秀视觉推理问答系统，是基于论文《MUTAN: Multimodal Tucker Fusion for Visual Question Answering》实现的。项目中有详细的配置使用方法说明。



**2.Clevr-IEP**

[https://github.com/facebookresearch/clevr-iep.git](https://link.zhihu.com/?target=https%3A//github.com/facebookresearch/clevr-iep.git)

Facebook Research 论文《Inferring and Executing Programs for Visual Reasoning》的 PyTorch 实现，讲的是一个可以基于图片进行关系推理问答的网络。

## **深度强化学习相关实现**

**1.Deep Reinforcement Learning withpytorch & visdom**

[https://github.com/onlytailei/pytorch-rl.git](https://link.zhihu.com/?target=https%3A//github.com/onlytailei/pytorch-rl.git)

多种使用 PyTorch 实现强化学习的方法。



**2.Value Iteration Networks in PyTorch**

[https://github.com/onlytailei/Value-Iteration-Networks-PyTorch.git](https://link.zhihu.com/?target=https%3A//github.com/onlytailei/Value-Iteration-Networks-PyTorch.git)

Value Iteration Networks (VIN) 的 PyTorch 实现。



**3.A3C in PyTorch**

[https://github.com/onlytailei/A3C-PyTorch.git](https://link.zhihu.com/?target=https%3A//github.com/onlytailei/A3C-PyTorch.git)

Adavantage async Actor-Critic (A3C) 的 PyTorch 实现。



## **通用神经网络高级应用**

**1.PyTorch-meta-optimizer**

[https://github.com/ikostrikov/pytorch-meta-optimizer.git](https://link.zhihu.com/?target=https%3A//github.com/ikostrikov/pytorch-meta-optimizer.git)

论文《Learning to learn by gradient descent by gradient descent》的 PyTorch 实现。



**2.OptNet: Differentiable Optimization as a Layer in Neural Networks**

[https://github.com/locuslab/optnet.git](https://link.zhihu.com/?target=https%3A//github.com/locuslab/optnet.git)

论文《Differentiable Optimization as a Layer in Neural Networks》的 PyTorch 实现。



**3.Task-based End-to-end Model Learning**

[https://github.com/locuslab/e2e-model-learning.git](https://link.zhihu.com/?target=https%3A//github.com/locuslab/e2e-model-learning.git)

论文《Task-based End-to-end Model Learning》的 PyTorch 实现。



**4.DiracNets**

[https://github.com/szagoruyko/diracnets.git](https://link.zhihu.com/?target=https%3A//github.com/szagoruyko/diracnets.git)

不使用“Skip-Connections”而搭建特别深的神经网络的方法。



**5.ODIN: Out-of-Distribution Detector for Neural Networks**

[https://github.com/ShiyuLiang/odin-pytorch.git](https://link.zhihu.com/?target=https%3A//github.com/ShiyuLiang/odin-pytorch.git)

这是一个能够检测“分布不足”（Out-of-Distribution)样本的方法的 PyTorch 实现。当“true positive rate”为 95％时，该方法将 DenseNet（适用于 CIFAR-10）的“false positive rate”从 34.7％降至 4.3％。



**6.Accelerate Neural Net Training by Progressively Freezing Layers**

[https://github.com/ajbrock/FreezeOut.git](https://link.zhihu.com/?target=https%3A//github.com/ajbrock/FreezeOut.git)

一种使用“progressively freezing layers”来加速神经网络训练的方法。



**7.Efficient_densenet_pytorch**

[https://github.com/gpleiss/efficient_densenet_pytorch.git](https://link.zhihu.com/?target=https%3A//github.com/gpleiss/efficient_densenet_pytorch.git)

DenseNets的 PyTorch 实现，优化以节省 GPU 内存。



[资料下载](https://link.zhihu.com/?target=http%3A//campus.swarma.org/gapp%3D120)
