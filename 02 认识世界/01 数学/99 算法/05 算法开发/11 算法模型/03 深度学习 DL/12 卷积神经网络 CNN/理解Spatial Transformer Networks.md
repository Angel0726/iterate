---
title: 理解Spatial Transformer Networks
toc: true
date: 2019-11-17
---
# 理解Spatial Transformer Networks


随着深度学习的不断发展，卷积神经网络(CNN)作为计算机视觉领域的杀手锏，在几乎所有视觉相关任务中都展现出了超越传统机器学习算法甚至超越人类的能力。一系列CNN-based网络在classification、localization、semantic segmentation、action recognization等任务中都实现了state-of-art的结果。



对于计算机视觉任务来说，我们希望模型可以对于物体姿势或位置的变化具有一定的不变性，从而在不同场景下实现对于物体的分析。传统CNN中使用卷积和Pooling操作在一定程度上实现了平移不变性，但这种人工设定的变换规则使得网络过分的依赖先验知识，既不能真正实现平移不变性(不变性对于平移的要求很高)，又使得CNN对于旋转，扭曲等未人为设定的几何变换缺乏应有的特征不变性。

![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClzCshaGEJ6NKvjOwvDJDp7LIDwowTXf8He4vaNyMkvzE2fPTialJ73viaUesrb3ibZ7WK11HhtOrKBw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



STN作为一种新的学习模块，具有以下特点:

(1)   为每一个输入提供一种对应的空间变换方式(如仿射变换)

(2)   变换作用于整个特征输入

(3)   变换的方式包括缩放、剪切、旋转、空间扭曲等等

具有可导性质的STN不需要多余的标注，能够自适应的学到对于不同数据的空间变换方式。它不仅可以对输入进行空间变换，同样可以作为网络模块插入到现有网络的任意层中实现对不同Feature map的空间变换。最终让网络模型学习了对平移、尺度变换、旋转和更多常见的扭曲的不变性，也使得模型在众多基准数据集上表现出了更好的效果。



空间变换网络:







![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClzCshaGEJ6NKvjOwvDJDp7x7ybXlicUQ0efa5ph284CGmLibbfUeiaZ8aKbZ2FRr1uy0LyWicjKK6yuA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

ST的结构如上图所示，每一个ST模块由Localisation net， Grid generator和Sample组成， Localisation net决定输入所需变换的参数θ，Grid generator通过θ和定义的变换方式寻找输出与输入特征的映射T(θ)，Sample结合位置映射和变换参数对输入特征进行选择并结合双线性插值进行输出，下面对于每一个组成部分进行具体介绍。

Localisation net

Localisation net输入为一张Feature map: U∈RH×W×C 。经过若干卷积或全链接操作后接一个回归层回归输出变换参数θ。θ的维度取决于网络选择的具体变换类型，如选择仿射变换则θ∈R2×3。如选择投影变换则θ∈R3×3。θ的值决定了网络选择的空间变换的“幅度大小”。

Grid generator

Grid generator利用localisation层输出的θ， 对于Feature map进行相应的空间变换。设输入Feature map U每个像素位置的坐标为(xis ，yis )，经过ST后输出Feature map每个像素位置的坐标为(xit ，yit )， 那么输入和输出Feature map的映射关系便为(选择变换方式为仿射变换)

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClzCshaGEJ6NKvjOwvDJDp71lESGUJlhib9XVrgJkDjQRK6AAMr6HlpCuy5WwCuJXNsRjHUXLrs1dw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

也就是说，对于输出Feature map的每一个位置，我们对其进行空间变换(仿射变换)寻找其对应与输入Feature map的空间位置，到目前为止，如果这一步的输出为整数值(往往不可能)，也就是经过变换后的坐标可以刚好对应原图的某些空间位置，那么ST的任务便完成了，既输入图像在Localisation net和Grid generator后先后的确定了空间变换方式和映射关系。

![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClzCshaGEJ6NKvjOwvDJDp7yvsvO5aLHaAOMYQial5hkzPEHmPV0lNbSuDtKic9FlRv8hEPhZjMKXSA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

但是一些读者看到这可能有一个疑问，这个嵌入的ST网路如何通过反向传播进行参数的训练?没错，如果仅仅包含上述的两个过程，那么ST网络是无法进行反向传播的，原因就是我们上述的操作并不是直接对Feature map进行操作，而是对feature position进行计算，从而寻找输入到输出的对应关系。而feature position对应到feature score是离散的，即feature position进行微小变化时，输出O[x+△x,y]值是无法求解的(图像的计算机存储为离散的矩阵存储)。这里论文作者使用了笔者认为STN最精髓算法，双线性插值算法。

Sample

经过以上的两步操作后，输出的Feature map上每一个像素点都会通过空间变换对应到输入Feature map的某个像素位置，但是由于feature score对于feature position的偏导数无法计算，因而我们需要构造一种position->score的映射，且该映射具有可导的性质，从而满足反向传播的条件。即每一个输出的位置i，都有:

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClzCshaGEJ6NKvjOwvDJDp7aiaPKxCbL4t8N6yywgibFqk3k02NUptEaTTI4LtOB1aFevO8KNhDWlWQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

其中Unm为输入位置(n,m)对应的score值，k为某种可导函数， Φ为可导函数参数，通过如上的构造方式，我们便可以实现对于![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClzCshaGEJ6NKvjOwvDJDp7ic2csmNicPfBHncqGuMbIkI4CIV5OLCLUG4ZTicaPxibPricX37XoKY8xdQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)等网络参数的求导，从而满足反向传播的要求。如

论文使用的双线性插值法公式如下:

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClzCshaGEJ6NKvjOwvDJDp7rJEy0vKM8pE8BMV6j8rUq0rYemmyfF1xOgpkBedYsgQ7fs4qAZYaDA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

我们可以看到，通过max函数选择与输出(xis ,yis )距离小于1的像素位置，距离(xis ,yis)越近的点被分配了越高的权重，实现了使用(xis ,yis)周围四个点的score计算最终score，由于max函数可导，我们可以有如下偏导数计算公式:

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClzCshaGEJ6NKvjOwvDJDp7ZZBOLas8GfRuEk6Mcfz7lVVdDcwHm83icYzuJ3HJ1ecQhqojJSJXa1Q/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClzCshaGEJ6NKvjOwvDJDp7lpAyd9tq7PVVQ48OBFdw9MbvlPiamoeOahg44bRl19h8aje8dgJia2Yg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

对于yis的求导与xis类似，因而我们可以求得对于的偏导:

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClzCshaGEJ6NKvjOwvDJDp7qPxPwBEu7Wd2uKlVyFhgj2Yut1zEEKoFBtFZpHicXK3h2bzpJZ0GMgA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

到目前为止，我们证明了ST模块可以通过反向传播完成对于网络梯度的计算与参数的更新。



算法分析(STN)







(1)   STN作为一种独立的模块可以在不同网络结构的任意节点插入任意个数并具有运算速度快的特点，它几乎没有增加原网络的运算负担，甚至在一些attentive model中实现了一定程度上的加速。



(2)   STN模块同样使得网络在训练过程中学习到如何通过空间变换来减少损失函数，使得模型的损失函数有着可观的减少。



(3)   STN模块决定如何进行空间变换的因素包含在Localisation net以及之前的所有网络层中。



(4)  网络除了可以利用STN输出的Feature map外，同样可以将变换参数作为后面网络的输入，由于其中包含着变换的方式和尺度，因而可以从中得到原本特征的某些姿势或角度信息等。



(5)   同一个网络结构中，不同的网络位置均可以插入STN模块，从而实现对与不同feature map的空间变换。



(6)   同一个网络层中也可以插入多个STN来对于多个物体进行不同的空间变换，但这同样也是STN的一个问题:由于STN中包含crop的功能，所以往往同一个STN模块仅用于检测单个物体并会对其他信息进行剔除。同一个网络层中的STN模块个数在一定程度上影响了网络可以处理的最大物体数量。



实验结果:







论文中在手写数字识别、街景数字识别、高维度物体变换、鸟类识别等多个任务上都进行了实验，如对于手写数字识别:

![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClzCshaGEJ6NKvjOwvDJDp7jmMPebnjvBgELOCFT1iaQHZTcQZyfmztWLlt3cMZT2KPdURxdf3RBXw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



原始数据集选择Mnist， 分别进行了旋转（R）、旋转、缩放、平移（RTS），透射变换(P）， 弹性变形（E）四种方式对数据集进行了预处理，选用FCN和CNN作为baseline，分别使用仿射变换（Aff )、透射变换（Proj )、以及薄板样条变换（TPS )的空间变换方式进行STN模块的构造，我们可以看出STN-based网络具有全面优于baseline的错误率。右图为部分输入数据经过STN变换后的结果。可以看出STN可以学习到多种原始数据的位置偏差并进行调整。



STN模块的Pytorch实现:







这里我们假设Mnist数据集作为网络输入:



(1)首先定义Localisation net的特征提取部分，为两个Conv层后接Maxpool和Relu操作:

![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClzCshaGEJ6NKvjOwvDJDp7jNLqcVEexNuthKia9BiaXQIWjCPoL2ia9H7KJ6t0ticc24V6mM8yrpJzibQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

(2)定义Localisation net的变换参数θ回归部分，为两层全连接层内接Relu:

![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClzCshaGEJ6NKvjOwvDJDp7dGYYMNJszK6UOGH4VSLnYZiaaoxEsu6dYyuzrCIDBv17XXW229ZkicYg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

(3)在nn.module的继承类中定义完整的STN模块操作:

![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClzCshaGEJ6NKvjOwvDJDp7qr1EVfXzY2iaJChgxb69tRniaiaD0TDUKUJLCFyZWch6qGica1BokCFVEQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)









参考文献:

[1] Max Jaderberg, Karen Simonyan, Andrew Zisserman, Koray Kavukcuoglu. Spatial Transformer Networks. CVPR, 2016

[2] Ghassen HAMROUNI. Spatial Transformer Networks Tutorial:©Copyright2017，PyTorch.https://pytorch.org/tutorials/intermediate/spatial_transformer_tutorial.html


# 相关

- [理解Spatial Transformer Networks](https://mp.weixin.qq.com/s?__biz=MzU4MjQ3MDkwNA==&mid=2247486133&idx=1&sn=31c64e83511ad89929609dbbb0286890&chksm=fdb69722cac11e34da58fc2c907e277b1c3153a483ce44e9aaf2c3ed468386d315a9b606be40&mpshare=1&scene=1&srcid=08100eXOxYG1ZZASk34bpmIn#rd)
