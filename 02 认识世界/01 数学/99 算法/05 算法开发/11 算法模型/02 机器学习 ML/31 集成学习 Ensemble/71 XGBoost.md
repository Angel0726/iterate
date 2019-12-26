---
title: 71 XGBoost
toc: true
date: 2019-10-18
---
# XGBoost


XGBoost 是竞赛中的大杀器，是速度快效果好的 boosting 模型。

## 什么是XGBoost？


XGBoost 是"极端梯度上升"(Extreme Gradient Boosting)的简称，是一种现在在数据科学竞赛的获胜方案很流行的算法，它的流行源于在著名的Kaggle数据科学竞赛上被称为"奥托分类"的挑战。由于其高效的C++实现，xgboost在性能上超过了最常用使用的R包gbm和Python包sklearn。

XGBoost是使用梯度提升框架实现高效、灵活、可移植的机器学习库，是GBDT(GBM)的一个C++实现。不同于GBDT串行生成的缺点，它快速的秘诀在于算法在单机上也可以并行计算的能力。

## xgboost 如何调参


https://www.analyticsvidhya.com/blog/2016/03/complete-guide-parameter-tuning-xgboost-with-codes-python/

xgboost 调参可以参考上面这篇文章，经实践表示有用~~~

主要流程如下：

1. 先选较大的learning rate (0.05 to 0.3)
2. 再调节树模型相关参数（max_depth, min_child_weight, gamma, subsample, colsample_bytree）
3. 然后调节正则化方法及系数
4. 最后再回到降低learning rate


调参一般可以先粗调，然后细调。粗调就是选几个范围，选出效果最好的范围，然后在这个范围细调。不过用来做融合的时候不用调倒是最好，相对较好应该对融合效果会有较大收益



## XGBoost和神经网络相比各自有什么擅长和不足呢？

一个是树模型，一个是网络模型。

- 树模型擅长处理非线性特征，结构化数据，有很好的可解释性。
- 网络模型擅长处理非结构化数据，比如CNN之类的，MLP也可以处理一般数据挖掘任务的数据，但是层数过深容易过拟合，网络结构解释性不强。




## 尝试比较一下xgboost和rf。为啥xgboost的效果有些数据上表现的很好，有些数据上不如RF？


xgboost跟rf只能说形状很像，但模型内部的机理完全不一样。

一个是boosting框架，低偏差高方差，一个是bagging框架，低方差，高偏差。

当然这些都是相对的。

比如xgboost里面加入了很多随机因素，比如行采样，列采样，同样起到了bagging的效果。


如果从数据量上来看的话，量少的时候RF效果可能会优于xgb，毕竟RF是强抗过拟合的

## xgboost做了哪些优化来加速训练？


比如底层C++实现，二阶导加速收敛，特征级并行，特征排序做直方图（快速但是并不精确）



## xgboost模型的特征选择；

xgb的特征选择目前有3个方法，1.特征被分裂的次数 2.特征分裂时的增益均值 3.特征分裂时覆盖的样本数均值





# 一些资料

作者陈天奇论文：

 [XGBoost: A Scalable Tree Boosting System](http://ml-pai-learn.oss-cn-beijing.aliyuncs.com/%E6%9C%BA%E5%99%A8%E5%AD%A6%E4%B9%A0%E8%B5%84%E6%96%99/xgboost.pdf)

 [XGBoost: Reliable Large-scale Tree Boosting System](http://ml-pai-learn.oss-cn-beijing.aliyuncs.com/%E6%9C%BA%E5%99%A8%E5%AD%A6%E4%B9%A0%E8%B5%84%E6%96%99/LearningSys_2015_paper_32.pdf)

知乎讨论：

[机器学习算法中GBDT和XGBOOST的区别有哪些？](https://tianchi.aliyun.com/forum/new_articleDetail.html?postsId=2576)


XGBOOST调参：

[论XGBOOST科学调参 ](https://tianchi.aliyun.com/forum/new_articleDetail.html?postsId=2581)

 [XGBOOST调参](https://www.analyticsvidhya.com/blog/2016/03/complete-guide-parameter-tuning-xgboost-with-codes-python/)

[ XGBOOST参数](https://tianchi.aliyun.com/forum/new_articleDetail.html?postsId=2585)

GBDT：

[GBDT详解](http://ml-pai-learn.oss-cn-beijing.aliyuncs.com/%E6%9C%BA%E5%99%A8%E5%AD%A6%E4%B9%A0%E8%B5%84%E6%96%99/gbdt.pdf)

比XGBOOST更快的LightGBM：

[LightGBM论文](http://ml-pai-learn.oss-cn-beijing.aliyuncs.com/%E6%9C%BA%E5%99%A8%E5%AD%A6%E4%B9%A0%E8%B5%84%E6%96%99/LightGBM%E8%AE%BA%E6%96%87-a-communication-efficient-parallel-algorithm-for-decision-tree.pdf)

[ XGBoost, LightGBM性能大对比](https://tianchi.aliyun.com/forum/new_articleDetail.html?postsId=2586)
