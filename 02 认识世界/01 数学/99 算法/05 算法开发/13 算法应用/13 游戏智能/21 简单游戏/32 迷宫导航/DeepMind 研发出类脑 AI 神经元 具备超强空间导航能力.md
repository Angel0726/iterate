---
title: DeepMind 研发出类脑 AI 神经元 具备超强空间导航能力
toc: true
date: 2019-11-17
---
# DeepMind 研发出类脑 AI 神经元 具备超强空间导航能力


DeepMind 研发出了能够模拟哺乳动物大脑中网格细胞（Grid Cells）工作模式的 AI 神经元。



在模拟环境中，这些 AI 神经元在人为设置的迷宫中显示出超强的导航能力，甚至还能绕过障碍“抄小路”。



![img](https://mmbiz.qpic.cn/mmbiz_png/BnSNEaficFAa8sSj43TgJ4o4c0YbjmmCR7yibiarlZYhWR89ibBk7zMJXHAuJZHfjyQ2NMysRd4BO8vrucvssJ32QA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

﻿

这是继 DeepMind 研发出超过人类围棋水平的 AlphaGo 和 AlphaGo Zero 之后，在空间定位与认知导航领域取得接近哺乳动物的水平。



自从进化论被提出以来，包括人类在内的哺乳动物所具备的空间定位和绕过障碍物能力，一直优于其它动物。但脑神经科学家对这背后的具体工作原理并没有彻底搞清楚。



早在上世纪六十年代，伦敦大学学院（UCL）的认知神经学教授 John O’Keefe 就开始研究这个课题，并于 1971 年在人脑海马回中发现了位置细胞（Place Cells）。这是一种锥体神经元，当动物进入到某个环境的特定位置时，位置细胞会释放电化学信号，导致相邻区域变得活跃。一般认为，是这种细胞活动导致了动物对空间位置的记忆。但位置细胞也并非全能，它不可以记录坐标，也不具备几何计算能力，因此无法完整解释哺乳动物的空间定位能力。



后来，又有科学家发现了能够感应动物头部前进方向的方向细胞。



到了 2005 年，挪威大学的 May-Britt Moser 和 Edvard Moser 夫妇共同在大鼠内嗅皮层发现了网格细胞（Grid Cells）。这种细胞在大鼠进行空间活动时，可以将整个空间环境划分成六边形的蜂窝状网格网络，就好像地图中通用的经纬度一样。相当于大鼠对空间建立了坐标系，从而用于定位。



这个重要发现让两位夫妇和 John O’Keefe 教授一起获得了 2014 年的诺贝尔生理学或医学奖。



但是，网格细胞的具体功能和工作模式仍然需要验证。有科学家猜测，网格细胞可能参与了大脑中的矢量计算，从而帮助规划路径。



在 DeepMind 团队的帮助下，研究人员先利用循环神经网络（RNN）在虚拟空间模拟大鼠在附近觅食时的移动路径、速度和方向，以此生成数据来训练算法。但其中并不包括网格细胞相关的数据。



接着，科学家使用生成的数据训练更加复杂的深度学习模型，来识别和感知虚拟大鼠所在的位置。神奇的现象发生了，这个利用人工智能模拟的“网格单元”与实验用大鼠的“网格细胞”出现了高度相似的反应模式，虚拟大鼠也跟实验室里的真实大鼠的循迹能力相近。



![img](https://mmbiz.qpic.cn/mmbiz_png/BnSNEaficFAa8sSj43TgJ4o4c0YbjmmCRIUPnx6Ixry6MFr1TCLcBaWmkKwKqDM8ib4uHMalJB5Iwo7gb2X0VftA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

﻿

这意味着，网格细胞在真实大鼠的空间和路径规划活动中确实起作用，而且呈现出可被验证的规律性。研究人员可以利用 AI 模拟系统，增加学习所需要的记忆和奖励机制。经过重复训练，模拟大鼠的寻路技能变得越来越熟练。



另外，在 AI 中还可以彻底关闭“网格单元”，让模拟大鼠仅依靠位置和方向细胞来定位和寻路，结果证明模拟大鼠无法完成走出迷宫的任务。需要指出的是，彻底关闭“网格细胞”在真实大鼠身上是无法做到的。

﻿﻿

![img](https://mmbiz.qpic.cn/mmbiz_png/BnSNEaficFAa8sSj43TgJ4o4c0YbjmmCRyofrFLq67iciceypMkgGBicOXj3YHDPgV31JYjOQg1F2SSnNTlaDQN0qg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



DeepMind 的研究人员、同时也是本论文的共同作者 Andrea Banino 解释说，尽管 DeepMind 通过与神经科学家合作，实现了新的人工智能突破，但整个 AI 算法还停留在很基础的研究阶段，并不能真正导入应用进而产生研究结果。



从宏观角度讲，深度学习算法模型的不可解释性从根本上限制了这次 AI 突破的影响力。它所模拟的各种细胞仍然缺乏一个合乎逻辑并且可被重复验证的系统解释。



但无论如何，AI 在神经和脑科学研究领域的运用还大有潜力可挖掘。



> 参考资料：
>
> Nature:https://www.nature.com/articles/s41586-018-0102-6.epdf
>
> http://www.wired.co.uk/article/deepmind-newest-network-mimics-the-gps-cells-in-your-brain
>
> http://www.scholarpedia.org/article/Grid_cells
>
> https://www.ft.com/content/3d0f3f92-5377-11e8-b24e-cad6aa67e23e


# 相关

- [DeepMind 研发出类脑 AI 神经元，具备超强空间导航能力](https://mp.weixin.qq.com/s?__biz=MzI0ODcxODk5OA==&mid=2247494932&idx=3&sn=f9bf9b6ffc90c3e435c6d80791c5191c&chksm=e99edeeddee957fbb6239d7a40e4d649db266c5b78ce810225d57082a39ac565e2bc560935a8&mpshare=1&scene=1&srcid=0510Mdug4n8PtxxFPpWvP2my#rd)
