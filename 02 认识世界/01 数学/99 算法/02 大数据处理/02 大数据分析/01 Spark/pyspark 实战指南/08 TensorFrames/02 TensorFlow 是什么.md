---
title: 02 TensorFlow 是什么
toc: true
date: 2019-07-02
---
8.2 TensorFlow是什么
TensorFlow是一个 Google 开源软件库，用于使用数据流图进行数值计算。换句话说就是一个以深度学习为重点的开源机器学习库。TensorFlow是 Google Brain团队的研究人员和工程师基于神经网络将深度学习应用于 Google 产品，并为各个 Google 团队（包括但不限于）的搜索、照片和语音识别构建生产模型的成果。
Tensorflow建立在具有 python 接口的 C＋＋之上，它在很短的时间内迅速成长为最受欢迎的深度学习项目之一。下图展示了四个流行的深度学习库之间的 Google trend比较；请注意 2015 年 11 月 8 日至 14 日（TensorFlow宣布时）的尖峰以及去年快速上涨（此快照拍于 2016 年 12 月底）：
让我们用另一种方法来衡量 TensorFlow 的人气度。你可以注意到根据 http://www.theverge.com/2016/4/13/11420144/google-machine-learning-tensorflowupgrade所述，TensorFlow是 GitHub 上最受欢迎的机器学习框架。请注意，TensorFlow在 2015 年 11 月发布，仅仅在两个月内就已经成为 GitHub repository最受欢迎的 ML 分支。在下图中，你可以通过 http://donnemartin.com/viz/pages/2015查看每个 2015 年创建的 GitHub repository（Interactive Visualization）：


如前文所述，TensorFlow使用数据流图进行数值计算。当考虑图形（正如上一章一样）时，该图的节点（或顶点）表示数学运算，而图形的边表示在不同节点（也就是数学运算）之间通信的多维数组（即张量）。
参考下图，t1是 2×3矩阵，而 t2 是 3×2矩阵；这些是张量（或张量图的边）。节点是表示为 op1 的数学运算：
在这个例子中，op1是由下图所示的矩阵乘法运算，这可以是 TensorFlow 中多种可用数学运算中的任意一个：
为了在图中执行数值计算，在数学运算（节点）之间存在一个多维数组（即张量）的数据流，即张量流或 TensorFlow。
为了更好地了解 TensorFlow 的工作原理，我们首先在你的 python 环境中安装 TensorFlow（最初没有 Spark）。有关完整说明，请参阅“TensorFlow|Download and Setup”：https://www.tensorflow.org/versions/r0.12/get_started/os_setup.html。
在本章中，我们将重点介绍 Linux 或 Mac OS上使用 python pip软件包管理系统的安装。
8.2.1 安装 PIP
确保你已经安装了 pip；如果没有，请使用以下命令安装 Ubuntu/Linux的 python 软件包安装管理器：
对于 Mac OS，你可以使用以下命令：
请注意，对于 Ubuntu/Linux，你可能还需要升级 pip，因为 Ubuntu 存储库中的 pip 是旧的，可能与新的软件包不兼容。为此，你可以运行命令：
8.2.2 安装 TensorFlow
要安装 TensorFlow（已安装 pip），你只需执行以下命令：
如果你有一台支持 GPU 的计算机，则可以使用以下命令：
请注意，如果上述命令不起作用，另有具体说明可根据你的 python 版本（即 2.7，3.4或 3.5）和 GPU 支持来安装具有 GPU 支持的 TensorFlow。
例如，如果我想在 Mac OS上安装具有 GPU 功能的 python 2.7的 TensorFlow，请执行以下命令：


最新安装指南请参考 https://www.tensorflow.org/versions/r0.12/get_started/os_setup.html。
8.2.3 使用常量进行矩阵乘法
为了更好地描述张量和 TensorFlow 的工作原理，我们从一个涉及两个常数的矩阵乘法运算开始。如下图所示，我们有 c1（3×1矩阵）和 c2（1×3矩阵），其中操作（op1）是矩阵乘法：
我们现在将使用以下代码定义 c1（1×3矩阵）和 c2（3×1矩阵）：
现在有了我们的常量，让我们使用下面的代码运行矩阵乘法。在 TensorFlow 图的上下文中，记住图中的节点称为操作（或 ops）。以下矩阵乘法是 ops，而两个矩阵（c1，c2）是张量（类型化的多维数组）。一个 op 将零个或多个张量作为其输入，执行诸如数学计算的操作，输出为零个或多个张量，其格式为 numpy ndarray对象（http://www.numpy.org/）或 C 及 C＋＋中的接口 tensorflow：Tensor：
现在已经建立了 TensorFlow 的图形，则该操作（例如在这种情况下是矩阵乘法）已经在会话中完成。会话将图形操作放入要执行的 CPU 或 GPU（即设备）中：
输出将是：
一旦操作结束，你可以关闭会话：


8.2.4 使用 placeholder 进行矩阵乘法
现在我们将执行与之前相同的任务，不过这一次，我们将使用张量而不是常量。如下图所示，我们将使用与上一节相同的值从两个矩阵（m1：3x1，m2：1x3）开始：
在 TensorFlow 中，我们将使用 placeholder 根据以下代码段来定义我们的两个张量：
这种方法的优点在于，通过 placeholder，你可以使用与不同大小和形状的张量（只要它们符合操作的标准）相同的操作（在这种情况下就是矩阵乘法）。像上一节中的操作一样，我们定义两个矩阵并执行图形（使用简化会话执行）。运行模型
以下代码片段与上一节中的代码段相似，不同的是它现在使用 placeholder 而不是常量：
输出值同时包含数据类型：运行另一个模型
现在我们有一个使用 placeholder 的图形（尽管是一个简单的），我们可以使用不同的张量来使不同的输入矩阵执行相同的操作。如下图所示，我们有 m1（4×1）和 m2（1×4）：


因为我们正在使用 placeholder，所以我们可以轻松地使用新的输入在新会话中重用相同的图形：
结果将为：
8.2.5 讨论
如前所述，TensorFlow通过将计算表示为用张量表示数据（图形的边）、操作表示要执行内容（例如数学计算）（图的边界）的图形，为用户提供了使用 python 库进行深度学习的能力。
更多内容请参阅：
·“TensorFlow|Get Started|Basic Usage”，https://www.tensorflow.org/get_started/basic_usage；
·“Shannon McCormick’s Neural Network and Google TensorFlow”，http://www.slideshare.net/ShannonMcCormick4/neural-networks-and-google-tensor-flow。
