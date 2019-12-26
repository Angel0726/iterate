---
title: DeepMind解决医疗AI黑箱问题 诊疗50多种眼疾
toc: true
date: 2019-11-17
---
![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtC0ywIu8o05DzL6G1Io75Kt01nicozILichZibuHavTr0p3Kibl91AHiaZZuyG5JRPicSqNmcKvgpjib4pVw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)﻿

人工智能诊断疾病并不是什么稀罕事，但是，在今天之前，还没有人知道AI在做出诊断时，内心到底是怎么“想”的，AI对人类是个黑箱。

不过，AI黑箱的历史被DeepMind终结了。DeepMind在Nature Medicine上发表了一项研究成果，不仅可以识别眼疾，还能有理有据的讲出系统是如何进行判断的。

在这项研究中，DeepMind的系统可以识别50种左右的眼疾，并且表明它依照**光学相干断层扫描（OCT）**进行诊断，这对验证人工智能技术的安全性和有效性十分重要，有望加以开发，用于多种疾病的诊断，以及给出推荐的治疗方案。未来，不管是癌症、神经疾病还是视力问题，这项成果都能让病人们得到更好的治疗。

这项研究的数据主要来自伦敦Moorfields眼科医院历来的患者扫描图像，该系统在超过94％的病例中提出了正确的转诊建议，在相同的扫描图像上，成绩和顶级眼科专家一样，甚至表现更优 ，它得出结果的速度也让专家印象深刻。但更大的突破是，该系统解决了人工智能黑箱问题。

﻿![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtC0ywIu8o05DzL6G1Io75KtDmocYSMwTzcx3eB2v8ISETQLo89GzvZlgfR72VoY0CX729hp4j4djQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)﻿

# 更简更强

和以往的方法相比，DeepMind的新AI**诊断流程更简化**了。

目前，传统的眼科医生通过光学想干断层扫描（OCT）帮助诊断眼睛状况。这些3D图像提供了眼后的详细“地图”，但对人类来说有些难以查看，还需要人类专家的分析来解释。

这需要耗费大量时间。分析这些扫描所需的时间，再加上诊疗过程需要拍不止一次片子，可能导致拍片和治疗之间的长时间拖延。如果患者出现突发问题，例如眼后出血，长时间的拖延可能使患者失明。

DeepMind开发的系统可以在几秒钟内自动检测眼部疾病的特征，而且像人类一样也会给出诊断建议，比如对于急症患者，它会告知病人建议转诊治疗。

这种即时的诊断可以大大缩减患者拍片子和治疗之间的时间，避免患有糖尿病眼病和年龄相关性黄斑变性的患者失明。

另外，DeepMind研发的这个系统的卓越性在于，它不仅仅能看某一种疾病，而是能区分除50中不同的眼科疾病。它可以分析3D的扫描图，从而“看到”比2D图更多的信息，给出更靠谱的治疗方案。

﻿![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtC0ywIu8o05DzL6G1Io75KtvdpicO9os3R8rN9fAtsnUOZVWic5UzkB6njLPCs9icsLUqFKocick7uwAg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)﻿

# 适应性更强

诊断眼疾只是一方面，DeepMind还希望它用于治疗过程。因此，在论文中，DeepMind也承担起人工智能在临床实践中的一大阻碍：即**解决黑箱问题**。

对于大多数人工智能系统而言，很难准确理解它们自己提出建议的原因。对于需要了解系统推理过程的临床医生和患者而言，这是一个巨大的挑战。医生需要知道为什么AI会做出这样的诊断，也需要了解它是如何得出的。

针对这个问题，DeepMind采用了一种新颖的方法解决。

﻿![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtC0ywIu8o05DzL6G1Io75KteSxDvXolvz9lR1iasEbaoTIibagQic6cDayGBUHrqejP10IrXz9uya2aw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)﻿

DeepMind的系统通过在两个不同的神经网络间插入**可解释性表征**而将它们结合起来。第一个神经网络是分割网络，用来分析 OCT 扫描，提供不同类型的眼组织图，找到疾病特征，比如眼部出血、病变或其它疾病症状。这种图能帮助眼科专家深入了解系统诊断疾病的过程。

第二个神经网络为分类网络，可以用来分析特征图，为人类医生提供诊断意见和转诊推荐。这个神经网络将所有的推荐意见以百分比的形式表示，所以人类医生可以分析判断：这个神经网络心中到底有没有谱。

这个功能非常重要，因为人类医生才是那个做出诊疗决定的人，这样可以让人类医生们了解患者疾病的整体情况，再考虑要不要采纳系统给出的建议。

还有一点好处是，该技术可以适配不同的眼部扫描仪，不仅仅在Moorfields眼科医院，在全世界都可以推广这一系统，即使是扫描仪更新换代后也依然可以使用。

# 论文摘要

诊断影像的体量和复杂性比人类可来解释它的专业知识增长得更快。AI已经展现出在一些常见疾病的二维图像分类方面潜力巨大，并且这些疾病通常依靠数以百万计的标注图像数据库。至今为止，用现实世界的临床方法进行三维扫描诊断还无法与人类专家相提并论。

在这篇文章中，研究人员将一种新的深度学习架构应用于临床上异质三维光学相干断层扫描。这些来自大型眼科医院的患者在14884次扫描后，在特定数据范围内，AI已经达到甚至超过了专家的诊断表现。

此外，该架构的组织分割可作为一种独立于设备的表征。当使用来自不同设备类型的组织分割时还能保持诊断建议的准确性。

这项研究消除了以前大范围临床使用的阻碍，并且也无需现实世界中多种疾病的庞大的训练数据。

﻿![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtC0ywIu8o05DzL6G1Io75KtXFEBIDFuAScbCddJhFUS9WfX8qdicPvgjJou1UTKNsaZUYW6pqpwBHg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)﻿

# 未来

虽然这个AI目前看起来表现不错，但暂时还不会在医院里使用，人类专家现在还得一如既往得诊断眼疾。参与这项研究的Pearse Keane博士表示：“我们都认为这项技术可能具有变革性，但我们也承认这不是魔术，必须一视同仁对它也采取严格干预。”

下一步，DeepMind要将它转移成实际产品了。

如果这项技术经过临床试验的一般使用验证，Moorfields的临床医生将能够在其所有30家英国医院和社区诊所在五年内免费使用该技术。这些诊所每年诊断300000名患者，每天进行1000多个OCT扫描诊断。这样，每个诊所都可以提高准确性和诊断速度了。

# 传送门

DeepMind相关博客：

https://deepmind.com/blog/moorfields-major-milestone/

论文Clinically applicable deep learning for diagnosis and referral in retinal disease地址：

https://www.nature.com/articles/s41591-018-0107-6

# 相关

- [DeepMind解决医疗AI黑箱问题，诊疗50多种眼疾堪比专家丨论文](https://mp.weixin.qq.com/s?__biz=MzIzNjc1NzUzMw==&mid=2247502503&idx=3&sn=9bfe22609909a151b34b21b4182eff4b&chksm=e8d07dd5dfa7f4c39e3b8f8e448f292b058947a4d19c9e095161a6ff39c15aa4f404297b1e75&mpshare=1&scene=1&srcid=0814KhpmoWA3xMqsuyv4vHqR#rd)
