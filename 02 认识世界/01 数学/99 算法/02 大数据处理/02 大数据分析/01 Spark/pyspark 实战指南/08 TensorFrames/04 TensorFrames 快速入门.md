---
title: 04 TensorFrames 快速入门
toc: true
date: 2019-07-02
---
.4 TensorFrames快速入门
所有这些前言之后，让我们用这个快速入门教程来开始使用 TensorFrames。你可以在 http://bit.ly/2hwGyuC下载并使用 Databricks 社区版中笔记本的完整版本。
你也可以从 PySpark shell（或其他 Spark 环境）运行它，就像任何其他 Spark 软件包一样：
注意，你将只需使用上述命令之一（而不是两者都需要）。有关更多信息，请参阅 databricks/tensorframes的 GitHub repository（https://github.com/databricks/tensorframes）。
8.4.1 配置和设置
请按照以下顺序执行配置和设置步骤：启动 Spark 群集
使用 Spark 1.6（Hadoop 1）和 Scala 2.10启动 Spark 群集。这已经在 Databricks 社区版（http://databricks.com/try-databricks）上使用 Spark 1.6、Spark 1.6.2和 Spark 1.6.3（Hadoop 1）进行了测试。创建 TensorFrames 库
创建一个库，将 TensorFrames 0.2.2附加到你的群集：tensorframes-0.2.2-s_2.10。请参考第 7 章来了解如何创建库。在集群上安装 TensorFlow
在笔记本中，运行以下命令之一来安装 TensorFlow。该命令已使用 TensorFlow 0.9 CPU版本进行了测试：
·TensorFlow 0.9，Ubuntu/Linux 64-bit，仅用于 CPU，python 2.7：
·TensorFlow 0.9，Ubuntu/Linux 64-bit，GPU可用，python 2.7：
以下是安装 TensorFlow 到 Apache Spark驱动的 pip install命令：
安装成功应该有以下输出：
成功安装 TensorFlow 后，请分离并重新连接刚刚运行此命令的笔记本。你的集群现已配置完成，你可以在该驱动上运行纯 TensorFlow 程序，也可以在整个集群上运行 TensorFrames 示例。


.4.2 使用 TensorFlow 向已有列添加常量
这是一个简单的 TensorFrames 程序，其中的 op 是执行简单的添加。请注意，原始源代码可以在 databricks/tensorframes的 GitHub repository中找到。这是参考了 TensorFrames Readme.md|《How to Run in python》节选（https://github.com/databricks/tensorframes#how-to-runin-python）。
我们首先要做的是导入 TensorFlow、TensorFrames和 pyspark.sql.row来创建一个基于浮点数 RDD 的 DataFrame：
要查看由浮点数 RDD 生成的 df DataFrame，我们可以使用 show 命令：
这产生以下结果：执行张量图
如前所述，该张量图会将由自浮点数据 RDD 生成的 df DataFrame创建的张量的每个值加 3 组成。现在我们将执行以下代码片段：



以下是上述代码段的一些特定调用：
·x使用 tfs.block，其中 block 根据 DataFrame 中的列的内容构建了一个 placeholder；
·z是 TensorFlow 添加方法的输出张量（tf.add）；
·df2是新的 DataFrame，它把 z 张量逐块添加到 df DataFrame的一个额外的列中。
虽然 z 是本身就是张量（如前面的输出中所述），但是为了使我们能够使用 TensorFlow 程序的结果，我们将使用 df2 dataframe。df2.show（）的输出如下：
8.4.3 Blockwise reducing操作示例
在下一节中，我们将介绍如何使用 blockwise reducing操作。具体来说，我们将计算字段向量的总和和最小值，使用行块进行更有效的处理。构建向量的 DataFrame
首先，我们创建一个向量的单列 DataFrame：
输出如下：分析 DataFrame
我们需要分析 DataFrame 以确定其形状（即向量的维度）。例如，在下面的代码片段中，我们对 df DataFrame使用 tfs.print_schema命令：


注意 double[？，？]，这意味着 TensorFlow 不知道向量的维度：
在分析了 df2 DataFrame之后，TensorFlow推测 y 包含大小为 2 的向量。对于小张量（标量和向量），TensorFrames通常推测张量的形状，而不需要初步分析。如果不能这样做，错误消息将提示你需要在运行 DataFrame 前先运行 tfs.analyze（）。计算所有向量的元素和和最小值
现在，我们来分析 df DataFrame以使用 tf.reduce_sum和 tf.reduce_min来计算所有向量的总和和元素的最小值：
·tf.reduce_sum：计算张量的维度上的元素之和，例如，如果 x＝[[3，2，1]，[-1，2，1]]，则 tf.reduce_sum（x）＝＝>8。有关更多信息，请访问：https//www.tensorflow.org/api_docs/python/math_ops/reduction#reduce_sum。
·tf.reduce_min：计算张量的维度上的最小值，例如，如果 x＝[[3，2，1]，[-1，2，1]]则 tf.reduce_min（x）＝＝>-1。更多信息，请访问：https//www.tensorflow.org/api_docs/python/math_ops/reduction#reduce_min。
以下代码片段允许我们使用 TensorFlow 执行有效的元素缩减，其中源数据位于 DataFrame 中：


使用几行结合 TensorFrames 的 TensorFlow 代码，我们可以获取存储在 df DataFrame中的数据，并执行 Tensor Graph来获取元素和以及最小值，再将数据合并回到 DataFrame 中并（在我们的例子中）打印出最终值。
