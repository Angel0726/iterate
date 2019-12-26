---
title: 使用Keras实现多输出分类 用单个模型同时执行两个独立分类任务
toc: true
date: 2019-11-17
---
# 使用Keras实现多输出分类 用单个模型同时执行两个独立分类任务


> 如何让一个网络同时分类一张图像的两个独立标签？多输出分类可能是你的答案。已经推出了两个图像搜索引擎（ID My Pill 和 Chic Engine）的 Adrian Rosebrock 近日发布了一份教程，介绍了使用 Keras 和 TensorFlow 实现「服装种类+颜色」多输出分类的详细过程。机器之心编译介绍了该教程。



之前我们介绍了使用 Keras 和深度学习的多标签分类（multi-label classification），参阅 https://goo.gl/e8RXtV。今天我们将讨论一种更为先进的技术——多输出分类（multi-output classification）。



所以，这两者之间有何不同？你怎样才能跟得上这各项技术？



尽管这两者有些混淆不清（尤其是当你刚入门深度学习时），但下面的解释能帮你区分它们：



- 在多标签分类中，你的网络仅有一组全连接层（即「头」），它们位于网络末端，负责分类。
- 但在多输出分类中，你的网络至少会分支两次（有时候会更多），从而在网络末端创建出多组全连接头——然后你的网络的每个头都会预测一组类别标签，使其有可能学习到不相交的标签组合。



你甚至可以将多标签分类和多输出分类结合起来，这样每个全连接头都能预测多个输出了！



如果这开始让你感到头晕了，不要担心——这篇教程将引导你通过 Keras 透彻了解多输出分类。实际做起来会比预想的更轻松。



话虽如此，这项深度学习技术还是比之前介绍的多标签分类技术更先进。如果你还没有阅读那篇文章，一定要先看看。



读完那篇文章之后，你应该就已经能使用多个损失函数训练你的网络并从该网络获取多个输出了。接下来我们介绍如何通过 Keras 使用多个输出和多个损失。



