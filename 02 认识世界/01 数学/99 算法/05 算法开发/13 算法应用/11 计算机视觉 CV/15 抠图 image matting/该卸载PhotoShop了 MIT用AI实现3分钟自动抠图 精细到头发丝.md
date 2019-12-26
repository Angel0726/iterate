---
title: 该卸载PhotoShop了 MIT用AI实现3分钟自动抠图 精细到头发丝
toc: true
date: 2019-11-17
---
# 该卸载PhotoShop了 MIT用AI实现3分钟自动抠图 精细到头发丝

MIT CSAIL的研究人员开发了一种基于深度学习的图像编辑工具，能够自动抠图，替换任何图像的背景。他们称之为“图像软分割”，这个系统能够分析原始图像的纹理和颜色，仅需3~4分钟，生成非常自然、真实的图像，其效果不输专业人士用Photoshop的制作。


是时候卸载你的PS软件了。



最近，MIT计算机科学与人工智能实验室（CSAIL）的研究人员开发了一种AI辅助的图像编辑工具，它可以自动抠图，替换任何图像的背景。



像这样：



![img](https://mmbiz.qpic.cn/mmbiz_gif/UicQ7HgWiaUb1LAic6ibFeAv3R5AZamf2TTGcQccHw6UKor7I8yhNaicckIopWx0U2JvKC8r2PCbJZKYoAtLJ9y74Lw/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)



和这样：



![img](https://mmbiz.qpic.cn/mmbiz_gif/UicQ7HgWiaUb1LAic6ibFeAv3R5AZamf2TTGQBa0OuEohIpzC0qGf9ic5X1sc88mGVFuKIicKM9N6EW9SlaBgq57uHXg/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)



要使抠完的这些图像看起来很逼真并不是一件容易的事，因为图像编辑必须要成功捕捉前景和背景之间微妙的审美转换点，这对于**人类头发**等复杂材质来说尤其困难。



下图除外。



![img](https://mmbiz.qpic.cn/mmbiz_gif/UicQ7HgWiaUb1LAic6ibFeAv3R5AZamf2TTGLKPRR5NdrrLOWp7xSuKdpG1yNsjFz6B44FF5899kNxh7JqgAtibY4yA/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)



“处理这些图像的棘手之处在于，图像中每个像素并不是全部只属于一个物体。”麻省理工学院计算机科学与人工智能实验室（CSAIL）的访问研究员Yagiz Aksoy说。“很多时候，我们**很难确定哪些像素是背景的一部分，哪些像素是特定的人的一部分**。”



除了经验最丰富的编辑人员之外，其他人都很难把控这些细节。但是在最近的一篇新论文中，Aksoy和他的同事展示了一种利用机器学习让照片编辑过程自动化的方法，而且表示这种方法也可用于视频处理。



该团队提出的方法可以将拍摄的图像自动分解为一组不同的图层，图层之间通过一系列**“软过渡”（soft transitions）**相分隔。



他们把这个系统命名为**“语义软分割”（semantic soft segmentation，SSS）**，它能够分析原始图像的纹理和颜色，并将其与神经网络收集的有关图像中实际目标的信息相结合。



这一技术有多牛？看下面的视频体会一下：







3分钟AI自动抠图，彻底抛弃PhotoShop





![img](https://mmbiz.qpic.cn/mmbiz_png/UicQ7HgWiaUb1LAic6ibFeAv3R5AZamf2TTGcT49SsNibDiaQ0Ky9Q8mVQVIMiciankBf3YkKNSzbZWb507jVh3wxMgeyg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



编辑器将图像中的对象和背景分割成不同的部分，以便于选择。但不像大多数图片编辑软件需要式样磁性套索或魔术套索工具，MIT开发的AI工具并不依赖于用户输入的上下文，你不必跟踪一个对象或放大并捕捉精细细节。AI可以自动实现这一过程。



这个过程从**神经网络估计图像的区域和特征**开始：



![img](https://mmbiz.qpic.cn/mmbiz_png/UicQ7HgWiaUb1LAic6ibFeAv3R5AZamf2TTGkffRkwygGsP84iaY8UticEAJkmESticD1DOjJlkkp1R4jcpiba9ialdfObQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



然后，神经网络检测到“soft transitions”，例如狗狗的毛发和草。以前这个过程必须手动去做。



![img](https://mmbiz.qpic.cn/mmbiz_png/UicQ7HgWiaUb1LAic6ibFeAv3R5AZamf2TTGPYGCLibBib2lK0WibmeXdHxYZ4VOLMDUIETcm7nibvTLniaUKyCbhdMicaiaw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



然后通过颜色将图像中的像素相互关联：



![img](https://mmbiz.qpic.cn/mmbiz_png/UicQ7HgWiaUb1LAic6ibFeAv3R5AZamf2TTGmhxtKjiaaCFC5vj6XxIyicw7uibvBtI5EbBdxJmW3HQgEibUzMTz028G7g/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



这些信息与神经网络检测到的特征相结合，对图像的层进行估计。



![img](https://mmbiz.qpic.cn/mmbiz_png/UicQ7HgWiaUb1LAic6ibFeAv3R5AZamf2TTG2cTNQcGMnbib0WSx9cfGe2PaCoFVsK15NEKvqcFWNlmw181QKuubHJw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



经过这一系列处理，现在，可以实现AI自动抠图并更换背景了。



![img](https://mmbiz.qpic.cn/mmbiz_gif/UicQ7HgWiaUb1LAic6ibFeAv3R5AZamf2TTGQBa0OuEohIpzC0qGf9ic5X1sc88mGVFuKIicKM9N6EW9SlaBgq57uHXg/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)





研究人员表示，这样自动处理一张640×480的图像需要**3~4分钟**。



“一旦计算出这些软分割段，用户就不必手动套索，也不用对图像的特定图层的外观进行单独修改，”Aksoy说道，他在上周与温哥华举办的SIGGRAPH计算机图形会议上发表了该技术的论文。“这样一来，更换背景和调整颜色等手动编辑任务将变得更加容易。”



当然，这个魔术一般的工具背后涉及许多复杂的算法和计算，我们将在后文介绍。该团队使用神经网络来处理图像特征和确定图像的柔化边缘。



技术细节：图像“软分割”技术炼成大法





该方法最重要的是**自动生成输入图像的软分割**，也就是说，将输入图像分解成表示场景中对象的层，包括透明度和软过渡（soft transitions）。



![img](https://mmbiz.qpic.cn/mmbiz_png/UicQ7HgWiaUb1LAic6ibFeAv3R5AZamf2TTGfaRaVdIz58k59An8vx4561KDXk9d8OBEUbiaZds5xGaMsjqdLcmvshg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



图2：SSS方法的概述



如上图所示，对于输入图像，我们要生成每个像素的**超维语义特征向量**（hyperdimensional semantic feature vectors），并使用纹理和语义信息定义图形。图形构造使得相应的Laplacian矩阵及其特征向量揭示了语义对象和它们之间的软过渡（soft transitions）。



我们使用特征向量来构建一组初始的**软分割**（soft segments），并将它们组合起来得到语义上有意义的分割。最后，我们对soft segments进行细化，使其可用于目标图像编辑任务。



**非局部颜色亲和性（Nonlocal Color Affinity）**



我们定义了一个额外的 low-level affinity，表示基于颜色的长期交互。



这种亲和性（affinity）基本上确保了具有非常相似的颜色的区域在复杂场景结构中保持关联，其效果如下图所示。



![img](https://mmbiz.qpic.cn/mmbiz_png/UicQ7HgWiaUb1LAic6ibFeAv3R5AZamf2TTGTsV14pHZKLlOKZjVvgW3OXQQ6Pto37YSPYibkWicrwZR43HMdZB1piazw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)





**高级语义亲和性（High-Level Semantic Affinity）**



虽然非局部颜色亲和为分割过程增加了大范围的交互，但它仍然属于低级别特征。我们的实验表明，在没有附加信息的情况下，在分割中仍然会经常对不同对象的相似颜色的图像区域进行合并。



为了创建仅限于语义相似区域的分割片段，我们添加了一个**语义关联项**，对属于同一场景对象的像素进行分组，并尽量防止来自不同对象的像素的混杂。我们在目标识别领域的先前成果的基础上，在每个像素上计算与底层对象相关的特征向量。



我们还定义了超像素的语义亲和。除了增加线性系统的稀疏性之外，超像素的使用还减少了过渡区域中不可靠特征向量的负面影响，如图4所示。



![img](https://mmbiz.qpic.cn/mmbiz_png/UicQ7HgWiaUb1LAic6ibFeAv3R5AZamf2TTGhLibDX2Y9ODCibk2ibibNYotNicgQDg3f86fFVU3GM3MMCl9tcUYv7VRic4w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

图4. 不同处理流程效果比较。（a）仅使用Laplacian matting（b）结合使用Laplacian matting和语义分割 （c）进一步利用稀疏颜色连接方法。



由于特征向量不能表示人与背景之间的语义切割，因此仅使用Laplacian matting会导致包括背景的大部分的人物分割片段突出显示。加入稀疏颜色连接可提供更清晰的前景遮景。



**创建图层**



我们使用前面描述的语义亲和来创建图层，得到Laplacian matrix L。我们要从该矩阵中提取特征向量，并使用两步稀疏化过程，利用这些特征向量创建图层。



![img](https://mmbiz.qpic.cn/mmbiz_png/UicQ7HgWiaUb1LAic6ibFeAv3R5AZamf2TTGyy3y3QLVpOhjicrqibHYwC7bfDDYkkHWC7Ia88SHRB5WGZVUia27wibvGQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

图6.图像（a）显示了像素稀疏化之前（b）和之后（c）的结果。



如图所示，因为我们的结果（c）保留了头发周围的柔和过渡，而常数参数（d）则会导致过度稀疏的结果。



**语义特征向量**



![img](https://mmbiz.qpic.cn/mmbiz_png/UicQ7HgWiaUb1LAic6ibFeAv3R5AZamf2TTGian1WUIBkTI0ibZosklC8e0BxsEcribG9SW3WPudAKqtRPnyqulsIvveQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

图8.我们首先为给定图像生成每像素128维特征向量（图a）。图b表示128维到3维的随机投影。我们利用每个图像的主成分分析（c）将特征的维数减少到3。在降维之前，使用引导过滤器对特征进行边缘对齐。



更多技术细节，请阅读论文：

![img](https://mmbiz.qpic.cn/mmbiz_png/UicQ7HgWiaUb1LAic6ibFeAv3R5AZamf2TTGehkdBd6N9hPBGcTeG2icTClR0lfXsDicH9zvUvicsHc3Z3wSwehSic7YrQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



该论文由麻省理工学院副教授Wojciech Matusik、CSAIL博士后研究员Tae-Hyun Oh、Adobe Research的Sylvain Paris、以及苏黎世联邦理工学院和微软的Marc Pollefeys共同撰写。



**论文地址：**

**http://cfg.mit.edu/sites/cfg.mit.edu/files/sss_3.pdf**


# 相关

- [该卸载PhotoShop了！MIT用AI实现3分钟自动抠图，精细到头发丝](https://mp.weixin.qq.com/s?__biz=MzI3MTA0MTk1MA==&mid=2652025033&idx=2&sn=e6f8865636dd2c51a871eb2368d14c18&chksm=f121c438c6564d2e5862b6ce29f147c809569910d00962fa6850e7fd85d28effd6124f10c7fd&mpshare=1&scene=1&srcid=0822kmP7rAb7VWXg8RfLXtGH#rd)
