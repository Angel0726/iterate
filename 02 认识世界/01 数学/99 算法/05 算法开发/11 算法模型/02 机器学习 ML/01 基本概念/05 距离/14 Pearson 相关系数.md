---
title: 14 Pearson 相关系数
toc: true
date: 2019-08-28
---


# 皮尔逊相关系数


皮尔逊积矩相关系数


Pearson product-moment correlation coefficient

PPMCC 或 PCCs

文章中常用 r 或 Pearson's r表示

用于度量两个变量 X 和 Y 之间的相关程度（**线性相关**），其值介于-1与 1 之间。

Pearson相关系数只能衡量线性相关性，但无法衡量非线性关系。如 y=x^2，x和 y 有很强的非线性关系。

## 定义


两个变量之间的皮尔逊相关系数定义为两个变量之间的协方差和标准差的商：

$$
\rho_{X, Y}=\frac{\operatorname{cov}(X, Y)}{\sigma_{X} \sigma_{Y}}=\frac{E\left[\left(X-\mu_{X}\right)\left(Y-\mu_{Y}\right)\right]}{\sigma_{X} \sigma_{Y}}
$$

上式定义了总体相关系数，常用希腊小写字母 ρ (rho) 作为代表符号。


估算样本的协方差和标准差，可得到样本相关系数(样本皮尔逊系数)，常用英文小写字母 r 代表：

$$
r=\frac{\sum_{i=1}^{n}\left(X_{i}-\overline{X}\right)\left(Y_{i}-\overline{Y}\right)}{\sqrt{\sum_{i=1}^{n}\left(X_{i}-\overline{X}\right)^{2}} \sqrt{\sum_{i=1}^{n}\left(Y_{i}-\overline{Y}\right)^{2}}}
$$


r 亦可由 $\left(X_{i}, Y_{i}\right)$ 样本点的标准分数均值估算，得到与上式等价的表达式：

$$
r=\frac{1}{n-1} \sum_{i=1}^{n}\left(\frac{X_{i}-\overline{X}}{\sigma_{X}}\right)\left(\frac{Y_{i}-\overline{Y}}{\sigma_{Y}}\right)
$$

其中 $\frac{X_{i}-\overline{X}}{\sigma_{X}}$、 $\overline{X}$ 及 $\sigma_{X}$ 分别是 $X_{i}$ 样本的标准分数、样本平均值和样本标准差。




范围： $[-1,1]$ ，绝对值越大表示（正/负）相关性越大。







# 相关

- wiki

