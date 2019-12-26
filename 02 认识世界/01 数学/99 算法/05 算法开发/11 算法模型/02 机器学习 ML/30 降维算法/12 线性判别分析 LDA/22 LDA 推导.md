---
title: 22 LDA 推导
toc: true
date: 2019-08-27
---

# LDA 是为分类服务的

对于具有类别标签的数据，是如何设计目标函数使得降维的过程中不损失类别信息的呢？下面开始 LDA 的推导。


LDA 的中心思想——**最大化类间距离和最小化类内距离**




LDA 的思想非常朴素：给定训练样例集，设法将样例投影到一条直线上，使得同类样例的投影点尽可能接近、 异类样例的投影点尽可能远离。

示意图如下： LDA 的二维示意图"+"、 "-"分别代表正例和反例，椭圆表示数据簇的外轮廓，虚线表示投影， 红色实心园和实心三角形分别表示两类样本投影后的中心点.


<center>

![](http://images.iterate.site/blog/image/180626/ih4CF1e14C.png?imageslim){ width=55% }


</center>


在对新样本进行分类时，将其投影到同样的这条直线上，再根据投影点的位置来确定新样本的类别。



## 最大化类间距离


LDA 是为了分类服务的，因此只要找到一个投影方向 $\omega$，使得投影后的样本尽可能按照原始类别分开。我们不妨从一个简单的二分类问题出发，有 $C_1$、$C_2$ 两个类别的样本，两类的均值分别为 $\mu_{1}=\frac{1}{N_{1}} \sum_{x \in C_{1}} x$ ，$\mu_{2}=\frac{1}{N_{2}} \sum_{x \in C_{2}} x$。我们希望投影之后两类之间的距离尽可能大，距离表示为：

$$
D\left(C_{1}, C_{2}\right)=\left\|\widetilde{\boldsymbol{\mu}}_{1}-\widetilde{\boldsymbol{\mu}}_{2}\right\|_{2}^{2}\tag{4.17}
$$

其中，$\widetilde{\mu}_{1}, \widetilde{\mu}_{2}$ 表示两类的中心在 $\omega$ 方向上的投影向量 $\widetilde{\mu_{1}}=\omega^{\mathrm{T}} \mu_{1}$，$\widetilde{\mu_{2}}=\omega^{\mathrm{T}} \mu_{2}$，因此需要优化的问题为：

$$
\left\{\begin{array}{l}{\max _{\omega}\left\|\boldsymbol{\omega}^{\mathrm{T}}\left(\boldsymbol{\mu}_{1}-\boldsymbol{\mu}_{2}\right)\right\|_{2}^{2}} \\ {\text { s.t. } \boldsymbol{\omega}^{\mathrm{T}} \boldsymbol{\omega}=1}\end{array}\right.\tag{4.18}
$$

容易发现，当 $\omega$ 方向与 $(\mu_1−\mu_2)$ 一致的时候，该距离达到最大值。

例如对图 4.5（a）的黄棕两种类别的样本点进行降维时，若按照最大化两类投影中心距离的准则，会将样本点投影到下方的黑线上。

<center>

![](http://images.iterate.site/blog/image/20190331/TYnsehnYY3Qn.png?imageslim){ width=55% }

</center>

但是原本可以被线性划分的两类样本，经过投影后有了一定程度的重叠，这显然不能使我们满意。


我们希望得到的投影结果如图 4.5（b）所示，虽然两类的中心在投影之后的距离有所减小，但确使投影之后样本的可区分性提高了。<span style="color:red;">是的。</span>

<center>

![](http://images.iterate.site/blog/image/20190331/tQSyPCfHdwtM.png?imageslim){ width=55% }

</center>


仔细观察两种投影方式的区别，可以发现，在图 4.5（b）中，投影后的样本点似乎在每一类中分布得更为集中了，用数学化的语言描述就是每类内部的方差比左图中更小。

这就引出了 LDA 的中心思想——**最大化类间距离和最小化类内距离**。<span style="color:red;">是的。</span>



## 最小化类内方差

在前文中我们已经找到了使得类间距离尽可能大的投影方式，现在只需要同时优化类内方差，使其尽可能小。

我们将整个数据集的类内方差定义为各个类分别的方差之和，将目标函数定义为类间距离和类内距离的比值，于是引出我们需要最大化的目标：<span style="color:red;">这个地方有点不清楚，为什么这么定义？别的定义不行吗？</span>


$$
\max _{\omega} J(\omega)=\frac{\left\|\omega^{\mathrm{T}}\left(\mu_{1}-\mu_{2}\right)\right\|_{2}^{2}}{D_{1}+D_{2}}\tag{4.19}
$$

其中 $ω$ 为单位向量，$D_1$，$D_2$ 分别表示两类投影后的方差，

$$
\begin{aligned}D_{1}&=\sum_{x \in C_{1}}\left(\omega^{\mathrm{T}} x-\omega^{\mathrm{T}} \mu_{1}\right)^{2}\\&=  \sum_{x \in C_{1}} \omega^{\mathrm{T}}\left(x-\mu_{1}\right)\left(x-\mu_{1}\right)^{\mathrm{T}} \omega\end{aligned}\tag{4.20}
$$

$$
D_{2}=\sum_{x \in C_{2}} \omega^{\mathrm{T}}\left(x-\mu_{2}\right)\left(x-\mu_{2}\right)^{\mathrm{T}} \omega \tag{4.21}
$$

因此 $J(\omega)$ 可以写成：

$$
J(\omega)=\frac{\omega^{\mathrm{T}}\left(\mu_{1}-\mu_{2}\right)\left(\mu_{1}-\mu_{2}\right)^{\mathrm{T}} \omega}{\sum_{x \in C_{i}} \omega^{\mathrm{T}}\left(x-\mu_{i}\right)\left(x-\mu_{i}\right)^{\mathrm{T}} \omega}\tag{4.22}
$$


定义类间散度矩阵 $\boldsymbol{S}_{B}=\left(\boldsymbol{\mu}_{1}-\boldsymbol{\mu}_{2}\right)\left(\boldsymbol{\mu}_{1}-\boldsymbol{\mu}_{2}\right)^{\mathrm{T}}$，类内散度矩阵 $S_{w}=\sum_{x \in C_{i}}\left(x-\mu_{i}\right)\left(x-\mu_{i}\right)^{\mathrm{T}}$ 。则式（4.22）可以写为：

$$
J(\omega)=\frac{\omega^{\mathrm{T}} \boldsymbol{S}_{B} \boldsymbol{\omega}}{\boldsymbol{\omega}^{\mathrm{T}} \boldsymbol{S}_{w} \boldsymbol{\omega}}\tag{4.23}
$$


我们要最大化 J（ω），只需对ω求偏导，并令导数等于零


$$
\frac{\partial J(\omega)}{\partial \omega}=\frac{
\left(\frac{\partial \boldsymbol{\omega}^{\mathrm{T}} \boldsymbol{S}_{B} \boldsymbol{\omega}}{\partial \boldsymbol{\omega}} \boldsymbol{\omega}^{\mathrm{T}} \boldsymbol{S}_{w} \boldsymbol{\omega}-\frac{\partial \boldsymbol{\omega}^{\mathrm{T}} \boldsymbol{S}_{w} \boldsymbol{\omega}}{\partial \boldsymbol{\omega}} \boldsymbol{\omega}^{\mathrm{T}} \boldsymbol{S}_{B} \boldsymbol{\omega}\right)
}{\left(\omega^{\mathrm{T}} \boldsymbol{S}_{w} \boldsymbol{\omega}\right)^{2}}=0\tag{4.24}
$$


于是得出：

$$
\left(\boldsymbol{\omega}^{\mathrm{T}} \boldsymbol{S}_{w} \boldsymbol{\omega}\right) \boldsymbol{S}_{B} \boldsymbol{\omega}=\left(\boldsymbol{\omega}^{\mathrm{T}} \boldsymbol{S}_{B} \boldsymbol{\omega}\right) \boldsymbol{S}_{w} \boldsymbol{\omega}\tag{4.25}
$$

由于在简化的二分类问题中 $\omega^TS_W\omega$ 和 $\omega^TS_B\omega$ 是两个数，我们令 $\lambda=J(\omega)=\frac{\omega^{\mathrm{T}} S_{B} \omega}{\omega^{\mathrm{T}} S_{w} \omega}$ ，于是可以把式（4.25）写成如下形式：

$$
\boldsymbol{S}_{B} \boldsymbol{\omega}=\lambda \boldsymbol{S}_{w} \boldsymbol{\omega}\tag{4.26}
$$


整理得：

$$
\boldsymbol{S}_{w}^{-1} \boldsymbol{S}_{B} \boldsymbol{\omega}=\lambda \boldsymbol{\omega}\tag{4.27}
$$


从这里我们可以看出，我们最大化的目标对应了一个矩阵的特征值，于是 LDA 降维变成了一个求矩阵特征向量的问题。<span style="color:red;">哇塞，有些厉害呀</span>

$J(w)$ 就对应了矩阵 $S_W^{-1}S_B$ 最大的特征值，而投影方向就是这个特征值对应的特征向量。


对于二分类这一问题，由于 $S_B=(\mu_1-\mu_2)(\mu_1-\mu_2)^T$ ，因此 $S_B\omega$ 的方向始终与 $μ_1−μ_2$ 一致，如果只考虑ω的方向，不考虑其长度，可以得到 $\omega=S_W^{-1}(\mu_1-\mu_2)$ 。

换句话说，我们只需要求样本的均值和类内方差，就可以马上得出最佳的投影方向 $\omega$ 。这便是 Fisher 在 1936 年提出的线性判别分析。<span style="color:red;">哇塞！厉害！</span>


# 相关

- 《百面机器学习》
