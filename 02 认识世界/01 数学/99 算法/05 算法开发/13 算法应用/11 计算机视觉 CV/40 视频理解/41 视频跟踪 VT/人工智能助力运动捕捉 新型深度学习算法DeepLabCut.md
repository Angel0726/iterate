---
title: 人工智能助力运动捕捉 新型深度学习算法DeepLabCut
toc: true
date: 2019-11-17
---
根据今天《自然-神经科学》在线发表的一项研究**DeepLabCut: markerless pose estimation of user-defined body parts with deep learning**，运用一种**新型深度学习算法追踪动物运动及行为**，其准确度可达到人工水平，而且无需采用追踪标记物或进行费时的手动分析。

![img](https://mmbiz.qpic.cn/mmbiz_jpg/0OWoGbRW1icibpclTUqd4yic6n8rww9rticMhRG8NbSqTCNe9JNKs2X2hI9sw4uyCu3icIzdPrcpHnSMsj8g0feCeAQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

动物行为期间和跨多个物种的无标记姿态预计对于神经科学中的许多应用都是至关重要的。 上图描绘了几个常见动物的运动及其轨迹。

 Ella Maru Studio

准确追踪行为发生期间的身体运动部位是运动科学的一项重要内容。但是，如果采用视频记录方式来追踪运动，研究人员要么需要费时费力地标记每一帧，要么需要在研究对象身体的预定点上放置标记物。标记物可能会干扰研究目标的行为，而且一般只适合有限类型的运动。

![img](https://mmbiz.qpic.cn/mmbiz_jpg/0OWoGbRW1icibpclTUqd4yic6n8rww9rticMHx4aKN8dyIibPwbQhcDdKxEO1CKWkHtc3g51ouylo3HM0VhU5pR1gJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

DeepLabCut自动追踪的小鼠的手，轨迹显示了将来（左）和过去（最右边）的运动。

Mathis *et al*, 2018

美国哈佛大学的Mackenzie Mathis、Matthias Bethge及同事利用机器学习开发了一款开源运动追踪工具——**DeepLabCut**，它不受以上限制。作者先采用一个大型目标识别图像数据库对DeepLabCut进行了预训练。之后，**DeepLabCut只需要接受小规模的人类标记图像（约200张）训练，即可完成一项新的追踪任务，从而方便神经科学家研究动物行为。**



作者演示了这种算法如何可以在无需标记物的情况下，追踪小鼠和苍蝇在各种行为期间的任意身体部位运动，而且准确度可达到人工水平。DeepLabCut可以追踪精细的动作，如果蝇产卵、伸吻，以及小鼠的伸爪时每一个指的轨迹。

![img](https://mmbiz.qpic.cn/mmbiz_gif/0OWoGbRW1icibpclTUqd4yic6n8rww9rticMSib2oUZpyOQ5hudGpImuG9QYN2FvB8qSbJ601g2wryY5mJEzk94E30A/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

DeepLabCut自动追踪的果蝇运动。

Mathis *et al*, 2018

在相应的新闻与观点文章中，北京大学的魏坤琳与美国宾夕法尼亚大学的Konrad Kording表示，DeepLabCut在理论上适用于任何视频，为运动科学打开了巨大的数据来源。他们预计未来“运动捕捉将从实验室内的一项艰难而又耗资不菲的任务变成一项每个人在日常生活中就能完成的小事情”。

![img](https://mmbiz.qpic.cn/mmbiz_png/0OWoGbRW1icibpclTUqd4yic6n8rww9rticM3gZSj8aj19fFdhicZDT2Sib2TNSxs2nYQKlK51ve81gibpuruPPEehjyw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

DeepLabCut追踪的小鼠、果蝇甚至婴儿的身体运动结果。

Wei & Kording


# 相关

- [人工智能助力运动捕捉：新型深度学习算法DeepLabCut](https://mp.weixin.qq.com/s?__biz=MzAwNTAyMDY0MQ==&mid=2652554921&idx=2&sn=aaa584ae872b09e742af6474c68f4b5f&chksm=80cd6827b7bae1315d4353b5fa9b9c6bd997926219349054410f9ee6bc73e25aa633fe688480&mpshare=1&scene=1&srcid=0821txnF8g5DrzZ9KVJrthaC#rd)
