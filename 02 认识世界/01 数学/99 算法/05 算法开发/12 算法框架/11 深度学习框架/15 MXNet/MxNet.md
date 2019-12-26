---
title: MxNet
toc: true
date: 2018-07-27 15:43:50
---


# 可以补充进来的

- 没有自己使用过




讲到了 MxNet，因此总结下。



## 1.MxNet初始化


MxNet官网如下：

http://mxnet.incubator.apache.org/index.html

很多同学用过 dmlc 的 Xgboost。**难道 Xgboost 与 MxNet 有关系？dmlc是什么？Xgboost怎么使用的？之前只听说过。GBDT**


## 2.MxNet实现 MLP




### 读入数据




![](http://images.iterate.site/blog/image/180727/IfCd4G68iE.png?imageslim){ width=55% }




### 定义网络

![](http://images.iterate.site/blog/image/180727/Eh47G8LFJK.png?imageslim){ width=55% }

### 训练

![](http://images.iterate.site/blog/image/180727/K8mfl2ag5g.png?imageslim){ width=55% }




### 预测




![](http://images.iterate.site/blog/image/180727/c7lHH00maH.png?imageslim){ width=55% }




## 3.MxNet与 ResNet


ResNet通过直接将 input 直接送到后续层里，在一定程度上克服了梯度衰减问题，可以训练更深的网络。但是更深的网络结构，同时加上 BN 层之后，训练的参数量变大，对存储的消耗变大。**BN层是什么？**

试试 MxNet，它对显存的利用率高一些。**什么是显存的利用率？为什么对显存的利用率更高一些？**


![](http://images.iterate.site/blog/image/180727/74dJe0lC0G.png?imageslim){ width=55% }



![](http://images.iterate.site/blog/image/180727/7A5cIfL1iL.png?imageslim){ width=55% }



![](http://images.iterate.site/blog/image/180727/8CJK9Jd469.png?imageslim){ width=55% }








# 相关：

1. 七月在线 深度学习
