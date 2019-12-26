---
title: Open.ai 一小时内训练AI系统后空翻 仅需900Bit的人类反馈数据
toc: true
date: 2019-10-29
---
# Open.ai 一小时内训练AI系统后空翻 仅需900Bit的人类反馈数据


Open.ai 与 DeepMind 合作开发的算法。

使用少量人为反馈进行强化学习，并能够处理更复杂的任务。仅需900bit的人类反馈，系统便学会了后空翻，需要人类参与的时间也从70小时将至1小时，该技术还能够被应用在更多其他方面，目前在虚拟机器人以及Atari平台的游戏上已经接受广泛测试。


构建安全AI系统的关键步骤之一是消除系统对人类编写的目标函数的需求。因为如果复杂的目标函数中有一点小错误，或者对复杂目标函数使用简单的代理，都可能会带来不是我们希望的甚至危险的后果。因此，我们与DeepMind的安全团队合作，开发了一种算法，可以通过人类告诉系统哪种行为更好而使系统得知人类的想法。

> 论文地址：https://arxiv.org/abs/1706.03741

我们提出了一种使用少量人为反馈来学习现阶段RL环境的学习算法。 有人类反馈参与的机器学习系统在很久之前就出现了，此次我们扩大了该方法的适用范围，使其能够处理更复杂的任务。仅需900bit的人类反馈，我们的系统就学会了后空翻，这是一个看似简单的任务，成功与否的评估方式简单粗暴，但具有挑战性。



