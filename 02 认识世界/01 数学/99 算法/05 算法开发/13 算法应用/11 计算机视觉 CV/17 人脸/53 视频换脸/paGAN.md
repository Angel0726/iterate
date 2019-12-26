---
title: paGAN
toc: true
date: 2019-11-17
---
# paGAN


- 论文作者：黎颢
- 论文出处：SIGGRAPH



每秒1000帧扫描，用单幅照片实时生成超逼真动画人物头像。

<center>

![mark](http://images.iterate.site/blog/image/20191104/8bjzQwisWnfc.png?imageslim)

</center>



每秒1000帧：根据普通照片实时生成高清逼真动画人脸


下面就是 fxguide 的Mike Seymour，左边是苹果iPhone手机拍摄的短视频，右边则是实时渲染的CGI，在原视频人脸上盖了一层数码生成的3D数码人脸（hockey mask）。这个过程中只涉及边缘修饰的少量微调，其他全部自动生成。



![img](https://mmbiz.qpic.cn/mmbiz_gif/UicQ7HgWiaUb2dibicBY85udxXJGWPWzDRtlicV5xAvHnAicjMpyu6Go4qsv4mbM17EQu3H39Gnk6YibdYlhlJfMqBOFg/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)



Pinscreen的团队正在使用单幅jpeg图像构建他们的3D脸部模型，而且采用端到端的方法。



首先，模型识别照片中的人脸，然后生成一个合理的3D网格。



不仅如此，模型能以1000 fps的速度对人脸进行跟踪。手机摄像头的速度一般只有30或60 fp，但黎颢解释说：“这让我们有足够的时间在同一帧中追踪多个面孔。”



这个追踪器名为VGPT，代表“Veli Goodo Pace Tracka”，由Pinscreen的Shunsuke Saito领导开发。在搭载英伟达1080P GPU的PC上，VGPT以1000 fps的速度运行。在iPhone X上，它的运行速度接近60~90 fps。



“我们的解决方案的另一个巨大优势是它占用的内存非常少，没有I/O的核心只有5M，而且完全基于深度学习。”黎颢表示。



**该解决方案基于直接推理**，不像传统的面部跟踪器那样，后者是直接跟踪特征或标记。较旧的跟踪器会使用基于AAM模型的面部标记检测器，速度慢很多。而这个新的解决方案，根据黎颢的说法，提供了“相对于相机的3D精确头部模型，以及微表情测量工具和所有重要的东西”。



VGPT使用一组ML工具进行非常快速的无标记跟踪。不仅跟踪效果好，鲁棒性也高。如果一个人在摄像头前移动，部分遮挡了相机，程序将很快重新获得面部信息并继续工作。



VGPT将是Pinscreen下周在SIGGRAPH实时现场演示中最强大的新工具。





Pinscreen拍摄了《洛杉矶时报》记者David Pierson的一张照片作为输入（左），并制作了他的3D头像（右）。 这个生成的3D人脸通过黎颢的动作（中）生成表情。这个视频是6个月前制作的，Pinscreen团队称其内部早就超越了上述结果。



paGAN：逼真动画人物生成对抗网络



那么，再来看关键的**“paGAN”，这个缩写代表“Photoreal Avatar Generative Adversarial Network”，逼真动画人物生成对抗网络**，这就是Pinscreen系统的“渲染器”。



到目前为止，对动画头像或数字人物进行传统建模、纹理、灯光和渲染的方式都需要构建非常高质量的数据集。这通常需要很多高质量的扫描图像。多个图像开始，以构建摄影测量样式解决方案，具有非常多高质量、符合摄影测量法的人脸扫描图像。



为了解决这个问题，黎颢和Pinscreen团队跳过了传统的管道方法，他们认为“用ML采用”不等于“用CGI模拟”。



Pinscreen团队的目标是将采样的面部重新点亮，生成动画，旋转，然后放置在模拟的3D环境中，就像3D CGI头像一样。但是，整个过程没有使用正常的建模/纹理/照明和渲染管道。



![img](https://mmbiz.qpic.cn/mmbiz_png/UicQ7HgWiaUb2dibicBY85udxXJGWPWzDRtlDqgS8E7icxJA5U1xDyCCEvic9aLyK6vXhJkBzmPicLbMFNpYhWNVJNzYg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



结果看起来很真实，就像照片投影在与之匹配的几何体之上，但它仅在静态时才起作用。



Pinscreen想要看看他们是否可以使用最先进的深度生成模型来实现一个通用的解决方案。“这是一种非常特殊的深度学习网络，它包含了生成对抗网络。它们具有生成逼真的2D图像的能力。我们知道GAN可以生成逼真的2D图像，许多其他研究人员已经证明了这一点，”黎颢解释道。



“在Ian Goodfellow的开创性工作和NVIDIA的大量精彩工作的基础上，已经证明可以训练神经网络来合成高质量的面部图像。”黎颢和他的团队想知道他们是否可以将这项新技术转变为一种面部渲染引擎（facial render engine），从而跳过建模，纹理和光照的pineline。它不只是从正确的角度来“渲染”脸部，而是使用GAN ML。



paGAN是一个ML GAN网络，它基于简单模型的输入（具有少量纹理）来呈现照片级真实的面部，这个简单模型来自他们的VGPT。



paGAN擅长处理眼睛和嘴巴。



当用于面部处理时，GAN的问题在于输出是2D的，并且“vanilla GAN”非常难以控制。“用GAN会得到任意的斑点，这些斑点很难控制。我们用paGAN能够确保输出看起来是照片级真实的，特别是口腔和眼睛区域，”黎颢说。早期的研究也做了类似的工作，但没有包括眼睛或嘴巴。



“嘴巴，以及舌头在嘴巴里的移动方式，这是paGAN做得非常好。”



彻底解析神奇技术：重新定向



由于人脸可以由单个的Jpeg制成，并且所有表情都来自新的表情源，所以这种技术非常适合以一种可信的、合理的方式将别人的脸制成动画。



![img](https://mmbiz.qpic.cn/mmbiz_jpg/UicQ7HgWiaUb2dibicBY85udxXJGWPWzDRtl687CKB9G9fZ7geQgToHJ9HFWBowmUC4DAAdgVuFTSiavadibhLdfaOrg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



在查看结果时，要注意被操作的脸（右边）是由一张jpeg图像（顶部）仅由一个静止的jpeg图像（顶部）制成，而没有其他FACS输入或特殊扫描。所有的表情都是从expression source转移到目标人物。



**混合方案和光照问题**



由于黎颢的背景和在ILM、Weta Digital等公司的经历，他知道自己的面部工具需要在有V-Ray、Manuka或RenderMan的pipeline上工作。“目前我们的解决方案是一种混合方案，效果非常好。我们将在SIGGRAPH的Real Time Live上演示的解决方案就是这样的。”



黎颢补充说：“照片真实级别的人脸是很好的技术演示，但是在Pinscreen，我们想让人们使用它……如果你有3D的脸或头像，你需要有一个环境，否则就没有意义了。”



出于这个原因，paGAN面不仅能够从任何角度“渲染”，而且还能够任何光照场景中“渲染”。 “在环境中，意味着可以从任意方向和该环境的任何照明条件下渲染”。



Pinscreen目前通过解决面部的照片级反照率来解决这个问题（不是100％的反照率，但很接近）。“使用这种反照率纹理，再加上其他使用传统计算机图形的pipeline，可以获得令人信服的结果。”黎颢说。



在用户测试中部署时，CGI人脸的得分接近完美。在相似的背景下以相同方式和真实面部一起呈现时，CGI人脸几乎能够完美地欺骗用户。



**手机级别**



下图是以单张Jpeg作为输入，到最终在iPhone上呈现角色输出的过程。下面是Mike Seymour的源图像。 虽然Pinscreen团队可以使用深度相机，但这款iphone实时制作出来的Mike最终效果是使用单个Jpeg图像而不是深度相机传感器数据制作的。 图像是在iPhone X上拍摄的，但使用的是非深度感应相机。



![img](https://mmbiz.qpic.cn/mmbiz_jpg/UicQ7HgWiaUb2dibicBY85udxXJGWPWzDRtl4n9CfjyNo8lNOavZibTibaPjicmRvCIr3oFftyffoicVbkw77zIMSTzR9Q/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![img](https://mmbiz.qpic.cn/mmbiz_jpg/UicQ7HgWiaUb2dibicBY85udxXJGWPWzDRtleU2CC3icOrgCWFceia0y86fx8cantkvykWhjIPDqwc1ysub4WQSXgpaw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



“在Pinscreen公司，我们有两个团队，一个团队专注于制作人们喜欢玩的东西，同时我们有一个非常强大的研究小组。这个小组关注的是基本问题。”



Pinscreen想让3D avatar大众化，但是人们为什么需要它呢?



黎颢说：“首先，大多数游戏都是3D游戏，而且大部分游戏中都涉及到人类的形象或造型，但我认为它可以走得更远。”



他看到的应用是3D通信（Skype的3D版本），“在某种程度上，我觉得你就在我们办公室。这是人们真正合作，共同完全解决问题，交流思想和情感的唯一途径。这是建立信任的关键。”



他说，他期待有一天我们真的觉得在使用3D头像的时候会有人在房间里，但“要做到这一点，你不能依靠游戏或电影研究工作室来捕捉你的面部数据，它必须是足够聪明的，能够基于有限的知识来构建所有这些复杂性。”这就是为什么Pinscreen对于先进的ML和专业的GAN如此看重的原因。



![img](https://mmbiz.qpic.cn/mmbiz_jpg/UicQ7HgWiaUb2dibicBY85udxXJGWPWzDRtlWSziaEaOrMic4rr2snQsSegRaTKoUA8hmjBEQZfpaaUpuJVoldS5Tt8g/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

输入Charlie的单张图片，并在iPhone上生成3D人脸的过程



Pinscreen的策略是先构建“游戏”级的移动平台，“但这是我们能够用来部署我们正在开发的所有新研究技术的平台”。



![img](https://mmbiz.qpic.cn/mmbiz_jpg/UicQ7HgWiaUb2dibicBY85udxXJGWPWzDRtlWuVt5x5sg8JUibgrl6QdYzjt4fxzf2UNIujLoZx6yYWeflqxkUSHrMw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

团队训练一个GAN，可以在不同的视点中产生表情，给出一个中性的jpeg脸部图像。 在右侧，来自训练网络的提取纹理用于在手机上实时驱动动态avatar



我们使用大量的人脸图像数据集来训练网络，该数据集可以捕获各种目标和表情。由于移动端的硬件限制，paGAN无法以令人满意的帧速率在当前的iPhone硬件上运行。网络被训练好之后，可用于生成一小组固定关键表情的纹理，然后可以将这一固定或稀疏的集合扩展为基于面部动作编码系统（FACS）的一组混合形状UV纹理图。计算完成后，就可以用这些纹理来创建具有多种表情的头像，所有这些都由跟踪器在手机上以30帧的速率实时驱动的。它可以在线实时合成每帧纹理。这种移动“压缩”是Pinscreen解决方案的重要组成部分，也将在SIGGRAPH上展示。



paGAN的效果非常好，不仅可以用于制作面部表情，还可以用于制作嘴巴和眼睛。该程序为生成头像制作了300张嘴巴的纹理和20个预先计算出的眼睛纹理。然后利用paGAN的眼睛纹理来近似模拟所有观察方向。利用移动设备（例如iPhoneX）上的视线跟踪器，程序可以选择最接近真实的视线，并以此选择合适的眼睛，组合到面部。



**头发**



最后一部分是头像的头发。上面的示例框架使用Pinscreen的数据驱动毛发解决方案。这个方案是黎颢及其团队之前发布的。现在，该团队正在研究一种新型头发模拟器，但由于这种新方法刚刚提交发表，因此不会出现在今年的实时现场演示中。新的系统属于另一种端到端神经学习解决方案，将始终根据训练数据生成合理的头发模型。



视频演示体会一下：







2014年SIGGRAPH Asia访谈：特立独行的杀马特教授



正如前文所说，实际上，在2014年SIGGRAPH Asia上，新智元创始人兼CEO杨静就对黎颢[进行了采访](https://mp.weixin.qq.com/s?__biz=MzA4MjE5NjAzMg==&mid=201590576&idx=1&sn=e2550e9b1aa344608d76f5ee7ea204cd&scene=21#wechat_redirect)。



视效艺术家通常通过粘在人脸或身体上的3D感应球进行表情捕捉，黎颢的技术突破在于使用了深度传感器（微软的Xbox体感游戏使用了同样的技术）简化了这一过程，当装有深度传感器的摄像机对准演员的脸时，黎颢的软件会自动分析其面部表情的变化，并立刻将这些表情套用到动画人物上。



![img](http://mmbiz.qpic.cn/mmbiz/VPia6sR85GCNl2TUw2yfku72NUeVOVXibyAvx0icdVxnut6RlYIJbbALa1m9ht0oIysta4I3b4k0UD1tEF0Dr60yg/640?tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![img](http://mmbiz.qpic.cn/mmbiz/VPia6sR85GCNl2TUw2yfku72NUeVOVXibyZTQKD1VTZ09TGyfhGyrWXKSibrtR0pbNq4ph4yoophVMjwEicJe19hmA/640?tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![img](http://mmbiz.qpic.cn/mmbiz/VPia6sR85GCNl2TUw2yfku72NUeVOVXibyUfA9E66ZHkCZxlxCrQDT6T04Gp5Fuibd51W6sACkhLxukmO24Ehy14g/640?tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

黎教授于2013年发表的SIGGRAPH论文所提出的技术，在皮克斯和工业光魔进行过一些实验测试，用于前期的pre-visualization。技术结合利用了深度相机（Kinect）和视频摄像头捕捉到的信息，也就是同时使用了深度和颜色信息。



在光影工业，黎颢主要是针对几部《星球大战》（Star Wars）脸部捕捉技术的研发。主要工作是提供脸部和身体捕捉和重建技术的效率，希望能够在拍摄的同时能尽可能看到最后合成的效果，也就是尽可能做到实时。



![img](https://mmbiz.qpic.cn/mmbiz_png/UicQ7HgWiaUb2dibicBY85udxXJGWPWzDRtlhfKdbia3xalRT5TCu1c1gzTh4iaqaVhLaGaMo9WRib3leSstqTNziaSmaw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



编译来源：

https://www.fxguide.com/featured/a-i-at-siggraph-part-2-pinscreen-at-real-time-live/


# 相关

- [被控造假、打人之后要一雪前耻！“杀马特”华裔教授推出paGAN，GoodFellow也点赞](https://mp.weixin.qq.com/s?__biz=MzI3MTA0MTk1MA==&mid=2652024112&idx=1&sn=822444b21ea055e0439ffbbcd2c30c9a&chksm=f121d9c1c65650d730d1d98d3354906bcaefa63b313dddec95b5fb2e0577a3dca5761910a163&mpshare=1&scene=1&srcid=0807oIAZq4h99QBMot0VgGqS#rd)
