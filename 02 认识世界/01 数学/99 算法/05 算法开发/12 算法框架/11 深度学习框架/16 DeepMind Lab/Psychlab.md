---
title: Psychlab
toc: true
date: 2019-09-29
---
Deep[Mi](http://www.hqpcb.com/quote/)nd今天的官博发文，介绍他们的新工作 Psychlab，这是一个建立在 DeepMind Lab之上的平台，旨在构建可控环境，从心理认识的角度，更好地研究和理解[AI](http://www.elecfans.com/tags/ai/)。具体说，Psychlab有助于让研究人员了解，AI在完成一项复杂任务时，其中涉及的每一种特定行动分别起到了什么作用。

想象一下购物这个简单的任务。如果你忘记去拿名单上的某样物品，这说明了你大脑功能的什么？这可能表示，在搜索列表中的项目时，你无法将注意力从一个对象转移到另一个对象。这也可能表明记住购物清单很难，或者两者皆有。



[![DeepMind介绍新工作 Psychlab 旨在从心理认识的角度，更好地研究和理解 AI](http://file.elecfans.com/web1/M00/45/7C/o4YBAFpuxtSAY38bAADHS89qkYo312.png)](http://file.elecfans.com/web1/M00/45/7C/o4YBAFpuxtSAY38bAADHS89qkYo312.png)

看上去就是单一的一个任务，实际上取决于多种认知能力。我们在人工智能研究中也面临类似的问题，在这种情况下，任务的复杂性往往会使智能体取得成功所需的单个技能难以分离。但是，了解智能体特定的认知技能，可能有助于改善其整体表现。

在人类身上，为了解决这个问题，心理学家花了近 150 年的时间来设计严格控制的实验，目的是分离出每个特定的认知能力。例如，他们可能会使用两个单独的[测试](http://www.hqpcb.com/zhuoluye9)来分析超市场景——一个是“视觉搜索”测试，需要被测者在一个图案中定位某个特定的形状，这可以用来检测注意力。同时，心理学家可能会要求被测者背诵一份清单，从而测试他们的记忆力。

我们相信，有可能使用类似的实验方法来更好地理解 AI 的行为。这就是为什么我们开发了 Psychlab，Psychlab这个平台建立在 DeepMind Lab之上，使我们能够直接运用认知心理学等领域的方法，研究受控环境下智能体的行为。今天，我们也将这个平台开源，供其他人使用。

Psychlab在虚拟的 DeepMind Lab环境中，重建了通常用于人类心理学实验的典型设置。例如，让参与者坐在[计算机](http://www.hqchip.com/app/873)[显示器](http://www.hqchip.com/app/964)前，使用鼠标来响应屏幕上的任务。同样，我们的环境允许虚拟 AI 在虚拟计算机监视器上执行任务，使用它的注视方向进行响应。这样，人类和 AI 都采取相同的测试方法，最大限度地减少了实验差异。这也使结果更容易与认知心理学的现有文献联系起来，并从中获得见解。

随着 Psychlab 的开源版本的发布，我们构建了一系列在虚拟计算机监视器上运行的经典实验任务，并且具有灵活且易于学习的 API，方便其他人能够构建自己的任务。

视觉搜索（Visual search）- 测试搜索项目数组的能力。

持续识别（Con[ti](http://www.elecfans.com/tags/%E5%BE%B7%E5%B7%9E%E4%BB%AA%E5%99%A8/)nuous recognition）- 为不断增长的物品列表测试内存。

任意视觉运动测试（Arbitrary visuomotor map[pi](http://www.elecfans.com/tags/pi/)ng）- 测试对刺激-响应配对的记忆。

变化检测（Change detection）- 测试检测延迟后重新出现的对象数组中有所更改的能力。

视敏度和对比敏感度（Visual acuity and contrast sensitivity）- 测试识别小和低对比度刺激的能力。

玻璃图案检测（Glass pat[te](http://www.elecfans.com/tags/te/)rn detection）- 测试全局形式感知。

随机点运动判别（Random dot motion discrimination）- 测试相干运动的能力。

多对象跟踪（Multiple object tracking）- 测试随着时间的推移跟踪移动对象的能力。

所有这些任务都已被验证，表明人类结果反映了认知心理学文献中的标准结果。

以“视觉搜索”任务为例。在复杂的刺激阵列中定位对象，比如在超市货架上选择一个商品，作为理解人类选择性注意力的方法，已经得到深入的研究。

当要求人类“在水平线段中找出竖直线段”和“在其他颜色的线段中找出粉条的线段”的任务时，人类的反应时间不会根据屏幕上的线段数量的改变而改变。换句话说，他们的反应时间与“数据大小”是相互独立的。然而，当任务改为在不同形状和不同颜色的线段中找出粉色线段时，每增加一个线段，人的反应时间会增加大约 50ms。当人类在 Psychlab 上完成这个任务时，我们也复现了这个结果。

[![DeepMind介绍新工作 Psychlab 旨在从心理认识的角度，更好地研究和理解 AI](http://file.elecfans.com/web1/M00/45/7C/o4YBAFpuxtWAPPozAAFTIrhL2kY064.png)](http://file.elecfans.com/web1/M00/45/7C/o4YBAFpuxtWAPPozAAFTIrhL2kY064.png)

这张图片说明了在 Psychlab 的视觉搜索任务上人类和人工因素之间反应时间的差异

当我们对一个最先进的 AI 进行同样的测试时，我们发现它虽然可以执行任务，但并没有显示出与人类相似的反应时间模式。在上述三种情况下，AI都用了相同的时间来应对。在人类的情况下，这些数据表明了并行关注和串联关注的区别。而 AI 似乎只有并行的机制。识别出人类与我们目前的 AI 之间的这种差异，能够为我们改善未来 AI 设计提供途径。

我们设计 Psychlab 是作为认知心理学、神经科学和 AI 之间的桥接工具。通过开源，我们希望更广泛的研究团队能够在自己的研究中利用它，并帮助我们进一步发展。


# 相关

- [DeepMind介绍新工作 Psychlab 旨在从心理认识的角度，更好地研究和理解 AI](http://www.elecfans.com/d/625712.html)
