---
title: 搭建 Pipeline
toc: true
date: 2019-10-18
---
# 搭建 Pipeline

很有必要搭建一个 Pipeline，至少要能够自动训练并记录最佳参数。



一般项目的 Workflow 还是比较复杂的。尤其是 Model Selection 和 Ensemble。



因此，我们需要搭建一个高自动化的 Pipeline，它可以做到：

- **模块化 Feature Transform**，只需写很少的代码就能将新的 Feature 更新到训练集中。
- **自动化 Grid Search**，只要预先设定好使用的 Model 和参数的候选，就能自动搜索并记录最佳的 Model。
- **自动化 Ensemble Generation**，每个一段时间将现有最好的 K 个 Model 拿来做 Ensemble。

对新手来说，第一点可能意义还不是太大，因为 Feature 的数量总是人脑管理的过来的；第三点问题也不大，因为往往就是在最后做几次 Ensemble。但是第二点还是很有意义的，手工记录每个 Model 的表现不仅浪费时间而且容易产生混乱。

Crowdflower Search Results Relevance 的第一名获得者 Chenglong Chen 将他在比赛中使用的 Pipeline 公开了，非常具有参考和借鉴意义。只不过看懂他的代码并将其中的逻辑抽离出来搭建这样一个框架，还是比较困难的一件事。可能在参加过几次比赛以后专门抽时间出来做会比较好。