![img](http://mmbiz.qpic.cn/mmbiz_gif/ptp8P184xjxsJtg7nSzCedKI1triaOozEhj5MPQo8pt55sTO6kR4uAoPmtTJgtTqkVtmIicC31uicdWCDMxRPt32A/0?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)



仅需900bit的人类反馈，我们的系统就学会了后空翻

整体的培训过程是一个三节点的反馈循环，其中包括人类、代理对目标的理解、以及RL训练系统。

![img](http://mmbiz.qpic.cn/mmbiz_png/ptp8P184xjxsJtg7nSzCedKI1triaOozEJIYibtHJ5f09HfHiaj5EHYPOebuQOz5TtGZXmR55DLUChLcfsckx5cMw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

我们的AI代理最开始是在环境中随机行动，并定期向人类提供其行为的两个视频截图，人类选择其中最接近完成任务的一张（在这个问题下，是指后空翻任务），反馈给代理。AI系统逐渐地通过寻找最能表达人类目的的反馈函数（reward function）来创建该任务的模型。然后通过RL的方式学习如何实现这一目标。随着它行为的改善，它会继续针对其最不确定的环节征求人们对轨迹的正确反馈，并进一步提高对目标的理解。

我们的方法在效率上表现出色，如前所述，学会后空翻只需要不到1000bit的反馈数据。这就意味着，人类参与其中为机器提供反馈数据的工作时间不到一小时。而这一任务的平均表现为70小时（且模拟测量时假设的速率比实际操作时要快）。我们将继续努力减少人类对反馈数据的供应。您可以从以下视频中看到培训过程的加速版本。

> https://youtu.be/oC7Cw3fu3gU

我们已经在模拟机器人和Atari的许多任务上测试了我们的方法（系统没有访问reward function的权限：所以在Atari，系统没有办法访问游戏得分）。我们的代理可以从人类的反馈中学习，在我们测试的大部分环境中都能够实现强大的，甚至是超人的表现。 在以下动画中，您可以看到通过我们的技术训练的代理正在玩各种Atari游戏。 每个gif图片右侧的竖条表示每个代理预测的人类评估者将对其当前行为的认可程度。这些可视化表明，通过人类的反馈，1图中的代理在学习计量潜水舱中的氧气，2、3图中的代理在预估小球敲掉砖块的数量以及轨迹，或者在4图中学习如何从赛车撞车事故中恢复。

![img](http://mmbiz.qpic.cn/mmbiz_gif/ptp8P184xjxsJtg7nSzCedKI1triaOozEVMx3z9sbrTMemib6LeOa1XZxZicTdt3icTEFLfFh09CWH4ZKHqPQ77eeA/0?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

![img](http://mmbiz.qpic.cn/mmbiz_gif/ptp8P184xjxsJtg7nSzCedKI1triaOozEmW91xR5OeYe6254cnhWUoS7O3dpqCyBmB9aeGjLcehc4SEaY08miatw/0?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

![img](http://mmbiz.qpic.cn/mmbiz_gif/ptp8P184xjxsJtg7nSzCedKI1triaOozEBT4DDFaMjSFBXOJLfovnEyjL2aLzAEpzCLYSt4MR9A8RH76YiciakOwg/0?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

![img](http://mmbiz.qpic.cn/mmbiz_gif/ptp8P184xjxsJtg7nSzCedKI1triaOozECYDzB2icltPp0qiaHpctpRvrDJdUBRiaBEAXOVAHdVjgpNbm74bEoCabQ/0?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

请注意，反馈不需要与环境中的正常的奖励函数保持一致：例如，在赛车比赛中，我们可以训练我们的代理，使其与其他车辆保持持平，而不是为了最大化游戏分数而超过他们。 我们有时会发现，从反馈中学习，比通过正常奖励函数进行强化学习效果更好，因为人类塑造的奖励，比环境中的奖励函数更有效。



![img](http://mmbiz.qpic.cn/mmbiz_gif/ptp8P184xjxsJtg7nSzCedKI1triaOozE7Nrjtwict5hBC7oeMdCPm6m3yR0NJamqicaAQnmbHcYmx2ffxRey5Mhg/0?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)











**挑战与不足**









我们算法的性能基于人类评价者对于什么样的行为看起来正确的直觉，所以如果人类对这个任务没有很好的把握，那么他们可能不会提供有用的反馈。 相应地，在某些领域，我们发现，系统可能会习得愚弄人类评价者的策略。 例如，如下所示，机器人的任务是抓取物体，而非将机械手放置在摄像机和物品之间，假装成正在抓取物品的样子。



![img](http://mmbiz.qpic.cn/mmbiz_gif/ptp8P184xjxsJtg7nSzCedKI1triaOozEwumdFliaA86cY5ZzibPnyk0oXwcibXXxEiaFTTicI7n8jKP7UX6TTHwZXzw/0?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)



对于这个特殊问题，我们通过添加视觉线索（上述动画中的粗白线）来处理，以便人类评估者轻松估计机械手的深度。

这篇文章中描述的研究是与雷尼姆德（LeMindMind）的Jan Leike，Miljan Martic和Shane Legg合作完成的。 我们两个组织计划继续就人工智能安全的主题展开长期合作。 我们认为，像这样的技术是迈向安全人工智能系统的一个环节，能够驱动机器实现像人类一样学习这一目标，并且可以补充和扩展现有的方法，如加强和模仿学习。 本文代表了OpenAI安全团队所做的工作，如果您有兴趣处理这样的问题，请加入我们！

**脚注：**

作为对比，我们花2小时写了一个reward function来训练系统后空翻（如下图），虽然它可以后空翻成功，但却明显不如开篇提到的GIF图中优雅。

![img](http://mmbiz.qpic.cn/mmbiz_gif/ptp8P184xjxsJtg7nSzCedKI1triaOozEgiazF35jEPTRXWib0cjZfic3Lmia7kskeq9DAJIU7IlMqicDaHTPjQ8pdSg/0?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

我们认为，有许多情况下，比起手动编写对象代码，人类的反馈可以更直观，更快速地训练AI系统。

您可以通过下述代码在Open.ai gym中重现这个后空翻系统。


```py
def reward_fn(a, ob):
    backroll = -ob[7]
    height = ob[0]
    vel_act = a[0] * ob[8] + a[1] * ob[9] + a[2] * ob[10]
    backslide = -ob[5]
​    return backroll * (1.0 + .3 * height + .1 * vel_act + .05 * backslide)
```


# 相关

- [Open.ai新算法：一小时内训练AI系统后空翻，仅需900Bit的人类反馈数据](http://blog.yoqi.me/wp/3286.html)
