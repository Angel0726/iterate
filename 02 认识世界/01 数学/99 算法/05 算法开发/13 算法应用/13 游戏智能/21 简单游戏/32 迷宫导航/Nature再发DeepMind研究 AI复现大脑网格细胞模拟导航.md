---
title: Nature再发DeepMind研究 AI复现大脑网格细胞模拟导航
toc: true
date: 2019-11-17
---
# Nature再发DeepMind研究 AI复现大脑网格细胞模拟导航


DeepMind在Nature上发表的一篇论文引起AI领域和神经科学领域的极大震撼：AI展现出与人脑“网格细胞”高度一致的空间导航能力。这项发现有助于AI的可解释性和把神经科学作为新算法的灵感来源的重要意义。



![img](https://mmbiz.qpic.cn/mmbiz_jpg/UicQ7HgWiaUb0Qibo6JIsUXbmFHMf5DtLGToytg9g6ZpibaibGxPs0OWUNQmOm5NfibB68Gx5MClWLxNZCWibXmtibYMhw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



大多数动物，包括人类，都能够灵活地驾驭他们生活的世界——在新的地方探索，迅速返回到记忆中的地方，同时能够“抄近路”。这些能力如此简单和自然，以至于很少人在意其底层流程究竟有多复杂。相比之下，虽然AI在围棋等许多任务超过了人类，**空间导航能力对于人工智能体来说仍然是一个巨大的挑战**。



2005年，一项惊人的研究发现揭示了空间行为中神经回路一个关键部分：**当动物在探索它们所处的环境时，神经元的激发呈现出一种非常规则的六边形网格**。



这些网格被认为有利于空间导航，类似于地图上的网格线。



除了作为动物的内部坐标系之外，这些神经元——被称为**网格细胞（grid cell）**——最近也被假设为**支持基于矢量的导航**。即：让大脑计算出到达目的地的距离和方向，“像乌鸦飞行一样”，即使以前没有走过确切的路线，动物也可以在不同地点之间直接旅行。



首次发现网格细胞的研究团队共同获得2014年的**诺贝尔生理学或医学奖**，他们的研究揭示了空间的认知表征工作的方式。但从网格细胞被发现以来，经过10多年的理论论证，网格细胞的计算功能，以及它们是否支持基于矢量的导航，在很大程度上仍然是一个谜。

![img](https://mmbiz.qpic.cn/mmbiz_gif/UicQ7HgWiaUb3us8wbrZOia9CERIpLLicQqwGOgQbqfghPqfx3AN0QrtZatdvPS9icXhKicOKwIbRZ4AViaWO4CwQ72OA/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)



DeepMind今天发表在Nature上的论文“**Vector-based navigation using grid-like representations in artificial agents**”中，研究人员开发了一种人工智能体（artificial agent）来测试“网格细胞支持基于矢量的导航”这一理论。







研究人员首先训练了一个循环网络来执行**在虚拟环境中定位自身**的任务，主要使用与运动相关的速度信号。哺乳动物处于不熟悉的地方或不容易发现地表的地方（如在黑暗中行走）时，这种能力会自然地激发。



研究人员发现，**类似网格般的表示（grid-like representation，以下称“网格单元”）自发地出现在网络中，**这与在哺乳动物中观察到的神经活动模式惊人的一致，也与网格单元为空间提供高效代码的观点一致。





![img](https://mmbiz.qpic.cn/mmbiz_jpg/UicQ7HgWiaUb3us8wbrZOia9CERIpLLicQqwVncsMWy4ia9aPQIJj2G00zjoic8cTf94p8OibazAdqlxiaMy4xF6cIwuCQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



图：用agent进行的实验产生了类似网格的表示（“网格单元”），它们与哺乳动物中的生物网格细胞非常相似。



接下来，研究人员试图通过创建一个artificial agent来作为实验小白鼠，要测试的理论是：**网格细胞支持基于矢量的导航**。



这是通过将最初的“网格网络”与一个更大的网络架构相结合，形成了一个agent，可以使用深度强化学习在具有挑战性的虚拟现实游戏环境中导航进行训练。



这个agent的表现超过了专业游戏玩家的能力，展现出动物一般的灵活导航方式，**当游戏环境中可以有捷径时，agent会“抄近路”。**





![img](https://mmbiz.qpic.cn/mmbiz_jpg/UicQ7HgWiaUb3us8wbrZOia9CERIpLLicQqwSyhQkpicL0dtUNhrTia3GEPcP4aBDxwEsiayRfh4ebKolM3ObQogMMG4g/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



通过一系列实验操作，研究人员发现网格单元对于基于矢量的导航至关重要。例如，当网络中的网格单元被掐断时，agent的导航能力就会受损，而且对目标的距离和方向的判断等关键指标的表示变得不那么准确。



![img](https://mmbiz.qpic.cn/mmbiz_gif/UicQ7HgWiaUb3us8wbrZOia9CERIpLLicQqwciaYEKHLX4ibq8cP5yeMxCD9pyC2bVdbwOBmcUriaicCcMBBWRBsUw8QxQ/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

图：具有网格单元的基于矢量的导航的图示。底部的圆圈表示3个不同尺度的网格单元类群，有颜色的细胞是活跃的。当agent移动时，网格单元（表示“当前的网格代码”）会发生变化，以反映agent进入了不同的触发区域。网格单元用于计算目标的最短路径。



DeepMind认为，这一研究是理解大脑中网格细胞的基本计算目的的重要一步，同时也突出了它们对人工智能agent的好处。这些证据为“网格细胞提供欧几里德空间框架，支持基于矢量的导航”的理论提供了有利的支持。



此前研究人员对网格细胞进行的广泛的神经科学研究提供了在试图理解其内部表示的线索，有助于agent的可解释性——这本身就是人工智能研究中的一个主要话题。



这项工作还展示了在虚拟现实环境中使用人工agent积极参与复杂行为，以测试大脑工作原理的潜力。



更进一步，类似的方法可以用来测试那些对感知声音或控制肢体有重要意义的大脑区域的理论。未来，这样的网络很可能为科学家们提供一种新的方法来进行“实验”，提出新的理论，甚至对目前在动物身上进行的研究提供补充。



DeepMind的联合创始人兼CEO、该研究的联合作者Demis Hassabis说：“要证明我们现在致力于构建的通用智能是可行的，**人类的大脑是我们现有的唯一证据**，因此，把神经科学作为新算法的灵感来源是有意义的。”



针对DeepMind这项研究，国内外众多专家给与评价，新智元整理如下：



**杜克大学陈怡然教授：**



> 春鹏同学评论道：论文中提到的两个细节值得注意。第一，如果神经网络的损失函数中不包括正则项，那么神经网络无法表现出网格细胞功能。这一发现给了我们一个全新的角度去思考正则项的作用。第二，论文指出深度神经网络的“黑盒”特点阻碍了进一步分析网格细胞活动特性对路径整合的作用。这一点再次印证了当前研究神经网络可解释性的必要。



**中科院自动化所何晖光：**



> 在这项工作中，研究人员首先训练循环神经网络基于运动速度信息在虚拟环境中定位。这与哺乳动物在不熟悉环境中运动定位所用到的信息非常类似。令人震惊的是，类似网格细胞的模式，研究人员称之为网格单元，在神经网络中自然出现，如上图所示！人工智能的定位方案，与大自然亿万年进化所得到的答案，高度一致。



**中科院计算所研究员、中科视拓创始人、董事长兼 CTO 山世光：**



> 基于数据进行学习后得到的人工神经网络中的规律和模式与长期进化而来的生物神经系统有相似之处——出现这样的可能性是偶然还是必然，这确实是很有趣，很值得探索的方向。



**新加坡南洋理工大学黄广斌教授：**



> 这也再次说明AI发展突飞猛进，国内和国外在AI算法上的差距越来越大。国内许多AI公司还处在重复使用开源算法阶段。除了讲故事、描绘理想，专家们也需要带头低调踏实做研究。



# 相关

- [Nature再发DeepMind研究：AI复现大脑网格细胞模拟导航！](https://mp.weixin.qq.com/s?__biz=MzI3MTA0MTk1MA==&mid=2652018443&idx=1&sn=873ec77fa5a50d40931e4c45d08b444c&chksm=f121e3fac6566aecb185836b9cf1e793cc9834205e9c53426e0845707b363399ad00d7765f27&mpshare=1&scene=1&srcid=0510BRErKPPdIWJzwM9Ki58K#rd)
