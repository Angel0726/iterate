---
title: 特朗普模仿奥巴马 进阶版换脸技术DeepFakes来了
toc: true
date: 2019-11-17
---

DeepFakes，这种能够移花接木的技术，它能将图像或视频中把一张脸替换成另一张脸。



去年 12 月，一个名 Reddit 用户用 DeepFakes 技术将盖尔·加朵和斯嘉丽·约翰逊的脸嫁接到一个成人电影女星的身上，制作了一个几乎可以以假乱真的视频。



![img](https://mmbiz.qpic.cn/mmbiz_jpg/BnSNEaficFAYtJdWSJQYwiaY5gyOLej6jy0KHJ0CFXYucibthDGk8k5f2Sbpax17w9gVXaf9XXqn5N7iadvicveHhXg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



就其发挥的作用本质而言，这跟 CG 技术没多大区别，不过这项技术“换脸”速度极快，成本较低，甚至在今年年初，一款与之相关的名为 FakeApp 的应用被发布，使得虚假内容的创作变得更加容易。



不过，就在人们还在寻找检测 DeepFakes 方法以防滥用之际，强力进阶版的 Recycle-GAN 来了。



近日，CMU 的研究人员发表了一篇介绍 Recycle-GAN 的论文，他们介绍说这个研究是受到了 Cycle-GAN 的启发。至于 Recycle-GAN 这项技术特点，他们将其描述为“无人监督，数据驱动”，它能将一个视频或者图片的内容转换到另一个视频或图片里。这样的内容转换任务能够支持很多应用，包括从一个人的人体运动和面部表情转换到另一个人上。



先来看川普是如何“模仿”奥巴马神态的：



![img](https://mmbiz.qpic.cn/mmbiz_gif/BnSNEaficFAYtJdWSJQYwiaY5gyOLej6jy34iadO4xdiatVZps0RvzOP9hJsmXygQQX4VeVnphDEx5ToibpN8LlQ0ug/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)



根据论文，Recycle-GAN 利用生成式对抗网络（GANs）和“时空线索（spatiotemporal cues）”来学习和寻找图片或视频之间的关联，当训练人类主体的镜头时，它能够捕捉面部表情一些细微的线条，比如面部抽搐、微笑时的面部细节。



他们还使用了 OpenPose 提取了关键的脸部关键点，这是在没有任何手动输入调整的情况下，捕捉到了这些公众人物的特点。



而目前大多数类似 Deepfakes 的“换脸”技术缺乏的正是对细节等全局性的掌握，这些技术都只针对人脸，缺乏对其他领域的总结和整合，如果在实际中遇到遮脸等情况，机器就难以进行相应操作，还有一些必须依赖对照组或者成对图像，这需要大量的手动标记数据。



上述图片主要关注脸部特征，使用相同的方法，还可以同步源人物的身体摆动动态：

﻿![img](https://mmbiz.qpic.cn/mmbiz_gif/BnSNEaficFAYtJdWSJQYwiaY5gyOLej6jy0WySnicdW8iaaTrczIkciaTbWJadSKE6libNTjym81YSib4ckNLAgLIOYGw/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)﻿

当然，Recycle-GAN 也可以使得人与其他物体之间的表情可以进行转换：



![img](https://mmbiz.qpic.cn/mmbiz_gif/BnSNEaficFAYtJdWSJQYwiaY5gyOLej6jy171xegRPOfyN7tEn2V8YCcRQ3316CJaVSNMszlqWF7Xbjicu5q1Nib2A/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)﻿



根据论文内容，他们的方法综合了空间和时间信息以及内容翻译和风格保存的对抗性损失方法。他们研究使用了时空约束优于空间约束的有效重新定位的优势，这些都从花与花、风和云合成、日出和日落转化中有所体现。



![img](https://mmbiz.qpic.cn/mmbiz_gif/BnSNEaficFAYtJdWSJQYwiaY5gyOLej6jyeOMRd6N0OBjqfpmtXGUVF5CXI6g3vNiayqF9AgHXNvShqGw1ia7kP2kQ/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)﻿

云与风的合成，研究人员使用它来修改视频中的天气状况，将无风的日子转换为刮风的日子。此外，他们通过两种情况的视频重定向来展示自动视频操作方法：合成视频中的云和风；在不同的视频中制作日出和日落。



﻿![img](https://mmbiz.qpic.cn/mmbiz_jpg/BnSNEaficFAYtJdWSJQYwiaY5gyOLej6jydXQeAmhOWjGJ6NIF6x6tJeibZrbmIZ4BMcBPMbAT7VFibKSia2zuaiat9Q/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)﻿



﻿![img](https://mmbiz.qpic.cn/mmbiz_jpg/BnSNEaficFAYtJdWSJQYwiaY5gyOLej6jyz7t2giakvFxyUuUbLHyPcJ2trQ3tkFYLTibu9bOqgzgkCr8ABoza6nQw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)﻿



就当前的实验结果而言，可以在 28.3％ 的时间内让 15 名测试对象分不清真假，但团队认为，如果他们学会了“生成输出视频”的速度，就像人们说话有轻重缓急一样，系统的未来版本就会更加准确。



真正的视频风格应该更加自然，语音和内容应该随时间的变化而变化，研究团队相信，以后会有更好的时空神经网络架构能解决这个问题。



以 Deepfakes 这类为代表的技术如果能投入到制作电影等应用场景中，自然很不错。但它们的低门槛也可能导致恶意的操纵者制作不良视频，比如制作假的复仇色情片、假新闻，此前 Reddit、Pornhub 和 Twitter 以及其他平台，都对此技术滥用行为表示反对，而美国国防部的研究人员也在积极寻找检测 Deepfakes 以及揭露由 AI 创作的假事件的方法。



让人无奈的是，如果要防止 AI 的负面效应，我们策略只能是以 AI 抗衡 AI，但这又陷入到了无限循环当中，而随着技术的不断迭代，这也意味着像用 Recycle-GAN 技术做出的视频，人类将很难识别出真假。



﻿圣塔克拉拉大学法学院教授 Eric Goldman 说，我们最好为一个真假难辨的世界早做准备，但事实上，我们已经身处在这样一个世界中了。



论文链接：https://arxiv.org/abs/1808.05174



参考来源：https://venturebeat.com/2018/08/16/carnegie-mellon-researchers-create-the-most-convincing-deepfakes-yet/


# 相关

- [特朗普“模仿”奥巴马？进阶版换脸技术DeepFakes来了](https://mp.weixin.qq.com/s?__biz=MzI0ODcxODk5OA==&mid=2247497037&idx=1&sn=29524d65144a4f811d0e42d71ae077e6&chksm=e99ec6b4dee94fa2f1be09da9b4071fec0313ab55249180734e88eb110237ded31ad6ab3a8cb&mpshare=1&scene=1&srcid=08206gXXRsKBYYhzwbQqUeUQ#rd)
