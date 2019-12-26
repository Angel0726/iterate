---
title: DeepMind 开源三大新框架
toc: true
date: 2019-09-29
---
# DeepMind 开源三大新框架

深度强化学习（DRL）一直是人工智能取得最大突破的核心。，DeepMind 在推动 DRL 研发方面做了大量工作，包括构建了许多专有工具和框架，以大规模地简化 DRL agent 训练、实验和管理。最近，DeepMind 又默默开源了三种 DRL 框架：OpenSpiel、SpriteWorld 和 bsuite，用于简化 DRL 应用。

## OpenSpiel

GitHub：https://github.com/deepmind/open_spiel

游戏在 DRL agent的 训练中发挥着重要作用。与其他数据集一样，游戏本质上基于试验和奖励机制，可用于训练 DRL agent。但是，正如我们所想，游戏环境的复杂度还远远不够。

OpenSpiel 是一系列环境和算法，用于研究一般强化学习和游戏中的搜索/规划。OpenSpiel 的目的是通过与一般游戏类似的方式促进跨多种不同游戏类型的一般多智能体强化学习，但是重点是强调学习而不是竞争形式。当前版本的 OpenSpiel 包含 20 多种游戏的不同类型（完美信息、同步移动、不完美信息、网格世界游戏、博弈游戏和某些普通形式/矩阵游戏）实现。

核心的 OpenSpiel 实现基于 C ++ 和 Python 绑定，这有助于在不同的深度学习框架中采用。该框架包含一系列游戏，允许 DRL agent 学会合作和竞争行为。同时，OpenSpiel 还包括搜索、优化和单一 agent 等多种 DRL 算法组合。

## SpriteWorld

GitHub：https://github.com/deepmind/spriteworld

几个月前，DeepMind 发表了一篇研究论文，介绍了一种好奇的基于对象的 seaRch Agent（COBRA），它使用强化学习来识别给定环境中的对象。COBRA agent 使用一系列二维游戏进行训练，其中数字可以自由移动。用于训练 COBRA 的环境，正是 DeepMind 最近开源的 SpriteWorld。

Spriteworld 是一个基于 python 的强化学习环境，由一个可以自由移动的形状简单的二维竞技场组成。更具体地说，SpriteWorld 是一个二维方形竞技场，周围可随机放置数量可变的彩色精灵，但不会发生碰撞。SpriteWorld 环境基于一系列关键特征：

多目标的竞技场反映了现实世界的组合性，杂乱的物体场景可以共享特征，还可以独立移动。此外，它还可以测试与任务无关的特征/对象的稳健性和组合泛化。连续点击推动动作空间的结构反映了世界空间和运动的结构。它还允许 agent 在任何方向上移动任何可见对象。不以任何特殊方式提供对象的概念（例如，没有动作空间的特定于对象的组件），agent 也完全可以发现。SpriteWorld 针对三个主要任务训练每个 DRL agent：

目标寻找。agent 必须将一组目标对象（可通过某些功能识别，例如“绿色”）带到屏幕上的隐藏位置，忽略干扰对象（例如非绿色的对象）排序。agent 必须根据对象的颜色将每个对象带到目标位置。聚类。agent 必须根据颜色将对象排列在群集中。安装


## bsuite

GitHub：https://github.com/deepmind/bsuite

![img](http://pics4.baidu.com/feed/a8ec8a13632762d0bbc2a92393fc7dff503dc647.jpeg?token=7c16f6bafdec26a6b92745be3baf3c60&s=FD28347239C156470D755CD6030050A0)

强化学习行为套件（bsuite，The Behaviour Suite for Reinforcement Learning ）的目标是成为强化学习领域的 MNIST。具体来说，bsuite 是一系列用来突出 agent 可扩展性关键点的实验。这些实验易于测试和迭代，对基本问题，例如“探索”或“记忆”进行试验。具体来说，bsuite 有两个主要目标：

收集清晰、信息量大且可扩展的问题，以捕获高效和通用学习算法设计中的关键问题。通过在这些共享基准上的表现来研究 agent 行为。bsuite 当前的实现可以在不同环境中自动执行实验，并收集可以简化 DRL agent 训练的相应指标。

如果你是一个 bsuite 新手，可以开始使用 colab 教程。这款 Jupyter 笔记本电脑配有免费的云服务器，因此无需任何安装即可立即开始编码。在此之后，你可以按照以下说明在本地计算机上运行 bsuite。




# 原文即相关

- [DeepMind悄咪咪开源三大新框架，深度强化学习落地希望再现](http://baijiahao.baidu.com/s?id=1644950787657147958&wfr=spider&for=pc)
- https://towardsdatascience.com/deepmind-quietly-open-sourced-three-new-impressive-reinforcement-learning-frameworks-f99443910b16
-
