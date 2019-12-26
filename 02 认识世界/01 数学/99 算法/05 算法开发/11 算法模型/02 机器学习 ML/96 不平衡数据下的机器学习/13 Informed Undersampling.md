---
title: 13 Informed Undersampling
toc: true
date: 2019-08-28
---

# Informed Undersampling

既然 SMOTE 可以解决随机过采样容易发生的模型过拟合问题，对应地也有一些采样方法可以解决随机欠采样造成的数据信息丢失问题，答案是 Informed undersampling采样技术，informed undersampling采样技术主要有两种方法分别是 EasyEnsemble 算法和 BalanceCascade 算法。

EasyEnsemble算法如下图 4 所示，此算法类似于随机森林的 Bagging 方法，它把数据划分为两部分，分别是多数类样本和少数类样本，对于多数类样本 S_maj，通过 n 次有放回抽样生成 n 份子集，少数类样本分别和这 n 份样本合并训练一个模型，这样可以得到 n 个模型，最终的模型是这 n 个模型预测结果的平均值。BalanceCascade算法是一种级联算法，BalanceCascade从多数类 S_maj中有效地选择 N 且满足\midN\mid=\midS_min\mid，将 N 和\S_min合并为新的数据集进行训练，新训练集对每个多数类样本 x_i进行预测若预测对则 S_maj=S_maj-x_i。依次迭代直到满足某一停止条件，最终的模型是多次迭代模型的组合。

<center>

![mark](http://images.iterate.site/blog/image/20190828/xTuLpiCdoXnS.png?imageslim)

</center>






# 相关

- [不平衡数据下的机器学习方法简介](http://baogege.info/2015/11/16/learning-from-imbalanced-data/)