![img](https://mmbiz.qpic.cn/mmbiz_gif/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaKGV2VTicLNrPTldaSvuMByss2dzhTAvxeHBE6UMyb8bYAVkQVJgulibVw/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

*图 1：我们可以使用 Keras 执行多输出分类，其中多组全连接头使其有可能学习到不相交的标签组合。该动画展示了几个多输出分类的结果。*



在这篇文章中，我们将了解如何通过 Keras 深度学习库使用：



- 多个损失函数
- 多个输出



正如前面提到的，多标签预测和多输出预测之间存在区别。



使用多标签分类时，我们使用一个全连接头来预测多个类别标签。



但使用多输出分类时，我们至少有两个全连接头——每个头都负责执行一项特定的分类任务。



我们甚至可以将多输出分类与多标签分类结合起来——在这种情况下，每个多输出头也会负责计算多个标签！



你可能已经开始觉得有些难以理解了，所以我们不再继续讨论多输出分类和多标签分类的差异。接下来走进项目里看看吧！我相信本文中所给出的代码能帮你理清这两个概念。



首先我们将介绍数据集的情况，我们将使用这个数据集来构建我们的多输出 Keras 分类器。



然后，我们将实现并训练我们的 Keras 架构 FashionNet，其可使用该架构中两个独立的分支来分类服装/时装：



1. 一个分支用于分类给定输入图像的服装种类（比如衬衫、裙子、牛仔裤、鞋子等）；
2. 另一个分支负责分类该服装的颜色（黑色、红色、蓝色等）。



最后，我们将使用训练后的网络来分类示例图像，得到多输出分类结果。



下面就开始吧！



**多输出深度学习数据集**



![img](https://mmbiz.qpic.cn/mmbiz_jpg/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaK94Wp63ZVP5zcMDe8cxf0IrFvTnBjLjiaic35GG1u09iawiaZibenSibVCB0A/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*图 2：我们的多输出分类数据集是使用这篇文章所讨论的技术创建的：https://goo.gl/3C8xyK。注意我们的数据集中不包含红色/蓝色鞋子或黑色裙子/衬衫，但本文所介绍的 Keras 多输出分类方法依然能正确预测这些组合。*



在本 Keras 多输出分类教程中，我们将使用的数据集基于之前的多标签分类文章的数据集，但也有一个例外——我增加了一个包含 358 张「黑色鞋子」图像的文件夹。



总体而言，我们的数据集由 2525 张图像构成，分为 7 种「颜色+类别」组合，包括：



- 黑色牛仔裤（344 张图像）
- 黑色鞋子（358 张图像）
- 蓝色裙子（386 张图像）
- 蓝色牛仔裤（356 张图像）
- 蓝色衬衫（369 张图像）
- 红色裙子（380 张图像）
- 红色衬衫（332 张图像）





我使用我之前写的教程《如何（快速）创建一个深度学习图像数据集》中描述的方法创建了该数据集，参阅：https://goo.gl/3C8xyK。



下载图像和人工移除 7 个组合中的无关图像的整个过程大约耗时 30 分钟。在构建你自己的深度学习图像数据集时，要确保你遵循了上述链接的教程——这能为你开始构建自己的数据集提供很大帮助。



我们的目标是能够同时预测颜色和服装种类，这和上次一样；但不同之处是我们这一次的网络要能够预测之前未训练过的「服装种类+颜色」组合。



比如，给定下列「黑色裙子」图像（我们的网络没使用过这样的训练数据）：



![img](https://mmbiz.qpic.cn/mmbiz_jpg/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaKvlPkgNRlibv7uQNBYRazklKc9rwH910CcaXwegpXOyic3z2hHxl6AMJw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*图 3：尽管我们的数据集不包含「黑色裙子」图像，但我们仍然可以通过 Keras 和深度学习使用多输出分类来得到正确的分类结果。*



我们的目标是正确预测出该图像的「黑色」+「裙子」。



**我们的 Keras + 深度学习项目结构**



如果你想在你自己的图像上操作这些代码，可以访问原文结尾处的下载板块，下载与本文关联的 .zip 文件。



然后，unzip 这个文件并按下列方式修改目录（cd）。然后使用 tree 命令，你就可以看到组织好的文件和文件夹。



上面你可以看到我们的项目结构，但在我们继续之前，首先让我们概览一下其中的内容。



其中有 3 个值得关注的 Python 文件：



- pyimagesearch/fashionnet.py：我们的多输出分类网络文件包含由三种方法组成的 FashionNet 架构类：build_category_branch、build_color_branch 和 build。我们将在下一节详细介绍这些方法。
- train.py：这个脚本会训练 FashionNet 模型，并在这一过程中在输出文件夹生成所有文件。
- classify.py：这个脚本会加载训练后的网络，然后使用多输出分类来分类示例图像。



我们还有 4 个顶级目录：



- dataset/：我们的时装数据集，这是使用 Bing 图片搜索的 API 收集到的。我们在前一节中介绍了这个数据集。你可以参考前面提到的教程来创建自己的数据集。
- examples/：我们有一些示例图像，我们将在本文最后一节与我们的 classify.py 脚本一起使用。
- output/：我们的 train.py 脚本会生成一些输出文件：

- fashion.model：我们的序列化的 Keras 模型
- category_lb.pickle：由 scikit-learn 生成的服装类别的序列化 LabelBinarizer 对象。这个文件可被我们的 classify.py 脚本加载（而且标签会被调用）
- color_lb.pickle：颜色的 LabelBinarizer 对象
- output_accs.png：准确度的训练图表
- output_losses.png：损失的训练图表

- pyimageseach/：这是一个包含 FashionNet 类的 Python 模块



**快速概览我们的多输出 Keras 架构**



要使用 Keras 执行多输出预测，我们要实现一种特殊的网络架构（这是我专为这篇文章创造的），我称之为 FashionNet。



FashionNet 架构包含两个特殊组件，包括：



- 一个网络的早期分支，之后会分成两个「子网络」——一个负责服装种类分类，另一个负责颜色分类。
- 在网络末端的两个（不相交的）全连接头，每一个都负责各自的分类任务。



在我们开始实现 FashionNet 之前，我们先可视化地看看每个组分，首先是分支：



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaKBAw71vswGYpc1xBWn3dKLISxHU9RaQia1CjMYmoLTibf4eeOR7vNB3rQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*图 4：我们的多输出分类网络的顶层是用 Keras 编码的。可以看到，左边是服装种类分支，右边是颜色分支。每个分支都有一个全连接头。*



在这个网络架构图中可以看到，我们的网络接收的输入图像是 96x96x3 大小。



接下来我们就创建两个分支：



- 左边的分支负责分类服装种类。
- 右边的分支负责分类颜色。



每个分支都执行各自的卷积、激活、批归一化、池化和 dropout 操作组合，直到我们得到最终输出：



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaKrcErYkAwhVetgaWibJ80I3t1OBF0L4c8Vv0TEuw4pBTWJmebxatVCRA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*图 5：我们的深度学习 Keras 多输出分类网络有可能学习到不相交的标签组合。*



注意这些全连接（FC）头组合看起来就像是本博客介绍过的其它架构的全连接层——但现在这里有两个全连接头了，其中每一个都负责一个给定的分类任务。



可以看到，该网络的右边分支比左边分支要浅很多，这是因为预测颜色比预测服装类别容易多了。



下一节我们将介绍如何实现这样的架构。



**实现我们的 FashionNet 架构**



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaKRtEoRMHetg1icbWHjiaMFFju40jV3oWOIMyoia7V3qHcFIbktFNtxhqsg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*图 6：Keras 深度学习库拥有执行多输出分类所需的所有功能。*



因为使用多个损失函数训练带有多个输出的网络是一项相当先进的技术，所以我假定你已经知道 CNN 的基础知识，我们将主要关注实现多输出/多损失训练的元素。



如果你还是深度学习和图像分类领域的新手，你可以考虑看看我的书《Deep Learning for Computer Vision with Python》，这能帮你快速赶上来。



我相信你已经照前文说的方法下载好了那些文件和数据。现在让我们打开 fashionnet.py 看一看：



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaK9icqZPZBnwAyFPdj8e6Hjibf83vzKvvCZgL2bpYKhEeiaejwQkmib5D8fg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



我们先从 Keras 库导入模块并导入 TensorFlow 本身。



因为我们的网络由两个子网络构成，所以我们将定义两个函数，分别负责构建每个分支。



第一个分支 build_category_branch 负责分类服装种类，定义如下：



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaKqUqCdvNXOBpxJs4bianz62m5THsNWibibJiaMqGxdzFdPBibOXE74S7raXw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



第 16 和 17 行定义了 build_category_branch，它具有三个值得提及的参数：



- inputs：输入类别分支子网络的输入量
- numCategories：裙子、鞋子、牛仔裤、衬衫等类别的数量
- finalAct：最后的激活层的类型，默认是一个 softmax 分类器。如果你要既要执行多输出分类，也要执行多标签分类，你应该将这个激活换成 sigmoid。



看一下第 20 行，这里我们使用了一个 lambda 层将我们的图像从 RGB 转换成了灰度图像。



为什么要这样做？



因为不管是红、蓝、绿还是紫，裙子始终都是裙子。所以我们决定丢弃颜色信息，仅关注图像中的实际结构成分，以确保我们的网络没有在学习中将特定的颜色与服装种类关联起来。



注：lambda 在 Python 3.5 和 Python 3.6 中的工作方式不一样。我训练这个模型使用的是 Python 3.5，所以如果你想用 Python 3.6 运行这个 classify.py 脚本来进行测试，你可能会遇到麻烦。如果你在 lambda 层遇到了报错，我建议你 (a) 尝试 Python 3.5 或 (b) 在 Python 3.6 上训练然后分类。不需要修改代码。



第 23-27 行，我们继续构建带有 dropout 的 CONV => RELU => POOL 代码块。



我们的第一个 CONV 层有 32 个带有 3x3 卷积核和 RELU（修正线性单元）激活的过滤器。我们应用了批归一化、最大池化和 25% 的 dropout。



dropout 是一种随机断开当前层节点与下一层节点之间的连接的过程。这一随机断开连接过程本质上有助于减少过拟合，因为该层中不会有什么单独的节点负责预测一个特定的类别、物体、边缘或角。



接下来是两组 (CONV => RELU) * 2 => POOL 代码块：



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaKicoLTRicRR58oiaMcBMxfGUdibdXutan3xZ4r8J1XY4dGDiayzH8xYCT65Q/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



在这个代码块中对过滤器、卷积核和池化大小的修改是联合进行的，以在逐步降低空间尺寸的同时增加深度。



让我们再使用一个 FC => RELU 层将其归总到一处：



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaKQaFRIsaAVcx4MajG8W0cl2rSTphXU1dfmXYg59OiawGGQic0FCT6sq2Q/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



最后一个激活层是全连接的，并且有与我们的 numCategories 同样数量的神经元/输出。



要注意，我们在第 57 行将我们最后的激活层命名为了 category_output。这很重要，因为我们之后将在 train.py 中通过名字引用这一层。



让我们定义第二个用于构建我们的多输出分类网络的函数。我们将其命名为 build_color_branch，顾名思义，其负责分类图像中的颜色。



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaKGsdHsYCbReQcz8vKwEV6jVWvwWtbWowqMia4K9mzX6kKwDB8djxYUkw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



build_color_branch 的参数与 build_category_branch 的参数基本一样。我们使用 numColors 作为其最后一层激活的数量（不同于 numCategories）。



这时候我们不再使用 lambda 灰度转换层，因为网络这个部分实际上关心的就是颜色。如果转换成灰度图像，我们就会丢失所有的颜色信息！



网络的这个分支比分类服装种类的分支浅很多，很多分类颜色的任务要简单很多。这个子网络需要做的只是分类颜色，不需要太深。



类似于服装种类分支，我们也有一个全连接头。让我们构建 FC =>RELU 代码块来完成：



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaKTDeQzWB0ucIibUnhBg5TZictVxpIic2JPt6bRmmlYfpzLrkXhMSwfwTCw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



为了区分颜色分支的最终激活层，我在第 94 行提供了 name="color_output" 关键词参数。我们将在训练脚本中引用它。



构建 FashionNet 的最后一步是将我们的两个分支合并到一起，build 最终架构：



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaKEomItorfNQCsyiarlJtRWCPU5JicRISuzStD54YZZ21pqC5ia4m9jH91A/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



我们的 build 函数是在第 100 行定义的，其有 5 个一看就懂的参数。



这个 build 函数假设我们正在使用 TensorFlow 和通道最后排序（channels last ordering）。这使得第 105 行中的 inputShape 元组有清晰明确的排序 (height, width, 3)，其中 3 是指 RGB 这 3 个通道。



如果你更愿意使用不同于 TensorFlow 的后端，你需要对代码进行修改：（1）你的后端应该有适当的通道排序，（2）实现一个定制层来处理 RGB 到灰度的转换。



之后，我们定义该网络的两个分支（第 110-113 行），然后将它们组合到一个 model 中（第 118-121 行）。



其中的关键思想是我们的分支有一个共有输入，但有两个不同的输出（服装种类和颜色分类结果）。



**实现多输出和多损失训练脚本**



现在我们已经实现了我们的 FashionNet 架构，开始训练它吧！



准备好了吗？让我们打开 train.py 继续深入：



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaKMPuZcOtACjCqQ1UXlK822hBibudNHFMaz99Hiaiac6UapeYicUgco239KA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



我们首先导入该脚本必需的软件包。



然后我们解析我们的命令行参数：



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaK6d6CjDfyHSW55ko7II692QnWeyXvVh6YGlwS46nezZNdxRPiaOrk74A/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



我们很快就会看到如何运行训练脚本了。目前，只需要知道 --dataset 是我们的数据集的输入文件路径，--model、--categorybin、--colorbin 是三个输出文件的路径。



还有个可选操作。你可以使用 --plot 参数指定一个用于生成的准确度/损失图表的基本文件名。我会在脚本中遇到它们时指出这些命令行参数。如果第 21-32 行对你而言有些难以理解，请参阅这篇文章：https://goo.gl/uG5mo9。



现在，让我们确定 4 个重要的训练变量：



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaKlPL9t7ic6j5KHkHzEY3ZYAedgfT9bXd4TCLn9KduphCeDAKqqtiaDmJg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



我们在第 36-39 行设置了以下变量：



- EPOCHS：epoch 数量设置为 50。我通过实验发现 50 epoch 能得到低损失同时又不会过拟合训练集（或者尽我们所能不过拟合）的模型。
- INIT_LR：我们的初始学习率设置为 0.001。学习率控制着我们沿梯度前进的「步伐」。值越小说明步伐越小，值越大说明步伐越大。我们很快就将看到我们会使用 Adam 优化算法，随时间逐步降低学习率。。
- BS：我们将以 32 的批大小训练我们的网络。
- IMAGE_DIMS：所有输入图像的尺寸都会调整为 96x96，外加 3 个通道（RGB）。我们使用这样的维度进行训练，我们的网络架构输入维度也反映了这一点。当我们在之后一节使用示例图像测试我们的网络时，测试图像的维度也必须调整得和训练图像一样。



接下来是抓取我们的图像路径并随机打乱顺序。我们还将初始化分别用于保存图像本身以及服装种类和颜色的列表。



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaKSx1f1USF93a8SuSgIqIOCPic1hqzjs6hQxAKofyq8dpnQQicLJ3VBLibw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



接下来，我们将在 imagePaths 上循环，预处理图像并填充 data、categoryLabels 和 colorLabels 列表。



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaKEicohdKc4Z9KxO6OzMMj1eGccLCUmRSibzG6IOwfzfu0TqaVr12fKhSA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



这个在 imagePaths 上的循环是从第 54 行开始的。



在该循环内部，我们加载图像并将其尺寸调整为 IMAGE_DIMS。我们也将图像颜色通道的顺序从 BGR 转换成 RGB。为什么要做这样的转换？回想一下 build_category_branch 函数中的 FashionNet 类，其中我们在 lambda 函数/层中使用了 TensorFlow 的 rgb_to_grayscale 转换。因此，我们首先在第 58 行将图像转换成 RGB，最后将预处理后的图像加到 data 列表里。



接下来，依然在循环内，我们从当前图像所在的目录名称中提取颜色和类别标签（第 64 行）。



如果你想实际看看操作情况，只需要在你的终端启动 Python，然后按如下方式提供了一个样本 imagePath 来实验即可：



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaK8RZqicoOlGsqSOwtSz4CyOUsAAF6ebww3f676t8YVsoKAOqz0qtXoEw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



当然，你可以按照你想要的任何方式组织你的目录结构（但你必须相应地修改代码）。我最喜欢的两种方法包括：（1）为每个标签使用子目录，（2）将所有图像存储在同一个目录中，然后创建一个 CSV 或 JSON 文件将图像文件名映射到它们的标签。



然后将这三个列表转换成 NumPy 数组，将标签二值化，并将数据分成训练部分和测试部分。



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaK8WdBwR59BglKqnG8icsuxBpQOb36pMSXx3NyfaNSYgl6icBLflCHNwoA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



我们的最后一个预处理步骤（转换成一个 NumPy 数组并将原始像素强度调整到 [0, 1] 区间）可以一步完成，见第 70 行。



我们也将 categoryLabels 和 colorLabels 转换成 NumPy 数组（第 75-76 行）。这很有必要，因为接下来我们将使用 scikit-learn 的 LabelBinarizer 来将这些标签二值化（第 80-83 行），这是我们之前导入的工具。因为我们的网络有两个独立的分支，所以我们可以使用两个独立的标签 LabelBinarizer——这不同于多标签分类的情况，其中我们使用了 MultiLabelBinarizer（这同样来自于 scikit-learn）。



接下来，我们对我们的数据集执行一次典型的分割：80% 训练数据和 20% 的测试数据（第 87-96 行）。



接下来构建网络，定义独立的损失，并编译我们的模型：



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaKdTYlMsKIK7dxqLsWguibC4JQfmwn8q3kMLQA9RNwl294Nknta75PmJw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



在第 93-96 行，我们实例化了我们的多输出 FashionNet 模型。我们在创建 FashionNet 类和其中的 build 函数时解释过这些参数，但你还是要看看这里我们实际提供的值。



接下来，我们需要为每个全连接头定义两个 losses（第 101-104 行）。



定义多个损失是使用一个词典完成的，其使用了每个分支激活层的名称——这就是我们在 FashionNet 实现中给我们的输出层命名的原因！每个损失都使用类别交叉熵，这是分类类别大于 2 时训练网络使用的标准损失方法。



在第 105 行，我们还在另一个词典中定义了一个等值的 lossWeights（同样的名称键值具有相同的值）。在你的特定应用中，你可能希望某些损失的权重大于其它损失。



现在我们已经实例化了我们的模型并创建了我们的 losses + lossWeights 词典，接下来我们用学习率延迟实例化 Adam 优化器并 compile 我们的 model（第 110-111 行）。



接下来的代码就是启动训练过程：



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaKeVPI3GfUvwkDoicnWDDjerml7sw2NOIUSYYQ2icEia2wT8LM5vMw4rwAw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



回想一下第 87-90 行，我们将我们的数据分成了训练部分（trainX）和测试部分（testX）。在第 114-119 行，我们在提供数据的同时启动了训练过程。注意第 115 行我们以词典的形式传递标签。第 116 行和 117 行也是一样，我们为验证数据传递了一个二元组。以这种方式传递训练和验证标签是使用 Keras 执行多输出分类的要求。我们需要指示 Keras 明白哪些目标标签集合对应于网络的哪些输出分支。



使用我们的命名行参数（args["model"]），我们可以将序列化的模型保存到磁盘以备之后调用。



我们也能通过同样的操作将我们的标签二值化器保存为序列化的 pickle 文件：



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaKw0NM2ibuK5s0TC2v6HaHxbNzbsOYIHBf2lJQFbkot7z871gahUJS7GA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



使用命令行参数路径（args["categorybin"] 和 args["colorbin"]），我们将两个标签二值化器（categoryLB 和 colorLB）都以序列化 pickle 文件形式保存到了磁盘。



然后就是使用这个脚本绘制结果图表：



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaKBjzZrmOptNI6l5NH7leKYlv9ibemGwhJB44YdyGckCGvSCrTyKt9L1Q/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



上面的代码块负责绘制每个损失函数的损失历史图表，它们是分别绘制的，但叠放在一起，包括：



- 总体损失
- 类别输出的损失
- 颜色输出的损失



类似地，我们将准确度绘制成另一个图像文件：



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaKfpLC1lARVCgQiafQOmNdYepzQlxg0miax0f6K86hfuAVicYM6ZUVPlFYQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



我们的类别准确度和颜色准确度图最好分开看，所以它们是分开的，但放在同一个图片中。



**训练多输出/多损失 Keras 模型**



请确保你下载了本文附带的代码和数据集。



不要忘了：在本教程给出的下载内容中，我使用的是 Python 3.5 训练该网络。只要你保持一致（一直都用 Python 3.5 或 Python 3.6），你应该不会遇到 lambda 实现不一致的问题。你甚至可以运行 Python 2.7（尚未测试）。



打开终端。然后将下列命令粘贴进去，开始训练过程（如果你没有 GPU，你可能就得等一段时间，也许抽空喝杯啤酒？）：



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaKjctVAiahaS3Kkdib2cSaqNQdUC7X7GxibczCh844x6q3wLK719EQoWhYQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



对于我们的类别输出，我们得到：



- 在训练集上准确度为 99.31%
- 在测试集上准确度为 93.47%



对于颜色输出，结果为：



- 在训练集上准确度为 99.31%
- 在测试集上准确度为 97.82%



下面是每个损失的图表：



![img](https://mmbiz.qpic.cn/mmbiz_jpg/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaK1icrxlYtkGArXlQWcLWWj6endcZ4RPV165jRNwzqMkLvha56DlZFfHQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*图 7：使用 matplotlib 绘制的我们的 Keras 深度学习多输出分类训练损失图。为了便于分析，我们的总损失（上图）、服装类别损失（中图）和颜色损失（下图）是分开绘制的。*



还有我们的准确度图表：



![img](https://mmbiz.qpic.cn/mmbiz_jpg/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaKoZsgxJ4kvakiagpr6FhpiaiakZ2bFfLcH7v8So1L0KgfgsDFS8mojUjOg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*图 8：FashionNet 是一个用 Keras 训练的多输出分类网络。为了分析训练情况，最好是分开呈现准确度图表。上图为服装种类训练准确度图，下图为颜色训练准确度图。*



应用数据增强可以实现更高的准确度。



**实现多输出分类脚本**



现在我们已经训练好了我们的网络，接下来看一下如何将其应用于不属于我们的训练集的输入图像。



打开 classify.py，插入以下代码：



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaKAjIBNicF3dHOetkOXp6TVK2ekQMxqR6SY4DBmibribicknxFS0XAMHlYzw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



首先，我们导入所需的软件包，然后解析命令行参数：



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaKl2F5B8X5iac9lrokBjmeTsvrejyYUEQokmw5GjGK7s9UhrTVrHUXXrA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



我们有 4 个命令行参数，你需要这些参数来在你的终端上运行这个脚本：



- --model：我们刚刚训练好的序列化模型文件的路径（我们之前脚本的一个输出）
- --categorybin：种类标签二值化器的路径（之前脚本的一个输出）
- --colorbin：颜色标签二值化器的路径（之前脚本的一个输出）
- --image：测试图像文件的路径——这个图像来自我们的 examples/ 目录



然后我们载入图像并对其进行预处理：



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaKlYbicdCVUrIlJo3VDvCqibz8dibVrsDLzuZic33Mq5KzQlicpZ8icRBfxIXw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



在运行推理前，我们需要预处理图像。在上面的代码块中，我们加载了图像，为输出调整了图像大小，然后转换了颜色通道（第 24-26 行），这样我们就可以在 FashionNet 的 lambda 层中使用 TensorFlow 的 RGB 转灰度函数了。然后我们重新调整 RGB 图像的大小（再次调用我们训练脚本中的 IMAGE_DIMS），将其范围调整到 [0,1]，将其转换成一个 NumPy 数组，并为该批增加一个维度（第 29-32 行）。



这里的预处理步骤应该遵照训练脚本的预处理步骤，这是很重要的。



接下来，加载我们的序列化模型和两个标签二值化器：



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaKD2PezlslmeszsoxSwicr2clHtYSLzp0u85nCzylJO3kdsb3ibicFh7SdA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



在第 37-39 行，我们使用了 4 个命令行参数中的 3 个，加载了 model、categoryLB 和 colorLB。



现在（1）多输出 Keras 模型和（2）标签二值化器都已经放入了内存，我们可以分类图像了：



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaKwQyu1dClkueCXCOXSvdQmg0IWiahqco8icjTNGeia22V71LiawBLkMSHibg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



我们在第 43 行执行多输出分类，得到服装种类和颜色的概率（分别为 categoryProba 和 colorProba）。



注意：我没有把 include 代码包含进来，因为这样会显得很冗长，但你可以通过检查输出张量的名称来确定你的 TensorFlow + Keras 模型返回多个输出的顺序。参阅 StackOverflow 上的这个讨论了解更多详情：https://goo.gl/F2KChX。



然后，我们会为类别和颜色提取最高概率的索引（第 48-49 行）。



使用这些高概率索引，我们可以提取出类别名称（第 50-51 行）。



看起来有点太简单了，对不对？但应用使用 Keras 的多输出分类到新图像上就这么简单！



让我们给出结果来证明这一点：



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaK5RtHZntobpL2dOlNlnvcaGq1icDMvoia5zuU31pktN7tialtMSZcvxoMw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



我们在输出图像上显示结果（第 54-61 行）。结果在图像左上角以绿色文本显示。如果我们遇到了「红色裙子」，结果可能是：



- category: dress (89.04%)
- color: red (95.07%)



第 64-65 行也会将结果信息显示在终端上，之后输出图像显示在屏幕上（第 68 行）。



**执行使用 Keras 的多输出分类**



有趣的部分来了！



在这一节，我们将为我们的网络提供 5 张不属于训练集样本目录的图像。



按道理，我们的网络应该只能识别其中 2 张（黑色牛仔裤和红色衬衫）。我们的网络应该能轻松处理这样的种类和颜色搭配。



其余三张图像则是我们的模型从未见过的搭配——我们没使用红色鞋子、蓝色鞋子或黑色裙子训练过；但我们将试试多输出分类的效果。



首先从「黑色牛仔裤」开始——这个应该很简单，因为训练数据集中有很多类似图像。请确保以这样的方式使用 4 个命令行参数：



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaKymrp54TtESctCNqicr6Tia27Vq8E2yFeam6EeU4BFlBd5bI8PXJY0ia4w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



![img](https://mmbiz.qpic.cn/mmbiz_jpg/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaKqmLorRb8ibE0fsMlvrF3Ps0sxrac4vGkntuRIJNEibyRwPqxsjyGlfjA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*图 9：深度学习多输出分类可以识别不相交标签的组合，比如服装种类和服装颜色。我们的网络正确分类了这张图像：牛仔裤+黑色。*



和预计一样，我们的网络正确分类了这张图像：牛仔裤+黑色。



再来试试「红色衬衫」：



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaKYlnnCz3WyFFHoCp7Eblicj5nEflhR3NdBjGVu4bwdTU4nNBkCnKO06w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



![img](https://mmbiz.qpic.cn/mmbiz_jpg/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaKicWF6GgMKicwU4t0zuwaNgWaypzmOrZABIyEqVJgs5vmTwmD9VxRNAEQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*图 10：这张「红色衬衫」图像是一张不在我们的深度学习图像数据集中的测试图像。但我们的 Keras 多输出网络见过其它红色衬衫。它能轻松以 100% 的置信度分类这两个标签。*



结果在这两个类别标签上都达到了 100% 的置信度，我们的图像确实包含一件「红色衬衫」。请记住，我们的网络在训练过程中见过其它「红色衬衫」。



现在让我们回头想想。我们的数据集中原来没有「红色鞋子」，但却有「红色」的「裙子」和「衬衫」，还有「黑色」的「鞋子」。



那么我们的模型能不能看懂之前从未见过的同时包含「鞋子」和「红色」的图像呢？



来看结果：



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaKLUhBI3ntc4kRfGWr2fjCDSPXoVHHoAVUicZSj9fViazVWXpLVhica0DxQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



![img](https://mmbiz.qpic.cn/mmbiz_jpg/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaKkHmpbWrXdS3zPW6cb5rRFhIc4z3p36o7V28ruAjGShgw7qibPSIrIEQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*图 11：我们的深度学习多输出分类网络之前从未见过「红色」与「鞋子」的组合。在训练过程中，模型确实看到过鞋子（但是黑色的）；也看到过红色（但是衬衫和裙子）。让人惊喜的是，我们的网络能得到正确的多输出标签，将这张图像分类为「红色鞋子」。成功！*



正确！



看看图像中的结果，我们成功了。



我们已经有一个好开始了，虽然这个多输出组合是之前从未出现过的。我们的网络设计+训练是有效的，我们可以以很高的准确度识别「红色鞋子」。



接下来看看我们的网络能正确分类「黑色裙子」吗？记得吗，在之前的多标签分类教程中，当时的网络并没有得到正确的结果。



我认为这一次我们很可能成功，将以下代码输入终端：



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaKMqJjKbdkTUddyCM7KbteibfjqUAIIwYl3vzewhEGU66p28z2uQTT5fw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![img](https://mmbiz.qpic.cn/mmbiz_jpg/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaKvlPkgNRlibv7uQNBYRazklKc9rwH910CcaXwegpXOyic3z2hHxl6AMJw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*图 12：尽管「黑色裙子」图像并不包含在今天的数据集中，但我们仍然可以通过 Keras 和深度学习使用多输出分类来正确分类它们。*



看看这张图左上角的标签类别！



我们在种类和颜色上都得到了超过 98% 准确度的正确分类。我们已经实现了目标！



不要激动，让我们再试试另一个未见过的组合：「蓝色鞋子」。在终端输入同样的命令，只是将 --image 参数改为 examples/blue_shoes.jpg：



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaKLEHj5bqvHK2iaem19W0ksFPh1n0tjTQ64JkIZWpDSgmeGfNz03n6hFg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



![img](https://mmbiz.qpic.cn/mmbiz_jpg/KmXPKA19gW8oTHXOUcibM8SZ39R1AAWiaKW14edcJI03odlBmemOxhPsMHXcQDZ8N2F5liamRSDB6iaqpwAnUULepw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*图 13：尽管多标签分类可能无法分类不熟悉的标签组合，但多输出分类能很好地应对这样的任务。*



结果同样很好——我们的网络没在「蓝色鞋子」图像上训练过，但还是能使用多输出和多损失分类的两个子网络正确分类它们。



**总结**



在这篇文章中，我们学习了如何使用 Keras 深度学习库中的多输出和多损失函数。



为了完成我们的任务，我们定义了一个用于时装/服装分类的 Keras 架构 FashionNet。



FashionNet 架构包含两个分支：



- 一个分支负责分类给定输入图像的服装种类（比如衬衫、裙子、牛仔裤、鞋子等）
- 另一个分支负责分类该服装的颜色（黑色、红色、蓝色等）



分支在网络早期产生，实际上在同一个网络中创造了两个「子网络」，它们分别负责各自的分类任务。



最值得一提的是，多输出分类让我们可以解决之前的多标签分类的遗留问题，即：我们在 6 种类别（黑色牛仔裤、蓝色裙子、蓝色牛仔裤、蓝色衬衫、红色裙子、红色衬衫）上训练了我们的网络，但得到的网络却无法分类「黑色裙子」，因为该网络之前从未见过这样的数据组合！



通过创建两个全连接头和相关的子网络（如有必要），我们可以训练一个头分类服装种类，另一个头负责识别颜色——最终得到的网络可以分类「黑色裙子」，即使它之前从未在这样的数据上训练过！



但还是要记住，你应该尽力提供你想要识别的每个类别的样本训练数据——深度神经网络虽然很强大，但可不是「魔法」！



你应该尽力保证适当的训练方式，其中首先应该收集合适的训练数据。



这就是我们的多输出分类文章，希望你喜欢！



代码下载链接：https://www.getdrip.com/forms/749437062/submissions



*原文链接：https://www.pyimagesearch.com/2018/06/04/keras-multiple-outputs-and-multiple-losses/*





# 相关

- [教程 | 使用Keras实现多输出分类：用单个模型同时执行两个独立分类任务](https://mp.weixin.qq.com/s?__biz=MzU1NTUxNTM0Mg==&mid=2247489198&idx=1&sn=5ff799619e50a99c0ef5ff1854224145&chksm=fbd27a0fcca5f319abecb1671022bee93a82f75226093c36a5680c9a813d914d21afd361f0de&mpshare=1&scene=1&srcid=0814Q7NRZFV0EU8SQLhEheuN#rd)
