---
title: 用一张图理解SVM的脉络
toc: true
date: 2019-11-17
---
# 用一张图理解SVM的脉络





在各种机器学习算法中，SVM是对数学要求较高的一种，一直以来不易被初学者掌握。如果能把握住推导的整体思路，则能降低理解的难度，在本文中[SIGAI](http://mp.weixin.qq.com/s?__biz=MzU4MjQ3MDkwNA==&mid=2247483731&idx=1&sn=237c52bc9ddfe65779b73ef8b5507f3c&chksm=fdb69cc4cac115d2ca505e0deb975960a792a0106a5314ffe3052f8e02a75c9fef458fd3aca2&scene=21#wechat_redirect)将通过一张图来为大家讲述SVM的整个推导过程。



SVM由Vapnik等人在1995年提出，在出现之后的20多年里它是最具影响力的机器学习算法之一。在深度学习技术出现之前，使用高斯核的SVM在很多问题上一度取得了最好的效果。SVM不仅可以用于分类问题，还可以用于回归问题。它具有泛化性能好，适合小样本等优点，被广泛应用于各种实际问题。



下面我们开始整个推导过程。先看这张图：

![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIziapiahhUnPhjrClqwdY9Fss9HFTxic81WKffiaEZw9UQ0hib8js8D8MwibQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

最简单的SVM从线性分类器导出，根据最大化分类间隔的目标，我们可以得到线性可分问题的SVM训练时求解的问题。但现实应用中很多数据是线性不可分的，通过加入松弛变量和惩罚因子，可以将SVM推广到线性不可分的情况，具体做法是对违反约束条件的训练样本进行惩罚，得到线性不可分的SVM训练时优化的问题。这个优化问题是一个凸优化问题，并且满足Slater条件，因此强对偶成立，通过拉格朗日对偶可以将其转化成对偶问题求解。



到这里为止，支持向量机还是一个线性模型，只不过允许有错分的训练样本存在。通过核函数，可以将它转化成非线性模型，此时的对偶问题也是一个凸优化问题。这个问题的求解普遍使用的是SMO算法，这是一种分治法，它每次选择两个变量进行优化，这两个变量的优化问题是一个带等式和不等式约束条件的二次函数极值问题，可以求出公式解，并且这个问题也是凸优化问题。优化变量的选择通过KKT条件来确定。



下面我们按照这个思路展开，给出SVM完整的推导过程，难点在于拉格朗日对偶和KKT条件。



预备知识







为了大家能够理解推导过程，我们先介绍KKT条件。在微积分中我们学习过，带等式约束的最优化问题可以用拉格朗日乘数法求解，对于既有等式约束又有不等式约束的问题，也有类似的条件定义函数的最优解-这就是KKT条件。对于如下优化问题：

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIJxWhbrJL5KJreesVDeT2s5qh13xCpzQ24Bm6UTJ2ygc2f0zYKElppA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

首先构造拉格朗日乘子函数：

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIYGEW43QFO3S8S2EjJWvfCicS6kcxGEZCCewNUPF0aBRweibLVfHExfnQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

其中![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIGEgB4Eo8qrEQickdfp9yOW3R28KXliawDABZukQPLkC3abyZhKRUK0TQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)称为拉格朗日乘子。最优解![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaI7BCXE97JA8EuicjiaiaoQ4cGicF9wev7ABjr6riaaCiaGDDLjPaSd5j5VLMQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)必须满足如下条件：

![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIGibHiamGKnJ2z4UoceorI52GrubbSiaCqKyY4Q5xJoksFW8hvcS6LrAFw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

除了原本应该满足的等式约束和不等式约束之外,

![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaISWvLZj7DS7v8N4ae2529D8m0qVjkuPbQAzjjs4n7pPfbQo6q1rLH7A/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

和拉格朗日乘数法一样。唯独多了

![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIEAOj84BdAOphEJnmZqPDUR2MuqTbt4ss3HAacYicPaceicfCXr8yBvXQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

这一条件。



下面介绍拉格朗日对偶。对偶是最求解优化问题的一种手段，它将一个优化问题转化为另外一个更容易求解的问题，这两个问题是等价的。常见的对偶有拉格朗日对偶、Fenchel对偶。这里我们介绍拉格朗日对偶。



对于如下带等式约束和不等式约束的最优化问题：

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIXGibDGdssIvHS2NQCfNSOxXKuorHvyazy6hdNibpq2osUuRq5w1z2yCw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

仿照拉格朗日乘数法构造如下广义拉格朗日函数：

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIBTGHW0oxIoybw9QN89Lu6mELQ4icibf58bl7Khetp5J4MNiaEJWtf2M3w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

同样的称![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIumiaLicEdibdWUgjFXLVcDpW3Tt9KvYic0aIuBkpLz42T9GsYt4eYkSuzw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1) 为拉格朗日乘子。变量![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIWgwGD8GObnktrCWdJQUAicTQIzxR6Zk9UeYQIXa3pINiaNev8gg9Sb2g/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)必须满足![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIl9wxLD7y6Gcpb9zcrzzNDjtYhINtCTIHQZdFzxyW1wylSBMbJBtfIA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)的约束。接下来将上面的问题转化为如下所谓的原问题形式，其最优解为：

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaI5x6bp9VqhbZJj121LlicgNdB9wiatDyFzNDB4GqiaZVQjeoIxrE0ARjgA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

等式右边的含义是先固定住变量x，将其看成常数，让拉格朗日函数对乘子变量![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIep2qpicNJYEkbtmTjbXookoJvn32BdQzvjD849vVLhU0MpkmAicP11Uw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)求最大值。消掉这两组变量之后，再对变量x求最小值。为了简化表述，定义如下最大化问题：

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIUPWVHzKI8KKruZzezeJtEV1aD8lDJJkgqTXDP4WDzIZJs6v8CpKeGQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

这是一个对乘子变量求最大值的问题，将x看成常数。这样原问题被转化为先对乘子变量求最大值，再对x求最小值。这个原问题和我们要求解的最小化问题有同样的解，如果x违反了等式或不等式约束，上面问题的最优解是无穷大，因此不可能是问题的解。如果x满足等式和不等式约束，上面的问题的最优解就是![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaI5D7zlnbJ9PS6Lu9dq88asf8iaIiaEtmiaLB2OGOs8kpDMRZgMmWmibCKnw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1), 因此二者等价。通过这样的构造，将带约束条件的问题转化成对x没有约束的问题。详细的证明在SIGAI后续的文章中会给出。



接下来定义对偶问题与其最优解：



![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIhZKicXcQYh6xAGKFb1iap7TeicrjOwdOPUA8mHJJCZNpdQzSSSxQABoOQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



其中

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIlOZHnBJNvMJqgYseCcknKFNyCDia4D4z1cSQVicb3geCvnF5KDQX5Grw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

和上面的做法相反，这里是先固定拉格朗日乘子，调整x让拉格朗日函数对x求极小值；然后再调整拉格朗日乘子对函数求极大值。



原问题和对偶问题只是改变了求极大值和极小值的顺序，每次操控的变量是一样的。如果原问题和对偶问题都存在最优解，则对偶问题的最优值不大于原问题的最优值，即：



![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIuIxCg9l3PFmhCsgMbA5J9rYiayZ54ONv3Stw8GFPDwKzmey8vBQDQIQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



这称为弱对偶，后面的文章中我们会给出证明。原问题最优值和对偶问题最优值的差![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIbdNoAJbyTzL9fVAkTcqHjzNRiamFqLzAzp56k0p9zjsbkkf6yEGhhMw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)称为对偶间隙。如果原问题和对偶问题有相同的最优解，我们就可以把求解原问题转化为求解对偶问题，这称为强对偶。强对偶成立的一种前提条件是Slater条件。



Slater条件指出，一个凸优化问题如果存在一个候选x使得所有不等式约束都严格满足，即对于所有的i都有![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaImunJJE0GqpqFlPj3fZr9PzfgCDFQRcI4d7VrtmyEfAqLiaMOLYfSibPA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)不等式不取等号。则存在![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIX6u7oL1miaPnhQPlHxaYZVKM2HA4TiaZbw7puUYiaq2QV4VQRt7obPADA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)使得它们分别为原问题和对偶问题的最优解，并且：



![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIB3lQeichZ8ZpAtddmutPWBV3e6Y79LJsibkKm9uJS9ic28VnEiavwE5sYQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



Slater条件是强对偶成立的充分条件而不是必要条件。强对偶的意义在于：我们可以将求原问题转化为求对偶问题，有些时候对偶问题比原问题更容易求解。强对偶只是将原问题转化成对偶问题，而这个对偶问题怎么求解则是另外一个问题。



线性可分的情况







首先我们来看最简单的情况，线性可分的SVM。对于二分类问题，线性分类器用一个超平面将两类样本分开，对于二维平面，这个超平面是一条直线。线性分类器的判别函数为：

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIQuuEJGNGlibCEJbaHXdwYMSat5beGudAfjs08aNKlK5b7WV9NicWXgpQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

其中，w为权重向量，b为偏置项，是一个标量。一般情况下，给定一组训练样本可以得到不止一个线性分类器，下图就是一个例子：



![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIObyMGicTPDhoMKD1Wuxiax0HAFG1IaOXXCl11W7Uh4tsNlBAttQZS8IQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

两个不同的线性分类器



上面的两个线性分类器都可以将两类样本分开，既然有不止一个可行的线性分类器，那么哪个分类器是最好的？SVM的目标是寻找一个分类超平面，它不仅能正确的分类每一个样本，并且要使得每一类样本中距离超平面最近的样本到超平面的距离尽可能远。



给定一批训练样本，假设样本的特征向量为x，类别标签为y，取值为+1或者-1，分别代表正样本和负样本。SVM为这些样本寻找一个最优分类超平面，其方程为：

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIyfOdJ3e93kW2Sth5RiarZD6EibrNae2RFnCibCtzicfTbwpuicxzNvG73Jw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

首先要保证每个样本都被正确分类。对于正样本有：

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIZicG3WEWJZvadUT4E7YxSdNOo54DibcUVIs7Rx3taTPVh56M19uc5YJA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

对于负样本有：

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaI5w5g3Nbib72dhKbbdMVibdTWG6lF7DbRbRVAic4jXoicnNCD1FGskpnrOw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

由于正样本的的类别标签为+1，负样本的类别标签为-1，可以统一写成如下不等式约束：

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIicz8rUg2kiaVI1hlCPtzs9BWCAX61ibVZFuibruVKygRanJRNlJwg7HhcA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

第二个要求是超平面离两类样本的距离要尽可能大。根据解析几何中点到平面的距离公式，每个样本点离分类超平面的距离为：

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIiaxKCBAefh8RiaGloOjyYFCUaypibQR0vjW6ibmVelfjsLoajHwaNZsF0A/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)





其中



![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIx4CY64OWXeKGRjJ9v86dbo6PDdbK2iawbeqbeee5CuR6aBFnrXDzyhQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

是向量的L2范数。上面的分类超平面方程有冗余，如果将方程两边都乘以不等于0的常数，还是同一个超平面。利用这个特点可以简化问题的表述。对w和b加上如下约束：

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIn4Wwa51d4CoESgFkCB6miayWMxaDCaZ5ianB9oCoibDswzfds4f3DibrJg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

即离超平面最近的正、负样本代入超平面方程之后绝对值为1。这样可以消掉这个冗余，同时简化了点到平面距离的计算公式。对分类超平面的约束变成：

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIvmM2iaEkjj1LNCIHeZWE81G7OdjUxULJK65LE6hOtia4aVicVjfI5cKeg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

这是上面那个不等式约束的加强版。分类超平面与两类样本之间的间隔为：

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIBYhibnQfDOavzjb8175ztOZuBPyia3y1XXq0Ixib0e6ZMN6ibEaw59cKmA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

目标是使得这个间隔最大化，这等价于最小化下面的函数：

![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIwAIEiafIN36tDIhylk0MlbQ5Eiagal0Geicmdujd0pic5myLr4oklhnibBA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

带上前面定义约束条件之后优化问题可以写成：

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIz2amoGgIdvOeJPXk8KNpezKejdJHTLZszfzvns4ldu3V6LhqkUHmFg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

下图是线性可分的SVM示意图：



![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIg9lVlhj5YiaQW9PJ5zafiafLFaqlOG5gEHicAPicBg01kk2IULlOJknFRQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

线性可分的支持向量机示意图



线性不可分的情况







线性可分的SVM不具有太多的实用价值，因为现实问题中样本一般都不是线性可分的，接下来我们将它进行扩展，得到能够解决线性不可分问题的模型。为了处理这个问题，当线性不可分时通过加上松弛变量和惩罚因子对错误分类的样本进行惩罚，可以得到如下最优化问题：

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaI3ogMPe4rlweVUBhxRrY7TFDar2UvOgNOPGOOsg8z0IMcRBuc2rhHSw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

其中![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIh1HVawyuCwN5QoYKhvDpZFNCe2JVGe0pbfCcS728H1hEAjia87sZOOw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)是松弛变量，如果它不为0，表示样本突破了不等式约束条件。C为惩罚因子，是人工设定的大于0的参数，用来对突破了不等式约束条件的样本进行惩罚。可以证明这个问题是凸优化问题，因此可以保证求得全局最优解，在后面的文章中[SIGAI](http://mp.weixin.qq.com/s?__biz=MzU4MjQ3MDkwNA==&mid=2247483731&idx=1&sn=237c52bc9ddfe65779b73ef8b5507f3c&chksm=fdb69cc4cac115d2ca505e0deb975960a792a0106a5314ffe3052f8e02a75c9fef458fd3aca2&scene=21#wechat_redirect)会给出证明，请关注我们的微信公众号。



另外，上述问题是满足Slater条件的。如果令![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIqXMpenLXMKBMIINJncPH2xrKvk6WYgJIxZVbeGbr60MlOzGIwrdFYw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

则有

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIoCS44veXozgOfoQ1QUn6X8cUC4rMicFF0ZSmm5unE9l1DzChJzutAhg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

不等式条件严格满足，因此强对偶条件成立，原问题和对偶问题有相同的最优解。因此可以转化成对偶问题求解，这样做的原因是原问题的不等式约束太多太复杂，不易于求解。



对偶问题







下面介绍如何将原问题转化成对偶问题。首先将上面最优化问题的等式和不等式约束方程写成标准形式：



![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIvcq6DibD8b0osxt9wCLZ6OyGLWq5qiaNkcq8yboPjC0ShHHXCvuDWkyg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIohZ8alrpRSNRW3JAB3gouGhIzwpwHuyvx4obJPu0x0IWA23vBEbDSQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

然后构造拉格朗日乘子函数：



![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIkkIm8YkBtESyxKnARdB12ib4ZdMzdLo77wheiajfwPZToyvicdTMp8X5A/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



其中![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIPEWprhicrbDKoFVmuFficxNtIGtdK6LibkxLlaUeDtzYyoYlVgLRFOy9Q/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)是拉格朗日乘子。转换成对偶问题的具体做法是先固定住这两组乘子变量，对![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIicy5R0jT9Jqqg1Mktb7mNFjicNWibk3XicISsKPGH9mrx8h1DQ0MI5IMLg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1) 求偏导数并令它们为0，得到如下方程组：

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaI9q9n3xc1tXOyRljqw6ic3ic6hgapib9Yc7j5J8yWfNgINkxibyXtDBxE8w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIC5WvweHM9KbqE2lr7aAPgpwqLiatg4lHia2MfxCwdwxBNR4QGC2vBY6Q/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIWJ6lBT4pdSILDicGW66GE5ia5d9buteonpCtLrhRMLiaLuVtuObkm4FIQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

将上面的这些结果代入拉格朗日函数中，消掉这些变量，得到关于乘子变量![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaI8jenQw4yzKhd8iblQPuK7CWJDxDUBur5ubzBHiaUXNCWeDFwEpOkEwtg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)的函数，然后控制乘子变量，对函数取极大值

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIlc1q0iavfuh5aleFiaKgOJOM3MmWAMh9icMARh11IRoSThDvYLcXgFOLQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

由于有等式约束![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIrXAwwibibM7V0SZeBOQU2Naicj6AIiavV3ibqTYcK19ypxT6pdiaWiatoAY6Q/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1), 并且有不等式约束![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaI4QvicFaNE1S6U2VFJXicFj5uIiaicGYYY540kgxDiag9VQr4KaKcgicfyXRw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1), 因此有![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaI3Utf7bwJvYia5ricoE3uegiayiaa1FcvbhWRgzc2JvYCw0QtFnmBNPdWicg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

这等价与如下最优化问题

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIm2Im1oOoAHPcGvl3Hic55Yzh0McqyFNwy05dAEX3l4Zy5JTuGicok5Qw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

转化成对偶问题之后，不等式和等式约束都很简单，求解更为容易。可以证明，上面这个问题是也凸优化问题，可以保证求得全局最优解，在[SIGAI](http://mp.weixin.qq.com/s?__biz=MzU4MjQ3MDkwNA==&mid=2247483731&idx=1&sn=237c52bc9ddfe65779b73ef8b5507f3c&chksm=fdb69cc4cac115d2ca505e0deb975960a792a0106a5314ffe3052f8e02a75c9fef458fd3aca2&scene=21#wechat_redirect)后续的文章中我们将给出证明，请大家关注我们的微信公众号。将w的值代入超平面方程，最后的策函数为：

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIkqBGBQWA2eEwia5bcLnqDI3truDIWArqInrzzx2XSGHUXltPNxiarNRg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

那些![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaI7tKzFD3iaEfXYP9pc9Wu2UfiaDibZ1SG64qb6XX46WSMC1nO8nYVVqjqw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)的样本即为支持向量，下面是支持向量的示意图：



![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaI77dgHvk3lZYAtrZeSwGgy7feKUwibLPq1Aicn5pRqbDtN3faaXGQqeyQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

支持向量示意图



核函数







虽然加入了松弛变量和惩罚因子，但支持向量机还是一个线性模型，只是允许错分样本的存在，这从它的决策函数也可以看出来。接下来要介绍的核映射使得支持向量机成为非线性模型，决策边界不再是线性的超平面，而可以是形状非常复杂的曲面。



如果样本线性不可分，可以对特征向量进行映射将它转化到一般来说更高维的空间，使得在该空间中是线性可分的，这种方法在机器学习中被称为核技巧。核映射将特征向量变换到另外一个空间：

![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIWkqGQfI2jz4CHYRApM3BknalkuMOh6UbNU4k1C2H2JXibv5r2ygGOXA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

在对偶问题中计算的是两个样本向量之间的内积，因此映射后的向量在对偶问题中为：

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaI9VWQGKRX1R0icMXlaFAPeaFvWL1LFohYM18LyqicEjoeSica7xhdRsnvg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

直接计算效率太低，而且不容易构造映射函数。如果映射函数选取得当，能够确保存在函数K，使得下面等式成立：

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIXqh6byzQicQpSyrP54jKabQFnDdsBzoKaESfRmITCBxeEKPUm8ee2og/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

这样只需先对向量做内积然后用函数K进行变换，这等价于先对向量做核映射然后再做内积。在这里我们看到了求解对偶问题的另外一个好处，对偶问题中出现的是样本特征向量之间的内积，而核函数刚好作用于这种内积，替代对特征向量的核映射。满足上面条件的函数称为核函数，常用的核函数有以下几种：



![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIU0FmNficydXrhvP3RPNLibSwLSSM8ov7VyKicL8Ok6KaGW0zcnuwvHFUQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

各种核函数与它们的计算公式



核函数的精妙之处在于不用真的对特征向量做核映射，而是直接对特征向量的内积进行变换，而这种变换却等价于先对特征向量做核映射然后做内积。



为向量加上核映射后，要求解的最优化问题变为：

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIZwKB7lDBT9DPt1rqbk0n1yF3T35n24VF9RC7MzKrJ7F8CT0wqmfu5Q/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

根据核函数满足的等式条件，它等价于下面的问题：

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaI49kOqwWkf3ZWm9Z4MpIWlVn3HIOiaPXyFRlbV3ulg26eiaicVam6XicqTA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

最后得到的分类判别函数为：

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIibA3PR2C8O8P87Ypym40fVR54EtIaU1oIIUUMBUuB7XYImbVmdG9Xaw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

和不用核映射相比，只是求解的目标函数、最后的判定函数对特征向量的内积做了核函数变换。如果K是一个非线性函数，上面的决策函数则是非线性函数，此时SVM是非线性模型。当训练样本很多、支持向量的个数很大的时候，预测时的速度是一个问题，因此很多时候我们会使用线性支持向量机。



如果我们定义矩阵Q，其元素为：

![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIaTjlokOanHrWHa6Ohers6qnfez365uH39ocTRf001oyMcqrEK9H8pQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

同时定义矩阵K，其元素为：

![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaId6HXdXaPFibJTByPT20ZgtpvYLBQOlHrSMibWlHiaPwjSxgM41bC6cWRg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



对偶问题可以写成矩阵和向量形式：

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIDxQcc5c1Ur5G8UZBz0YQow0UFicLow3a6oAfYR7Op88WXDJmuvIegeA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

可以证明，这个对偶问题同样是凸优化问题，这是由核函数的性质保证的，在[SIGAI](http://mp.weixin.qq.com/s?__biz=MzU4MjQ3MDkwNA==&mid=2247483731&idx=1&sn=237c52bc9ddfe65779b73ef8b5507f3c&chksm=fdb69cc4cac115d2ca505e0deb975960a792a0106a5314ffe3052f8e02a75c9fef458fd3aca2&scene=21#wechat_redirect)公众号SVM系列的后续文章中我们会介绍。下图是使用高斯核的SVM对异或问题的分类结果：



![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaI9WYzgMjEED0PK1g9y3dcpkaBgtQoAyCG3QeUh9LgsCtBEpicrKGeJtQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



只要参数设置得当，使用高斯核的支持向量机确实能解决非线性分类问题，分类边界可以是非常复杂的曲线。



KKT条件







对于带等式和不等式约束的问题，在最优点处必须满足KKT条件，将KKT条件应用于SVM原问题的拉格朗日乘子函数，得到关于所有变量的方程，对于原问题中的两组不等式约束，根据KKT条件必须满足：

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIzhKtldtt5gmxZ8JI4BYWMSb7uVWXASDhyBeLgVIjug5ZZtggY1w1Rw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



对于第一个方程，如果![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaICO4rsvYYHiaRTuoicN1KueRNZ4Hs8SmyCJUVFXPXLFJbMAP6N9LwHBmw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)，则必须有



![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIu0jsn0ZBQO1yN9xlIQUgiajt2Y5j1jNfAAicruj3tRumShQPDR3nXqlQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

即

![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIf8KicQITU6ZBic1pWopOlVXbEibz30FicLnJictWzvwoKaLp6icpglJkQrxQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



而由于![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIwxn2Q0oDYpRVsh2ULC0FSLNzA9ZIIJN5EMQ6W1AssIzhjOSViba1Q9g/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)，因此必定有



![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIG5Ym381klkhGSto8ECAdRibd8OwX0f7fh9IPCzw6QksUcsWgYGZrmIw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)。



再来看第二种情况：如果![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIg3Z6A84ch78wVna2MgFTQiaFP7Ye9PqRveSmI450G02C2r7glicGefrw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)，则对



![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIbUyDUcQibxCpSTWjEWpUwVSRWfqyM9huBMib3L0m7r1J1NlEf59cqibaA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



的值没有约束。由于有![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIiaQeAQ6X2bmnuGJVLcFyg2cPucuawoR6CCBm4XDWxCZfSicDWqK3w2Hg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1) 的约束，因此![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIwibf5TPnr216rnWUjTj1qJNETn68GVQhrCkib4NXQ30SVl5Wj62TYTbw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)；又因为![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIRJG0bWZ4icutibGGoia3FLUWNGhexqXIrguOP8ekuAhE3162uAn23L5Dw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)的限制，如果![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaI0O3F1tZSluvvcueW65UGgzaSLOTMT2AN0ZLO6PiaPdicvHtrJQDumAhw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)，则必须有![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIOZ82QZ3zhicOnySxA4JBib9Ol0aY0PH78K5Tk4toAcctENOXwNqImFibA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)。由于原问题中有约束条件



![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIMPmDoqnEdT7offUqReLbaoQ5aGiaayyX6pricmSAUA2ic9msa7m78l5dw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

而由于![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIOZ82QZ3zhicOnySxA4JBib9Ol0aY0PH78K5Tk4toAcctENOXwNqImFibA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)，因此有

![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaICMeUzIdcYKk7wTwr8GPic9XIOXvYI9UzxcxLCaKXtyej2VvvdfgJXpA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



对于![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIdY8FQ8dbPq4y63zaJaniar2NQFB0GeRFriapzyX6y5p5oWWm6jkhHeGA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)的情况，我们又可以细分为![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIogYtcbooleq9eoUGBeuLicqTBxrHStaAHzHicicKmiaI0IRdY2ud1sdS0Q/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)和![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaINMc6qGT9FenqFxmBr1IrCicT4shsp9MzmtzZFTcN340WK0WRpB9c3EA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)。如果![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaINom4Pb8r08vyjRiaqY247RX4kYPdrcxxTF2gy4RJFwqR1TU249VooKQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)，由于有![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIg1BGYbMcTicFpwjibbsxmJ0x80Q1ZXXISBPCwZxiawhibzSkBHPVsBkKcA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)的约束，因此有![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaI2wqAwAmZZticFhE5co3sEYickhlLns5KFVicfKiaVVOrnxFic7pOvQN3oeQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)；因为有![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaI7W06xoZQOMHPNibESK5z3xR1WgV6jmkboonhbUSvL51Bwy2xlUWJU8g/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)的约束，因此![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIeW98RPX2TKYy9SwOFxmtWJ9AwKz0yIgtPpDxTC8TqSo91CibjibPBWIA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)。不等式约束：



![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaI45oFtqhxVic5zYu1W1XolicKV3usPtbrTIznQWzGPkT7ibYxjAv3Y7hzA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

变为

![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIntpEcjyibQYWoicxnib0CMIgt8JiaLt3EZ7KvkLm6xYaHDlDY36dZejcNw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



由于![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIdZbTwL4qibEjf5kPYfudpE5NGNQeQqaQICRUy9piaIaiclcdqy4AN3Wgg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)时，既要满足



![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIDklsTfkzrvsqIpkljtMOSl5XnXqZ9H2gsFZ2ASunJnlMbRht54p9DQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

又要满足

![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaICDfwIGQOicXDfQONiaOu34qFT4mMIx9YATFjibsdibKUxPqYXdyPgAUPoQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

因此有

![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIMJn1G95bjogpIlkl3e5LYAYVCVyosfrHFricp7Usaku1bXibTuler3TQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



将三种情况合并起来，在最优点处，所有的样本都必须满足：

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIJXUISEICz06why4ay5bh8A1lffLE0ZicyYJlaiaxZ8q24sMQ4gCXczZA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

上面第一种情况对应的是自由变量即非支持向量，第二种情况对应的是支持向量，第三种情况对应的是违反不等式约束的样本。在后面的求解算法中，会应用此条件来选择优化变量。



SMO算法







前面我们给出了SVM的对偶问题，但并没有说明对偶问题怎么求解。由于矩阵Q的规模和样本数相等，当训练样本数很大的时候，这个矩阵的规模很大，求解二次规划问题的经典算法将会遇到性能问题。下面将介绍SVM最优化问题的高效求解算法-经典的SMO算法。



SMO算法由Platt等人在1998年提出，是求解SVM对偶问题的高效算法。这个算法的思路是每次在优化变量中挑出两个分量进行优化，而让其他分量固定，这样才能保证满足等式约束条件，这是一种分治法的思想。



下面先给出对于这两个变量的优化问题（称为子问题）的求解方法。假设选取的两个分量为![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaI8ia5UWTHQ9Wub5k0eA6fOtoibicDNk7oNU8sqicUWKtGrNBak3iaPYfpZpg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)，其他分量都固定即当成常数。由于

![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIKmNmXC2DUbPnYlbMicnReRF6yewXVGdb6ytqibSQLlqGkFLSZ49Oz1Hg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

以及

![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIoUC6Py1JRD1PyciaxraGQgZAafBGSdytMrKA1tJ4s7ncdn8suFq7Urg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

对这两个变量的目标函数可以写成：

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIy6zU72dO61bqOcS4vde4etJRN1k9QLeeJt69CD5GWbwQMjh3GnxywQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



其中c是一个常数。前面的二次项很容易计算出来，一次项要复杂一些，其中：

![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIxNuAyXiad30SxDR7g4dkMrCooAeXHZpn3CMH5OKicibq3A33936U5svqg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIZRmkICXazGecSMckDkDbCjhINTbWZJTKLBrryD22eqich9483jMlqJA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



这里的变量![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIGhvoHUqZkdC4eHsC1kkb6S77Joq2J6BiaRcSY0yia31dRMX2AmSxDwcg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)为变量а在上一轮迭代后的值。上面的目标函数是一个两变量的二次函数，我们可以直接给出最小值的解析解（公式解），只要你学过初中数学，都能理解这个方法。这个问题的约束条件为：



![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIp702db0pkNKVo7iaqtNwrX6Yj7p4ibwV3WwBbEbtXPWuFVnGbyBHLvYg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaICCibWZfAMJ4NNmMN3HErJqaOenx2o7iclb3d1re9orBEpX8ria2ibB9bXg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIwfkrRIJdV4bic0Jab6nufEjC4yacPPHQEPDXX45ALOkWA7Zn2con3VA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

前面两个不等式约束构成了一个矩形，最后一个等式约束是一条直线。由于![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIG0sVccPDiaO2fxuRMduKq7sQ9bNwXGjp599uibxTH1KIkicocqd0ibunlQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)的取值只能为+1或者-1，如果它们异号，等式约束为![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIqSQPS4pKJcveicfRyl0xCJWejJb8xPDUwU1uWnHaIYRLhbyYicEJjsxg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

它确定的可行域是一条斜率为1的直线段。因为![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIAYGWaWOcP5M4wwbKoyf0TXzqh6meOyd9Iot4W6p7R5WBDe1RoffoXA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)，要满足约束条件![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaI1KakBUtLibgkKrd7LItQJBhnsb6pcWGW7ibicaicNQ48HzGzCETgKibofXw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1) 和![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIYxuTHrxZKeQPe0dtxm3HjbBJRUexNCUaFMGopibibmrFtia3IWgajdH9w/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)，它们的可行域如下图所示：



![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaInJrtuWJaDMkbuAHgVY6qyESCZ2ja8s5BRribewYialPAjEz0kmquTicWw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

可行域示意图-情况1



上图中的两条直线分别对应于![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaILnrCKgOxVjSs1ntE8AIbicHK7qHOiazIwlD2tqfoJ4lJJqiauS02gyEicA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)为1和-1的情况。如果是上面那条直线，则![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaICUeI3hwumrHPEBjj88QhIcsWmic3GJaZJtmZ0xL1qcbsywujgn4LLYw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)的取值范围为![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIUNOBeJ8FvR2IZ2a46bibuqoNicicPpZGfwAVGv6SBkShBBI10KiaH3OtlA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)。如果是下面的那条直线，则为![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIEkOzSgIKLUULDYTly7iaDZHsTQvxEG90CCbWMsrhibKO64aV91KwDr8A/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)。

对于这两种情况![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIvMFcbRJzZt4EcqOrOZoWtXt7anicMwW7v8D2VUB1R7Dg9UZIrz82cUA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)的下界和上界可以统一写成如下形式：

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIxH5xemgkTLXFPiaYSZKK4qOGsHwbl4XCVxV2mibhnJibQ7ZpiaEtKnGHMw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIL7ZJUA6jTopMLgmu0wamwveaibq9IGtuNVYpvHicyO8cVhgnzxvNiaCHw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

下边界是直线和x轴交点的x坐标以及0的较大值；上边界是直线和的交点的x坐标和C的较小值。



再来看第二种情况。如果![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIQUmBXkkSNRyuibvoGgb5kKvJiaGlDFWNXTdh56dyXpibGGToRCSQ2mM9w/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)同号，等式约束为![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIFwTLdliaZ8PTI9dX17EgNqmQ8ImRD6dPLibPd7DELib1Tye8AAlnngBEw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

此时的下界和上界为：

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIFIgqgx5r9MDOqfJ0XjXSmx1KxhcOqSTcvtUdRZZ0vxoy0olaStQ3Tg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIrR5nZ9pfqqIsX9gZQ1NLKXvGibgNhmKMNnN79guibpXibHrMuL2ibLsiadg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

这种情况如下图所示：



![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaI4faGsMiaPdjniayv7YPjlqmjs5UYTdhvxpjNmDgd5bGKEh9r6iaulENcg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

可行域示意图-情况2



利用这两个变量的等式约束条件，可以消掉![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIZd0VIw9Xn0YoAL1U0lb4ib2zCzQ8d0j2m4Z5DAaCMXryYlmSn2BxvNg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)，只剩下一个变量![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIByne5McD6lkj4JY9TAPNoJicrKHEVwicHRMxO0cwvqwWxWXfDoSqvLyg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)，目标函数是它的二次函数。我们可以直接求得这个二次函数的极值，假设不考虑约束条件得到的极值点为，则最终的极值点为：

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIDXT5S2s9sWuESXGXINZ9tqPu6r6zuc45HVOVvUF33Gvy381w85Dw1w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

这三种情况如下图所示：



![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIFTT1tfLibKbQ5Z4LS4PfqDgLjFuB8DLNZmKVUHBI4ZNibo24ib0XicRxSg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

3种情况下的二次函数极小值



上图中第一种情况是抛物线的最小值点在[L,H]中；第二种情况是抛物线的最小值点大于H，被截断为H；第三种情况是小于L，被截断为L。



下面我们来计算不考虑截断时的函数极值。为了避免分-1和+1两种情况，我们将上面的等式约束两边同乘以![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIRN9AWoU6mLeHGNia0Uxj0axJds0icdf8CTU5icCydLlVdfpGPgfQlCE6w/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)有：

![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaI1ibQCmwVhPS4mMNGg5wpao4ABPVTFz6V6x2BicjPia1YtNn7zSNcahzHQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

变形后得到：

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIt0PQVOp0EXp8rkRFP2My7cvrNuEHkSoPlAmwqdL5b51IJyyib1MQVQg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



为了表述简介，令![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaINsMibDZ2dak65kN8m6108ocH8bkPbJ4X1bEvicl6urBSeiajMnFzIDmHQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)，将上面方程代入目标函数中消掉![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaITyUWqrb26GCIehP7cVnL3icaH1vbZ8cxQicRNsKJWaFtt6OuicKjH1icrA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)，有：



![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIpq5BR1Sibu0vhJcUzoQbT9Je9voBCsFO27nx9mia7RDa2icib2FSubz2Bg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



对自变量求导并令导数为0，得：



![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIsvRWnJJOkwib2WdDMFbQVBPpDV1lT2Q06O3icRY8J9MzXUYfP826UYVQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



而

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIgfkVahPD4SvoDIaS95onH08Mdw2kjUly5BzXiaAiabte8I89mDORD61w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

化简得：

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIB41X6E5opiblqralqib1zaGsicsR59mDxCl8YibNhmgKGG5D2OArVzHJaQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



即：

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIc4GRUZ6Znv71jlMmF3f0Sic0gAx2ib9HicvelSycYTicvAxpNjicKP2624g/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

将w和v带入，化简得：



![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIG68PIibhQUsN3HNFd5veiaRK5SSHpN5yqJFFFkMjHmJ6vWJE9C3moVIg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



如果令

![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIyCI1lQygZH2ibPegibkk0HzuoibNtBj0bTnYbq7SXv1gY4ic093sFibmGiaw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

上式两边同时除以![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIpVxcUrat2ic0VrGw5LOWMguR0jTOqcN7Nm2FHSSzsqLwUbKY4Kiaytqg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)，可以得到

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIdyhNhNGibmSW5KrsMrf7Ewej2faBN1Y1XUv0BbicGiaAEIYZHuibu4IFsg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

其中![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIzicf2IVHf07qflfh17TCuw6DqJAVwVgv3X7BmPMgjRsYfv99RkbSkpA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)，考虑前面提推导过的约束：



![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIdyhNhNGibmSW5KrsMrf7Ewej2faBN1Y1XUv0BbicGiaAEIYZHuibu4IFsg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



在求得![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaI3r8Eh9oInFxyyoiaRKxPp9glgnmca2DUoGkN1F2zCa2mcrRLYGXeqXg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)之后，根据等式约束条件我们就可以求得另外一个变量的值：



![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIr9SyuwGicLx44kgRjq46TYt77mibAOBicicNRj2lvDyknUibVpuvBtJiaFicQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



目标函数的二阶导数为![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIpVxcUrat2ic0VrGw5LOWMguR0jTOqcN7Nm2FHSSzsqLwUbKY4Kiaytqg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)，前面假设二阶导数![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIx6n2zfOrLahZwfX76L5b3JjUqmRG2TvrtX7RRmiatv3m2k841QhHicAw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)，从而保证目标函数是凸函数即开口向上的抛物线，有极小值。如果![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaISNOvN9F8aKDITha4gAOehicQRtlhVdAricvTDfjKp3oniaHqjx4nLShqA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)或者![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIohe0CH23xTOu1ZVVEajRgPWgpfesURWOib6214dVFMRiaNwMo1e9icK3g/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)，该怎么处理？对于线性核或正定核函数，可以证明矩阵K的任意一个上述子问题对应的二阶子矩阵半正定，因此必定有![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIXyMrTT0UfpA5orrvoKs9gLB5xpVY8TzbW12SWiccb991mxCSQ1eceXg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)，[SIGAI](http://mp.weixin.qq.com/s?__biz=MzU4MjQ3MDkwNA==&mid=2247483731&idx=1&sn=237c52bc9ddfe65779b73ef8b5507f3c&chksm=fdb69cc4cac115d2ca505e0deb975960a792a0106a5314ffe3052f8e02a75c9fef458fd3aca2&scene=21#wechat_redirect)公众号后面的文章中我们会给出证明。无论本次迭代时![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaILRS4PLZb6C0Fy12lVbNtM7EklaTtfAQZ67CibopOJtrRIf9LmsabRfw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)的初始值是多少，通过上面的子问题求解算法得到是在可行域里的最小值，因此每次求解更新这两个变量的值之后，都能保证目标函数值小于或者等于初始值，即函数值下降。



上面已经解决了两个变量问题的求解，接下来说明怎么选择这两个变量，最简单的是使用启发式规则。第一个变量的选择方法是在训练样本中选取违反KKT条件最严重的那个样本。在前面我们推导过，在最优点处训练样本是否满足KKT条件的判据是：

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIm8w6645GoqoGl49XsX10sZm3ibibyPzvGIoqlicNzvRpe1XNtkqW0rlSQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIv5KH48eXcla8CPVEOMHHiaGswqfHPVnONQAZlhxiaPsLyicxHEayCGCbg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIjiawNqmxG7W58StPgMurCI7aHica62wq2GTtjOwesia1jwAF9X77AQCnA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

其中

![img](https://mmbiz.qpic.cn/mmbiz_png/75DkJnThAClHZ0YYR7plr2zxysvxPBaIKClerw8icyncib0VbUjCsDcPSfvhBpGaoGpytv0icstVIj6A9XHvkn7bg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

首先遍历所有满足约束条件![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIMZ60hh4g3LYB1ueCowIYmhibHhN4xxsjsgkrX4NnIEXRPX59rnIh1mg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)的样本点，即位于间隔边界上的支持向量点，检验它们是否满足KKT条件。如果这些样本都满足KKT条件，则遍历整个训练样本集，判断它们是否满足KKT条件，直到找到一个违反KKT条件的变量![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIiaZKhUDbyq0iaDODo0MRj3Lvb4Vpnu4SNMojMR1OOlotIYN1ZicYTo7QA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)，找到了第一个变量之后，接下来寻找![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIAUzeqj7485N1Noz7XNmfegwyG4bJLJxyjL1DrXbSBBlGicg414dhLdA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)，选择的标准是使得它有足够大的变化。根据前面的推导我们知道![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIsRhwtGpbREYyyWMlLEPG5sbiapAkmyGf7URvpBHdTqdCA0k3oYonJgA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)依赖于![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIg7WlG7iaNrvcjca5ibTiaibEKicNmgmvsoKbdqPWTDI1pYibFNtDwfpeZsfg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)，因此，我们选择使得![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIu8V9MhP8GJia2YvAic6JWZfu89W0Fn6Xh2ox6Jw5LaibVmRia0Pd09dgNg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)最大的![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIU8NQDVgBvvw1wRAu5mmsoVVq1MVXala0UIN8V6K7muFVSeXwcEE7fA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)。由于![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIqUVCyJktUakEJn3MHic5giak1VDGEOyPBqKpnIr4ibTxm8eNX096Wericg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)已经选出来了，因此![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaI52CVLbLiasmupFmINg4Jr6K9d2Y9z2rkQD01OrfCicib5H9XVf9PZTA4A/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)已经知道了。如果![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIhciatcdcNIiaFwGWOmia6pT2iageic9qOKvO7TzxxHahy54W2ooNkn9iaggw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)则选择最小的![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIzLmUBs1osUIF3yC9d0nHdsZ90DT2h3CxHibwUzmtdX6Hiava2LDcNqIA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)，否则选择最大的![img](https://mmbiz.qpic.cn/mmbiz_jpg/75DkJnThAClHZ0YYR7plr2zxysvxPBaIzLmUBs1osUIF3yC9d0nHdsZ90DT2h3CxHibwUzmtdX6Hiava2LDcNqIA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)。



至此，我们给出了支持向量机求解的问题的完整推导过程，通过这张图，你将能更容易地理解这个算法，如果在理解的过程中有任何疑问，可以向[SIGAI](http://mp.weixin.qq.com/s?__biz=MzU4MjQ3MDkwNA==&mid=2247483731&idx=1&sn=237c52bc9ddfe65779b73ef8b5507f3c&chksm=fdb69cc4cac115d2ca505e0deb975960a792a0106a5314ffe3052f8e02a75c9fef458fd3aca2&scene=21#wechat_redirect)公众号发消息，我们将为你解答。



# 相关

- [用一张图理解SVM的脉络](https://mp.weixin.qq.com/s?__biz=MzU4MjQ3MDkwNA==&mid=2247483937&idx=1&sn=84a5acf12e96727b13fd7d456c414c12&chksm=fdb69fb6cac116a02dc68d948958ee731a4ae2b6c3d81196822b665224d9dab21d0f2fccb329&mpshare=1&scene=1&srcid=04304gfwc9hVDJQUgAsmR3di#rd)
