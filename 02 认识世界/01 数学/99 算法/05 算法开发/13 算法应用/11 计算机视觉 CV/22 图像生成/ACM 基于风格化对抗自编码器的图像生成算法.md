---
title: ACM 基于风格化对抗自编码器的图像生成算法
toc: true
date: 2019-11-17
---
# ACM 基于风格化对抗自编码器的图像生成算法


- 论文作者：阿里 赵一儒、邓兵、黄建强、卢宏涛、华先胜
-


要解决的问题：

解决城市大脑交通视频数据样本不足的问题，因此提出了一种图像生成算法。

受条件对抗生成网络和风格迁移学习的启发，采用内容提取网络和风格提取网络分别从内容图片和风格图片中提取特征，将两者融合后，通过图片生成网络获得融合相应内容和风格的图片。




## **摘要**



在本论文中，我们提出了一种用于自动图像生成的基于自编码器的生成对抗网络（GAN），我们称之为“风格化对抗式自编码器”。不同于已有的生成式自编码器（通常会在隐向量上施加一个先验分布），我们提出的方法是将隐变量分成两个分量：风格特征和内容特征，这两个分量都是根据真实图像编码的。这种隐向量的划分让我们可以通过选择不同的示例图像来任意调整所生成图像的内容和风格。



此外，这个 GAN 网络中还采用了一个多类分类器来作为鉴别器，这能使生成的图像更具真实感。我们在手写数字、场景字符和人脸数据集上进行了实验，结果表明风格化对抗式自编码器能实现优异的图像生成结果，并能显著改善对应的监督识别任务。



## **1 引言**



生成式自然图像建模是计算机视觉和机器学习领域的一个基本研究问题。早期的研究更关注生成网络建模的统计原理，但由于缺乏有效的特征表征方法，相应结果都局限于某些特定的模式。深度神经网络已经展现出了在学习表征方面的显著优势，并且已经被证明可有效应用于鉴别式视觉任务（比如图像分类和目标检测），与贝叶斯推理或对抗训练一起催生出了一系列深度生成模型。



研究表明，正则化神经网络的在实际工作中的表现通常优于无约束的网络。常用的正则化形式包括 L1 范数 LASSO、L2 范数岭回归（ridge regression）以及 dropout 等一些现代技术。尤其是对于自编码器神经网络，研究者近期已经提出了相当多的正则化方法。但是，所有这些正则化方法都会在隐变量（也被称为隐藏节点）上施加一个先验分布，经常使用的是高斯分布。



对于相对简单的生成任务（比如建模灰度数字图像）而言，这种方法效果很好，但却不适合用于生成彩色字母数字图像或人脸等复杂图像，因为这些图像的隐变量的真实分布是不可见的，也无法用简单的模型进行建模。



如图 1 所示，我们在本论文中提出了一种名为风格化对抗式自编码器（SAAE）的全新生成模型，该模型是使用一种对抗式方式来训练风格化自编码器。



不同于已有的自编码器，我们会将隐向量分成两部分，一部分与图像内容有关，另一部分与图像风格有关。内容特征和风格特征都是根据示例图像编码的，并且不会在隐变量的分布上使用任何先验假设。带有给定内容和风格的目标图像可以根据组合起来的隐变量解码得到，这意味着我们可以通过选择不同的示例内容和/或风格图像来调整输出图像。



此外，受 [1, 2, 3] 中方法的启发，我们在模型训练阶段采用了一种对抗式的方法。我们的 GAN 网络没有使用典型的二元分类器作为鉴别器，而是使用了一个多类分类器，它在鉴别真实图像和虚假图像时能更好地建模生成图像的变化情况。此外，由于 GAN 模型训练是博弈形式的最小-最大目标，所以非常难以收敛，因此我们根据经验开发出了一种有效的三步式训练算法，可以改善我们提出的 GAN 网络的收敛表现。



