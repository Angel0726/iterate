---
title: 05 NAS 进展
toc: true
date: 2019-08-31
---

# NAS

NASNet 带动了行业内的一次进步，它为深度学习研究指出了一个全新方向。

但是，用 450 个 GPU 来训练，找到一个优秀的架构也需要训练 3 到 4 天。也就是说，对于除了 Google 之外的普通贫民用户们，这种方法还是门槛太高、效率太低。

NAS 领域最新的研究，就都在想方设法让这个架构搜索的过程更高效。

## SMBO 策略

2017年谷歌提出的**渐进式神经架构搜索（PNAS）**，建议使用名叫“基于序列模型的优化（SMBO）”的策略，来取代 NASNet 里所用的强化学习。


用 SMBO 策略时，我们不是随机抓起一个模块就试，而是按照复杂性递增的顺序来测试它们并搜索结构。

这并不会缩小搜索空间，但确实用更聪明的方法达到了类似的效果。SMBO 基本上都是在讲：相比于一次尝试多件事情，不如从简单的做起，有需要时再去尝试复杂的办法。这种 PANS 方法**比原始的 NAS 效率高 5 到 8 倍**，也**便宜**了许多。

## ENAS

**高效神经架构搜索（ENAS）**，是谷歌打出的让传统架构搜索更高效的第二枪，这种方法很亲民，只要有 GPU 的普通从业者就能使用。作者假设 NAS 的计算瓶颈在于，需要把每个模型到收敛，但却只是为了衡量测试精确度，然后所有训练的权重都会丢弃掉。

因此，ENAS就要通过改进模型训练方式来提高效率。

在研究和实践中已经反复证明，迁移学习有助在短时间内实现高精确度。因为为相似任务训练的神经网络权重相似，迁移学习基本只是神经网络权重的转移。

ENAS 算法强制将所有模型的权重共享，而非从零开始训练模型到收敛，我们在之前的模型中尝试过的模块都将使用这些学习过的权重。因此，每次训练新模型是都进行迁移学习，收敛速度也更快。

下面这张表格表现了 ENAS 的效率，而这只是用单个 1080Ti 的 GPU 训练半天的结果。

<center>

![mark](http://images.iterate.site/blog/image/20190829/YzFqReXiXL62.png?imageslim)

</center>

> ENAS的表现和效率



# 相关

- [一文看懂深度学习新王者「AutoML」：是什么、怎么用？](https://zhuanlan.zhihu.com/p/42924585)
- [Learning Transferable Architectures for Scalable Image Recognition](https://link.zhihu.com/?target=https%3A//arxiv.org/pdf/1707.07012.pdf)
- [Progressive Neural Architecture Search](https://link.zhihu.com/?target=https%3A//arxiv.org/pdf/1712.00559.pdf)
- [Efficient Neural Architecture Search via Parameter Sharing](https://arxiv.org/pdf/1802.03268.pdf)
