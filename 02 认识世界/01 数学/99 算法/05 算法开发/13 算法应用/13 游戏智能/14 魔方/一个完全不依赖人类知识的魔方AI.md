---
title: 一个完全不依赖人类知识的魔方AI
toc: true
date: 2019-11-17
---
# 一个完全不依赖人类知识的魔方AI

![img](https://mmbiz.qpic.cn/mmbiz_gif/YicUhk5aAGtAoGzWTdBLm5DRIfKS9E2ibQRZqOHGrHX7N68ndASc1LjmXxKnpv2qHZdRxSarFBq419TNl6UlcUSw/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)


加州大学欧文分校发布了一个基于**强化学习**的魔方复原AI。

这只AI **完全不需要依靠人类的知识**来解魔方，有速度有准度。

# 魔方的正确打开方式

如何让AI自己学会破解魔方？

第一步是建立AI对魔方的基本认知。

魔方有26个小方格，可以按照它们身上的贴纸数量来分类——

中心，一张贴纸。

边边，两张贴纸。

角角，三张贴纸。

这样一来，54张贴纸，每张都有自己**独一无二**的身份，即身属哪类方格，同一方格上的其他颜色有哪些。

用**独热编码** (one-hot encoding) 便可以轻松表示每张贴纸的位置。

不过，由于每一张贴纸的位置不是独立的，而是和其他贴纸相关。这样，把表示方式降个维，每个方格可以只看一张贴纸。系统视角就是图中右边的样子——

![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtAoGzWTdBLm5DRIfKS9E2ibQlF8mOV0aicMwzvbB3rXxDnB5h1pdOicNUaibanAnWEiaNQGOVCZwBEgLOQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

###### **△** 右侧双色是降维后的视角

然后，按朝向来标注魔方的6个面，前(**F**)，后(**B**)，左(**L**)，右(**R**)，上(**U**)，下(**D**) 。

正对要操作的那一面，顺时针转 (90度) 直接用这几个字母就行了，逆时针在字母后面加个撇。比如，R和R’就是把右面顺时针转90度，以及逆时针转90度。

这样算的话，6个面，操作一共有12种。

每一个时间步(t)，都有一个状态(st) ，都会执行一个动作 (ta ) 。然后，就有了一个新状态(s t+1 ) ，得到一个奖励数值(Rs t+1 ) ，成功是1，没成功是-1。

三阶魔方的状态有**4.3e^19**种，而其中只有一种状态能够收到奖励信号，那就是复原成功的状态。

![img](https://mmbiz.qpic.cn/mmbiz_jpg/YicUhk5aAGtAoGzWTdBLm5DRIfKS9E2ibQ7ga0yUwRSsT18mIJ40slIbxABbZVzQZP32taECBG0ZzmRfx9GbLWbQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

正因如此，同样是走在强化学习的大路上，魔方和围棋之类的游戏，存在**明显的不同**。

# 到不了的终点？

在这样险峻的情况下，如果使用A3C算法，理论上有可能永远到不了终点。

面对稀有的奖励，团队受到策略迭代 (policy iteration) 的启发，提出了一种名为“自学迭代 (Autodidatic) ”的深度强化学习算法，简称ADI。

![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtAoGzWTdBLm5DRIfKS9E2ibQVOxw83mUm0PkmZHEOBK0blQF3kjwrPtptw3lLRPb9TlKTDeWqRYcoQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

在这里，策略评估和策略优化两个步骤会交替进行，最终找到最优策略。而把策略迭代和价值迭代 (value iteration) 结合在一起，便可以把评估和优化合而为一。

这还不是全部，把ADI和蒙特卡洛树搜索 (MCTS) 搭配食用，便称为“DeepCube (深度魔方) ”。到目前为止，复原魔方成功率高达100%。

#

## 自学迭代 (ADI)

ADI的训练，用的是一个迭代监督学习过程。

深度神经网络fθ，要先学习一个策略 (policy) ，了解在已知的状态下，应该采取怎样的旋转动作。

深度神经网络fθ(s)，参数θ，输入的是状态s，输出的是一个价值和策略的组合 (v,p) 。这个输出可以为MCTS铺路。

生成训练样本，要从复原完成的状态 (ssolved) 开始。

从初始状态打乱魔方，转动k次，得到k个魔方的序列。把上述打乱活动重复l次，生成k*l个训练样本。

![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtAoGzWTdBLm5DRIfKS9E2ibQC8ZDcL3rPiblia1oRl34CA0KmNuFR2PIwADJN9M2BpibCqt8pOMWswW1Q/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

###### **△** 后代生成中

给每一个训练样本，生成它的12个后代的状态，然后用当前的价值网络，来估计每个后代的价值。

然后，这些后代里面，价值评估的最大值，就是这个样本的**价值训练目标**。

而最大值对应的动作，就是这枚样本的**策略训练目标**。

## 复原大法

这里，蒙特卡洛树搜索 (MCTS) 才要出场。

团队用了一个**异步MCTS**，并用之前训练好的fθ网络帮它增强了一下——**策略输出***p*可以降低它的广度，**价值输出**v可以降低它的深度。

要为每一个已知状态s0，种起一棵搜索树。

树苗是T={s0}，迭代就从这个单一的集合开始。

在树苗身上执行模拟遍历 (simulated traversals) ，直至到达一个叶节点 (leaf node) 为止。

每一个状态(s’)，都有它的专属记忆——

**Ns(a)**，是从s开始，执行某个动作a的次数。

**Ws(a)**，是动作a从s那里获得的最大价值。

**Ls(a)** ，是动作a在s处的virtual loss (虚拟损失) 。

**Ps(a)** ，是动作a在s处的先验概率。

![img](https://mmbiz.qpic.cn/mmbiz_jpg/YicUhk5aAGtAoGzWTdBLm5DRIfKS9E2ibQ2SkVLHS5hWoIXG3yro5iafPvqknhmiaq4sH54KyRRVKOB95y2a3iasyuA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

每一次模拟，都是从根节点开始，跟随着树的策略，不断跌带着选择各种各样的动作。

每一个时间步，都会选择一个动作。

而**Virtual loss**可以避免搜索树多次关照同一个状态，也可以阻碍多个异步worker走上同样的道路。

到达一个**叶节点**之后，状态就会加上后代 (s’) 。这样，树上有了新的元素，后代也会生成自己的**记忆**。

生生不息。

# 全能小王子

枝繁叶茂之后，测试一下效果：DeepCube大战另外两个魔方高手算法。

一个是Kociemba在1992、1992年提出的两段式算法，依赖人类提供的领域知识，用群论来解魔方。这种方法的特点是运行速度非常非常快，也的确能解开任何状态下的魔方。但它所找到的解法，通常不是最优解，比其他方法要多花几步。

另一个是Korf在1997年提出的迭代式深入A*(IDA\)*算法。这种方法借助模式库进行启发式树搜索，无论在什么样的初始状态下，都能找到最优解，但寻找答案的过程要花费很长时间。

这些方法展开了两场竞赛。

﻿![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtAoGzWTdBLm5DRIfKS9E2ibQ2e2lric3LQTt42Dlv5ggia2dNfujKib4Jue315AX2glSzjkcR9wKCIbJw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)﻿第一场，DeepCube和Kociemba的方法用640个随机打乱的魔方进行了比拼，这些魔方都被胡乱拧了1000次。

两种方法都在1小时之内解开了全部魔方，Kociemba方法的速度比较快，每个魔方用时不到1秒，而DeepCube平均每个魔方用了10分钟。

Kociemba方法找到的解法都在31-33步的样子，DeepCube的解法分布稍微广一点，大概从28到35都有，不过作者们说，在55%的情况下都能匹敌Kociemba方法。

第一场，比速度。

DeepCube和Kociemba都成功复原了640个 (1000次打乱) 魔方。

DeepCube单个魔方用时的中位数是10分钟，Kociemba是不到1秒钟。但，在55%的魔方大战中，DeepCube或与后者速度相当，或好于后者。

其实自学成才的DeepCube和人类智慧结晶的Kociemba，基本上还算旗鼓相当。

至于Korf？这位选手玩一个魔方需要6天。

![img](https://mmbiz.qpic.cn/mmbiz_gif/YicUhk5aAGtAoGzWTdBLm5DRIfKS9E2ibQIBUpNdO2lNZQ3qpZhBKic5dKbI2gSURwX0aIe93ROynps00ZIiajADvA/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

###### **△** 人类速拧比赛现场

第二场，比最优解。

100个魔方，每个经过15次打乱。

这次Korf比较厉害，中位数是**13**步，只有一个魔方超过15步。

不过，DeepCube也不差，在74%的魔方上，都和Korf找到了一样的最优解。当然DeepCube超过15步的次数，比Korf略多一点。

至于kociemba？成绩太差，惨不忍睹。

顺便，再和人类对比一下，三阶魔方最少步数的世界比赛中，人族的最好成绩是22步。

如此看来，DeepCube堪称魔方全能小王子。

# 殊途同归

我们一直强调说，这个魔方AI，不依赖任何人类经验。

但是，从最后的结果看，DeepCube也和人类选手类似，学到了一些“套路”，包括用复杂的排列组合来解魔方，以及与人类速拧选手相近的策略。

比如，DeepCube大量使用一组特定的操作，即aba-1。就是先执行某个转动a，再执行另外一个转动b，最后把a步骤转回去。

![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtAoGzWTdBLm5DRIfKS9E2ibQiaBzflNX2H7iaV2m70ejZrwicfmMET4UVkRbWRo3VicNyKsEZK4mPicwEYA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

团队检查了DeepCube处理640个完全打乱的魔方时，发现AI经常使用这样的操作，这样能在移动某些方格的过程中，让其他方格不要受到影响。具体来说，就是查看每三次相邻的转动，出现频次最高的14种，都是aba-1格式。比其他格式的出现频率明显要高。

至于现在嘛，团队可能觉得，自家的AI复原三阶魔方已经百发百中了，于是就开始研究四阶魔方，以及各种奇奇怪怪的魔方。

![img](https://mmbiz.qpic.cn/mmbiz_jpg/YicUhk5aAGtAoGzWTdBLm5DRIfKS9E2ibQn8LRQbAuAJnbnBiaib8Lh0161OvIXEBIJdJTwHAEUHIVgMjwemDQyicQQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

另外，走出魔方的世界，他们觉得这种方法也可以用来处理其他组合优化问题，比如预测**蛋白质**的三级结构。

许多组合优化问题，都可以想成序列决策问题，也就可以用强化学习来解决。

团队可能觉得，自家的AI复原三阶魔方已经百发百中了，于是就开始研究四阶魔方，以及各种奇奇怪怪的魔方。

另外，走出魔方的世界，他们觉得这种方法也可以用来处理其他组合优化问题，比如预测蛋白质的三级结构。

# 论文

![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtAoGzWTdBLm5DRIfKS9E2ibQZdhLzmGDUbeT0mbqtyMjxHKbh0j6eo0eURxowho4Y0apaZllfm3D0g/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

这篇论文已经提交到NIPS，题目是：Solving the Rubik’s Cube Without Human Knowledge

传送门在此：

https://arxiv.org/pdf/1805.07470v1.pdf


# 相关

- [魔方全能小王子降临：一个完全不依赖人类知识的AI](https://mp.weixin.qq.com/s?__biz=MzIzNjc1NzUzMw==&mid=2247498978&idx=1&sn=d9e7708ec16bf16f62e77781e56fba44&chksm=e8d04b90dfa7c286b399568c0bfa580cae9531cb3c5a101525c04144eda91f51027adc3201dc&mpshare=1&scene=1&srcid=0525aQbp87nZzrZ5RbBLuvFG#rd)
