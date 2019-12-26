---
title: Tensorflow Lite 介绍
toc: true
date: 2019-11-17
---

最新的中文技术分享视频来了！本期来自 Google 的工程师 Renmin 为大家带来 TensorFlow Lite 的深度解析视频，主要讲述 TensorFlow Lite 模型文件格式，并可视化以帮助大家记忆理解，也包含 TensorFlow Lite 的具体加载运行过程，并给出关键的数据结构描述，同样以可视化的形式呈现给大家：





看完视频，我们不妨一起总结回顾一下：



首先，我们需要在台式机上设计、训练出目标模型，并将其转化成 TensorFlow Lite 的格式。接着，此格式文件在 TensorFlow Lite 中会被内置了 Neon 指令集的解析器加载到内存，并执行相应的计算。由于 TensorFlow Lite 对硬件加速接口良好的支持，开发者可以设计出性能更优的 App 供用户使用。



## **模型文件格式**



### **▌Model 结构体：模型的主结构**



```
table Model {
    version: uint;
    operator_codes: [OperatorCode];
    subgraphs: [SubGraph];

    description: string;
    buffers: [Buffer]
}
```



在上面的 Model 结构体定义中，operator_codes 定义了整个模型的所有算子，subgraphs 定义了所有的子图。子图当中，第一个元素是主图。buffers 属性则是数据存储区域，主要存储的是模型的权重信息。



### **▌SubGraph 结构体：Model 中最重要的部分**



```
table SubGraph {
    tensors: [Tensor];
    inputs: [int];
    outputs: [int];
    operators: [Operator];

    name: string;
}
```



类似的，tensors 属性定义了子图的各个 Tensor，而 inputs 和 outputs 用索引的维护着子图中 Tensor 与输入输出之间的对应关系。剩下的operators 定义了子图当中的算子。



### Tensor 结构体：包含维度、数据类型、Buffer 位置等信息



```
table Tensor {
    shape: [int];
    type: TensorType;
    buffer: uint;

    name: string;
}
```



buffer 以索引量的形式，给出了这个 Tensor 需要用到子图的哪一个 buffer。



### **▌Operator 结构体：SubGraph 中最重要的结构体**



Operator 结构体定义了子图的结构：





```
table Operator {
    opcode_index: uint;
    inputs: [int];
    outputs: [int];

    ...
}
```



opcode_index 用索引方式指明该 Operator 对应了哪个算子。 inputs 和 outputs 则是 Tensor 的索引值，指明该 Operator 的输入输出信息。



## **▌解析器概况**



那么 TensorFlow Lite 的解析器又是如何工作的呢？



一开始，终端设备会通过 mmap 以内存映射的形式将模型文件载入客户端内存中，其中包含了所有的 Tensor，Operator 和 Buffer 等信息。出于数据使用的需要，TensorFlow Lite 会同时创建 Buffer 的只读区域和分配可写 Buffer 区域。



由于解析器中包含了集体执行计算的代码，这一部分被称为 Kernel。模型中的各个 Tensor 会被加载为 TfLiteTensor 的格式并集中存放在 TfLiteContext 中。



每个 Tensor 的指针都指向了内存中的只读 Buffer 区域或是一开始新分配的可写入 Buffer 区域。



模型中的 Operator 被重新加载为 TfLiteNode，它包含输入输出的 Tensor 索引值。这些 Node 对应的操作符存储于 TfLiteRegistration 中，它包含了指向 Kernel 的函数指针。OpResolver 负责维护函数指针的对应关系。



TensorFlow Lite 加载模型的过程中会确定执行 Node 的顺序，然后依次执行。



大家如果想要更好掌握 TensorFlow Lite 的技术细节，一定要阅读以下文件：



- lite/context.h
- lite/model.h
- lite/interpreter.h
- lite/kernels/register.h



希望我们的分享能让广大开发者们对 TensorFlow Lite 的代码有一个初步认识，期待看到大家精彩的作品！



另外，**TensorFlow Lite 的代码位置****：**



https://github.com/tensorflow/tensorflow/tree/master/tensorflow/contrib/lite



**模型的模式文件位于：**



https://github.com/tensorflow/tensorflow/blob/master/tensorflow/contrib/lite/schema/schema.fbs


# 相关

- [Google第一个TF中文教学视频发布 | TensorFlow Lite深度解析](https://mp.weixin.qq.com/s?__biz=MzAwNDI4ODcxNA==&mid=2652247796&idx=2&sn=6cf9085735bbe4ffb59ec5ef828b5f60&chksm=80cc8f51b7bb0647d040449df2798bda98df7cf483ac9dec6c85e9b1f01b92a50e8546157395&mpshare=1&scene=1&srcid=0617bAOfP2mjdAYhHpbLCsA8#rd)