![img](https://mmbiz.qpic.cn/mmbiz_png/LwZPmXjm4WyVsd5w0045bDuBTROBRpWicuIGLdNVYfQZJicbTvaY4EmeyNeqoHfZ8MlcDC5ElfX2SOIIdsJeHWfg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

图 1：我们的模型的图示，其分别从内容图像和风格图像中提取特征，然后再将这些特征融合起来，解码得到目标图像。多类鉴别器会迫使生成的图像更具真实感。



本工作的主要贡献可以总结为：



- 我们提出了一种全新的深度自编码器网络，它可以分别编码来自两个示例图像的内容特征和风格特征并根据这两个特征解码得到新图像。
- 使用了多类分类器作为鉴别器，这能更好地建模生成的图像的变化情况，并能有效地迫使生成网络生成更具真实感的结果。
- 我们开发了一种三步式训练策略，以确保我们提出的风格化对抗式自编码器的收敛。



## **2 风格化对抗式自编码器**



为方便起见，我们将使用文本字符图像生成（比如场景文本生成等）作为背景应用来介绍我们的算法，但我们还会在实验部分展示更多应用（比如人脸生成）。我们的目标是通过定义和训练一个神经网络，根据两张示例图像（内容图像 c和风格图像 s）来生成图像。就字符图像生成而言，内容图像是指没有任何风格或纹理或背景的合成字符图像，比如 A 到 Z，0 到 9；风格图像是一张示例图像，比如是一张真实的单词图像。



正如前面提到的，我们将揭示了真实数据的先验分布的隐变量分成两个部分：风格特征和内容特征。内容特征是从内容图像中导出的（通过一个卷积网络），而风格特征是从风格图像中导出的。



### 2.1 生成器



生成网络由两个编码器（Enc_c 和Enc_s）和一个解码器（Dec）构成。其中 Enc_c 将内容图像编码成内容隐含表征或特征z_c，Enc_s 将风格图像编码成风格隐含表征或特征 z_s。Dec 解码组合后的隐含表征并得到输出图像。为了方便起见，我们使用生成器 G 表示 Enc_c、Enc_s 和 Dec 的组合。

###

### 2.2 鉴别器



已有 GAN 中的鉴别器的输出是表示该输出 x 是真实图像的概率 y = Dis(x) ∈ [0,1]。而鉴别器 D 的训练目标是最小化二元交叉熵：L_{dis} = −log(Dis(x))−log(1−Dis(G(z)))。



G 的目标是生成 D 无法将其与真实图像区分开的图像，即最大化 Ldis。前面已经提到，已有的 GAN 网络在 D 中使用二元分类器来确定图像是真实图像还是生成图像。但是，将所有真实图像放入一个大型的正例类别中将无法利用这些训练图像的内在形义结构。因此，我们提出使用多类分类器作为鉴别器，该分类器将确定输入是生成图像还是属于某个特定的真实图像类别（比如特定字符）。

###

### 2.3 网络架构



卷积神经网络（CNN）已经在特征表征和图像生成方面展现出了巨大的优势，我们提出的 SAAE 网络就基于 CNN 架构，如图 2 所示。



![img](https://mmbiz.qpic.cn/mmbiz_png/LwZPmXjm4WyVsd5w0045bDuBTROBRpWicsLqp0DAyp5CDzgfM1EVJ1qIYDL3jp75s3ZdNtFo1LJTicK3hm0Enw8w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

图 2：网络架构



实际上，我们提出的生成网络包含两个特征提取网络流程，之后再跟上一个生成网络。内容特征提取器和风格特征提取器都有三个无下采样的卷积层，这样能尽可能多地保留示例图像的细节信息。输入的风格图像和内容图像可能有不同的尺寸。



比如，当生成场景文本字符图像时，内容图像是含有一个字符的图像，风格图像是含有一个词或多个字符的图像。在三个卷积层之后，风格特征图（feature map）的形状会由一个全连接层重新调整为风格特征向量。为了与根据内容图像解码得到的内容特征图拼接到一起，风格特征向量需要重新调整回一个特征图，且该特征图与内容特征图具有一样的尺寸。



内容特征提取网络没有任何全连接层，因为内容图像的二维空间信息需要保留。我们在通道维度中合并内容特征图和风格特征图，这意味着组合后的特征图有一半通道来自内容特征，另一半则来自风格特征。之后，生成网络使用三个卷积层将组合后的特征图解码成一张目标字符图像。



鉴别网络是一个常见的 CNN 分类器，包含三个卷积层，其中第一个卷积层后有一个 2×2 最大池化层，最后一个卷积层后有两个全连接层。鉴别器的输出层是一个 (k+1) 维的向量，表示输入图像属于每个类别的概率（真实图像有 k 类，虚假图像占 1 类）。



我们在每个卷积层上都应用了批归一化，这能加快训练阶段的收敛速度。除最后一层之外的每一层都使用了 Leaky ReLU，最后一层使用了 sigmoid 来将每个输出投射到 [0,1] 区间中（作为概率）。

###

### 2.4 训练策略



受 [4] 中所用的分步训练的启发，我们提出了一种三步式训练策略来优化我们的模型。这个三步式优化策略能帮助我们得到稳定的训练结果。

##

## **3 实验**



我们使用 4 种不同的方法评估了我们的方法：在 MNIST 数据集上计算对数似然以衡量 SAAE 模型拟合数据分布的能力；在人脸生成任务上展示视觉属性迁移；在场景文本数据集上评估 SAAE 模型；为监督识别任务生成训练数据。

###

### 3.1 对数似然分析



受 [1,3] 中评估流程的启发，我们评估了作为生成模型的 SAAE 拟合数据分布的表现，具体方法是计算生成图像的估计分布与 MNSIT 测试集分布的对数似然。



表 1 比较了 SAAE 与六种当前最佳方法的对数似然结果。我们的方法在这一标准上表现最优，超过 AAE 大约 89。



![img](https://mmbiz.qpic.cn/mmbiz_png/LwZPmXjm4WyVsd5w0045bDuBTROBRpWicmmtDkGic7jGrOk0ibflibJfBXFqnYfjNvyshK6VB5QjAwpBUyIBXGGicdQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

表 1：测试数据在 MNIST 数据集上的对数似然。值越高越好。最后两行结果来自我们的方法，分别使用了二元鉴别器和多类鉴别器。这里报告的数值是样本在测试集上的平均对数似然以及在多次实验结果计算得到的均值的标准误差。



遵照之前的方法，我们在图 3 中展示了一些来自训练后的 SAAE 生成器的样本。最后一列是与倒数第二列的生成图像最接近的训练图像（用像素级别的欧氏距离来度量），以证明 SAAE 模型没有单纯地记忆训练集。



![img](https://mmbiz.qpic.cn/mmbiz_png/LwZPmXjm4WyVsd5w0045bDuBTROBRpWicfV4OHWBO47Qu5AicDEXA5jQaFEoMtwVShicT5NyGUcibrPGmzWAjP1OQw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

图 3：我们的 SAAE 模型生成的样本示例

###

### 3.2 基于属性条件的人脸生成



我们在 Labeled Faces in the Wild（LFW）数据集上评估了我们的模型在人脸图像生成任务上的表现。



如图 4 所示，生成的样本在视觉上与属性迁移一致。比如，如果改变“眼镜”这样的属性，整体外观仍然能保存完好，但眼部区域会出现差异。



![img](https://mmbiz.qpic.cn/mmbiz_png/LwZPmXjm4WyVsd5w0045bDuBTROBRpWicwzy7qDV74F0LnL97cMd4aBicMqW5hJaGGHNCZa6iaEDpInUFFdKYFusA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

图 4：基于属性条件的图像生成，分成六个组（性别、年龄、肤色、表情、眼镜和眼睛大小）。



### 3.3 模型样本



我们在 IIIT 5k-word（IIIT5K）数据集和中国汽车牌照（PLATE）数据集上评估了我们的 SAAE 模型。



图 5 展示了我们的模型和 DCGAN 模型生成的图像中随机取出的样本，同时也给出训练数据以便比较。SAAE 生成的样本看起来更像字符而且有更清晰的边缘和背景。



![img](https://mmbiz.qpic.cn/mmbiz_png/LwZPmXjm4WyVsd5w0045bDuBTROBRpWic0GSt0ibmzazV0qIeEqW5hRncJmM8o4x7LAJyZSOsYIiaJT66tMCp6n3w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

图 5：SAAE 和 DCGAN 的训练数据和模型样本。上行：IIIT5K 数据集，下行：PLATE 数据集



为了可视化我们的风格化对抗式自编码器的风格化属性，我们在图 6 中展示了几组生成样本，IIIT5K 和 PLATE 数据集上的都有。在每个数据集中，我们都选择了一张示例风格图像并遍历了所有的内容图像和标签。结果表明，SAAE 模型可以将示例风格图像的字符风格迁移给内容图像。



![img](https://mmbiz.qpic.cn/mmbiz_png/LwZPmXjm4WyVsd5w0045bDuBTROBRpWic3CIwUVurrPaRficagByJndqBjtUdVsicyiaoDSibyMnFtn1icLhn20A368w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

图 6：给定一张风格图像而生成的样本。上行：IIIT5K 数据集。下行：PLATE 数据集。对于每组生成样本，风格图像在左上角给出，用红色方框标出。对于 PLATE 数据集，我们因为隐私原因隐藏了汽车牌照的第一个汉语字符。



### 3.4 用于监督学习的数据生成



深度神经网络（DNN）已经在监督学习方面表现出了显著的优越性，但它却依赖于大规模有标注训练数据。在小规模训练数据上，深度模型很容易过拟合。我们还使用 SAAE 模型为识别中国汽车牌照任务生成了训练数据。



我们通过测量在 DR-PLATE 数据集上的识别准确度而对数据生成的质量进行了评估。图 7 表明加入到训练数据集中的生成数据越多，模型收敛得越慢，但分类准确度却越来越好。这个结果表明我们的 SAAE 模型能够通过生成数据提升监督学习的表现。



![img](https://mmbiz.qpic.cn/mmbiz_png/LwZPmXjm4WyVsd5w0045bDuBTROBRpWicdQSqGc7UwVfT5kiczw9hWJ0dAtIH1bia6bvPAhS2tNA0BDxkVmvqicfUw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

图 7：在不同训练集上对应迭代次数的识别准确度

##

## **4 结论**



未来的研究重点是优化网络结构，以实现更高的生成质量。将这一框架扩展到其它应用领域（比如半监督特征学习）也会是一个有趣的研究方向。



# 相关

- [ACM MM 论文 | 基于风格化对抗自编码器的图像生成算法](https://mp.weixin.qq.com/s?__biz=MzU5ODUxNzEyNA==&mid=2247483875&idx=1&sn=d476fc5def26f234d92ab43c45f95ef3&chksm=fe43b508c9343c1e8d032e4cf46ff82861370848624e4b60d0b433ef22bdb6eb6b02ba6bc697&mpshare=1&scene=1&srcid=0802lGX1cEDL1ef3YMqzSPJk#rd)
- Ian Goodfellow, Jean Pouget-Abadie, Mehdi Mirza, Bing Xu, DavidWarde-Farley,Sherjil Ozair, Aaron Courville, and Yoshua Bengio. 2014. Generative adversarialnets. In Advances in Neural Information Processing Systems. 2672–2680.
- Anders Boesen Lindbo Larsen, Søren Kaae Sønderby, and Ole Winther. 2015.Autoencoding beyond pixels using a learned similarity metric. arXiv preprint arXiv:1512.09300 (2015).
- Alireza Makhzani, Jonathon Shlens, Navdeep Jaitly, and Ian Goodfellow. 2015.Adversarial autoencoders. arXiv preprint arXiv:1511.05644 (2015).
- Shaoqing Ren, Kaiming He, Ross Girshick, and Jian Sun. 2015. Faster R-CNN:Towards real-time object detection with region proposal networks. In Advances in neural information processing systems. 91–99.
