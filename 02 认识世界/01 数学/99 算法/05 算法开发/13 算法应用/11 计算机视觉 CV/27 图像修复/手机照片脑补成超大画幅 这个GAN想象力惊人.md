---
title: 手机照片脑补成超大画幅 这个GAN想象力惊人
toc: true
date: 2019-11-17
---
# 手机照片脑补成超大画幅 这个GAN想象力惊人


- 方法作者：斯坦福 Mark Sabini和Gili Rusak


把图像补到了取景框外边。


就像这样：

﻿![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtDIIUtfgBzDPpmUNw2FVuIicjsVia1fKTLic0vwpr2Jz5MVQp5n14qHAlxuHic0kKicSO94ib0ibKNDnaPLQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)﻿

原本像手机拍摄的照片一样小的一幅画，一下子变成了开阔的大图。

机器根据它天才的“脑补力”，将白天和傍晚的竖幅海景图脑补出左右两侧的样子还原出了方形海景图照片。看上去除了左右两侧有种照片被水泡了的模糊感之外，就是完整的一张照片。

# Keras实现

最近，印度班加罗尔一位小哥Bendangnuksung（简称Bendang）看中了这种算法，决定把它发扬光大。于是，他根据论文中的训练方法，打造了一个超低门槛的Keras实现，还把可处理的分辨率从128×128提升到了256×256。

一经推出，在Reddit上引起轰动。

大家纷纷表示过于厉害了：

> 你该不会是用训练集做的测试吧？
>
> 牛逼，喜欢这种很实用的东西。
>
> 效果太好了，简直不像是真的。
>
> 除了能看出原图和生成内容的边界之外，其他简直完美。

甚至还开脑洞想出了应用场景：

> 4:3画幅的电影可以无暇延伸成21:9的了！还可以把旧电影放大成4k画面！
>
> 如果我把我的半个脸给它，能给我恢复过来么？

但是也有不少网友指出了一个小问题：这个模型的训练和测试过程很不规范，Bendang展示的效果图，是训练数据中就包含的。

Bendang解释说这个Keras实现，是用海滩数据集训练的，整个数据集一共就350张图，（你们就理解一下嘛）。

然后，他也给出了一张真正的测试效果：

﻿![img](https://mmbiz.qpic.cn/mmbiz_jpg/YicUhk5aAGtDIIUtfgBzDPpmUNw2FVuIicjibVNnh72aIIDnS2YoW28lEBD3DFsmAibeR4wxzWx7nTBc9YypRJkx3A/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)﻿

效果勉勉强强。

有了Keras实现，这么cool的想法确实好上手很多，不过在训练和测试这件事上，大家不要学印度小哥。

# 训练过程

在论文中，这个模型的用到的训练集相当大，有超过3万张图片。不过每张图片只是128×128的小图。
﻿
﻿![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtDIIUtfgBzDPpmUNw2FVuIic2MLibrrIMDhANW68lOyfRlcluNOiaNeTsib6Px9fGY6ENtqhkkpn0GMNw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)﻿

首先，按照这个要求准备数据库，找到36500张128×128的照片，保留100张做测试集。

﻿![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtDIIUtfgBzDPpmUNw2FVuIicA1q5IE92RmHGfOCI4Iic6APICk0uxetFcmMDwg16wdhq1zMDaKos6JA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)﻿

处理好的数据，通过这样一个DCGAN构架训练。

后面测试集的结果如下，第一排是输入的窄图，第二排是输出效果，第三排则是这张图的原图。

﻿![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtDIIUtfgBzDPpmUNw2FVuIicmclg0Rbu6ibUTs8hHehACSLwVPVtN8CKHkMbvRNdy78twlJ2ukM4e6w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)﻿

比较一下第二排和第三排看出，结果还不错，除了部分图片有一些明显的边缘之外，还是可以看出图像的连续性的。另外，还有五倍宽度版：

﻿![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtDIIUtfgBzDPpmUNw2FVuIic2rLetQaJrYxeR7vVMUubxqT65lz8EIt729GS9A7ZTn9aDcnpxHE9ibw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)﻿

# 彩蛋

这篇论文获得了CS230作业中的**Outstanding Posters**。在CS230的作业中，还有很多十分有趣的研究，比如说，**Final Project Prize Winners**第一名的作业，照着卫星图画地图。

﻿![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtDIIUtfgBzDPpmUNw2FVuIicxnUxiaoPR2FbBSycJHBfc9st1Td4XTtaaN3vd3ibbryQyIRnS27TGribQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)﻿

而且，量子位悄悄LinkedIn了一下几位拿到了第一名作者，貌似都是华人/华裔学霸（亮点自寻）。

﻿![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtDIIUtfgBzDPpmUNw2FVuIicFgSZviamthEra8ZibhgbqfS3NLJCpMTbYibSJFHBYNrf6icfdbZ1wtDM2w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)﻿

# 传送门

Keras实现：
https://github.com/bendangnuksung/Image-OutPainting

相关论文：

Painting Outside the Box: Image Outpainting with GANs
Mark Sabini and Gili Rusak

海报：
http://marksabini.com/files/cs230__Painting_Outside_the_Box_Image_Outpainting_with_GANs__poster.pdf

论文：
http://marksabini.com/files/cs230__Painting_Outside_the_Box_Image_Outpainting_with_GANs__report.pdf

原作者的代码：
https://github.com/ShinyCode/image-outpainting


# 相关

- [手机照片脑补成超大画幅，这个GAN想象力惊人 | Keras实现](https://mp.weixin.qq.com/s?__biz=MzIzNjc1NzUzMw==&mid=2247501995&idx=4&sn=c1f8d0f4678084644df0797c9d7955e3&chksm=e8d07fd9dfa7f6cf88f111fbbeaabd652bf50a80a77936bd77a93ca5b44858a51b14569649f0&mpshare=1&scene=1&srcid=0801wKshmzI941MjFyWZdBV8#rd)
