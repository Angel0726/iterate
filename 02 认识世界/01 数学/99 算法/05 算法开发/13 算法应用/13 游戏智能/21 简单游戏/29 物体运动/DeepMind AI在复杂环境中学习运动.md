---
title: DeepMind AI在复杂环境中学习运动
toc: true
date: 2019-10-29
---
# DeepMind AI 在复杂环境中学习运动


DeepMind 的论文 Emergence of Locomotion Behaviours in Rich Environments 探索了丰富的环境如何有助于促进复杂行为的学习。

## 复杂环境中的虚拟人物动作学习


强化学习范式原则上允许通过简单的奖励信号直接学习复杂的行为。然而，在实际操作中，通常要手动设计一些奖励函数以促成实现某些特定的解决方案，或者从演示数据中将其推导出来。DeepMind 的论文 Emergence of Locomotion Behaviours in Rich Environments 探索丰富的环境如何有助于促进复杂行为的学习。

这篇4分钟的视频论文解析很好地讲解了论文的大意，特推荐给读者。


具体来说，我们在不同环境环境下训练智能体，发现这样可以增强智能体的稳健行为，使其在一系列任务中表现良好。我们在此展示这种运动原则，这些行为以奖励敏感性高闻名。我们使用基于前向传播的简单奖励函数，在多种具挑战性的地形和障碍物上训练几个虚拟人物。使用策略梯度强化学习的一种新的可扩展变体，我们的虚拟人物学习在没有明确的基于奖励的指导下如何根据所处环境跑动、跳跃，蹲伏和转弯。视频中展现了这种学习行为的部分亮点。



下面是论文中的更多图示：



<center>

![mark](http://images.iterate.site/blog/image/20191024/fg0cj27QtFx4.png?imageslim)

</center>



Planar Walker（左）、Humanoid（中）和MemoryReacher（右）任务中的DPPO基准性能。



<center>

![mark](http://images.iterate.site/blog/image/20191024/j4Yw7Of0BBna.png?imageslim)

</center>

网络架构示意图



<center>

![mark](http://images.iterate.site/blog/image/20191024/cPjmecSnUzJ9.png?imageslim)

</center>

实验中使用的地形类型的示例。



<center>

![mark](http://images.iterate.site/blog/image/20191024/xjMm7cEKa2h8.png?imageslim)

</center>



Walker Skill：一个典型的 Planar Walker 策略走过不平整地面、跃过一个障碍、跳过一条沟和蹲下穿过一个平台下方的延时图像。



<center>

![mark](http://images.iterate.site/blog/image/20191024/oRfKXziO2wO5.png?imageslim)
</center>



这是一个典型的 Quadruped策略越过间隙（左）和在障碍物中穿行的延时图像。



<center>

![mark](http://images.iterate.site/blog/image/20191024/QxzAnNAt9ujm.png?imageslim)

</center>



a）课程培训：对具有不同统计数据的障碍课程培训策略的评估：“regular”课程包含任意交错的高低障碍（蓝色）;“curriculum”课程则随着课程进度逐渐增加障碍的高度（绿色）。在训练期间，我们对低/“容易”障碍（左）和高/“挑战”障碍（右）的验证课程进行评估。在“curriculum”课程上训练的政策进步更快。b）Planar  Walker 策略（左）和Quadruped策略（右）的稳健性：我们评估相比较于在平地的训练（蓝），跨越障碍的的训练（绿）如何增强了策略的稳健性。我们对基于地面摩擦力、地形表面、虚拟人物的身体机能强度和地面倾斜等难以观察的变化对策略的表现进行评估。在某些情况下，在跨越障碍地形中训练的策略存在显著优势。

<center>

![mark](http://images.iterate.site/blog/image/20191024/Pn0t8c36mb5p.png?imageslim)

</center>

这是 Humanoid 根据周围地形运动的一系列延时图像。



# 相关

- [4分钟视频讲解DeepMind论文：AI在复杂环境中学习运动](https://mp.weixin.qq.com/s?__biz=MzI3MTA0MTk1MA==&mid=2652005173&idx=5&sn=18e9610b572e4ad9b24125c8c79c2aec&chksm=f12117c4c6569ed28749478b5b8bef118dd1b0f19e159cb8bb93290f33603e52542542ec4ff2&mpshare=1&scene=1&srcid=09238sKA2YKvLbytUdYmolly#rd)
- [coding-the-history-of-deep-learning](http://blog.floydhub.com/coding-the-history-of-deep-learning/)
