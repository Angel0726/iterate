---
title: 03 TensorFrames 介绍
toc: true
date: 2019-07-02
---
8.3 TensorFrames介绍
在撰写本文时，TensorFrames是 Apache Spark的实验性约束。它是在 TensorFlow 发布后不久于 2016 年初推出的。使用 TensorFrames，可以利用 TensorFlow 程序来操作 Spark DataFrame。参考上一节中的张量图，我们将其更新为一张新图，以显示 Spark DataFrame如何与 TensorFlow 配合使用，如下图所示：
如前图所示，TensorFrames在 Spark DataFrame和 TensorFlow 之间提供了一个桥梁。这样，你可以将 DataFrame 作为输入应用到你的 TensorFlow 计算图中。TensorFrames还允许你使用 TensorFlow 计算图输出并将其推回到 DataFrame 中，以便可以继续下游的 Spark 处理。
TensorFrames的常见用法通常包括以下内容：1.应用 TensorFlow 处理你的数据
TensorFlow和 Apache Spark与 TensorFrames 的集成允许数据科学家通过 TensorFlow 扩展其分析、数据、图形和机器学习功能来实现深度学习（Deep Learning）。这样就可以按比例训练和部署模型。2.并行训练确定最佳的超参数
构建深度学习模型时，有几个配置参数（即超参数）会影响模型的训练。深度学习/人工神经网络中的常见之处是定义学习速率的超参数（如果速率很高，它将快速学习，但可能不考虑高度可变的输入。也就是说，如果数据的速率和变异性太高，学习的效果不会太好）和神经网络中每一层神经元的数量（太多的神经元导致估计的噪音，而太少的神经元会导致网络学习结果不好）。
如《Deep Learning with Apache Spark and TensorFlow》（https://databricks.com/blog/2016/01/25/deep-learning-with-apache-sparkand-tensorflow.html）所述，使用 Spark with TensorFlow来帮助找到最佳的神经网络训练超参数集，可以使训练时间减少一个数量级，手写数字识别数据集的错误率降低 34％。
有关深度学习和超参数的更多信息，请参阅：
·Optimizing Deep Learning Hyper-Parameters Through an Evolutionary Algorithm（http://ornlcda.github.io/MLHPC2015/presentations/4-Steven.pdf）。
·CS231n Convolutional Network Networks for Visual Recognition（http://cs231n.github.io/）。
·Deep Learning with Apache Spark and TensorFlow（https://databricks.com/blog/2016/01/25/deep-learning-with-apache-spark-and-tensorflow.html）。
在撰写本文时，TensorFrames正式被 Apache Spark 1.6（Scala 2.10）支持，尽管目前大多数贡献集中在 Spark 2.0（Scala 2.11）上。使用 TensorFrames 的最简单方法是通过 Spark Packages（https://spark-packages.org）进行访问。
