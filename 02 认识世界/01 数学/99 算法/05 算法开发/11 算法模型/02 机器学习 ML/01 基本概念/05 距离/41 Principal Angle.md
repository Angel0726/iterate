---
title: 41 Principal Angle
toc: true
date: 2019-08-28
---
# 可以补充进来的

- 相关资料没查到。

# Principal Angle


也是将两个分布映射到高维空间(格拉斯曼流形)中，在流形中两堆数据就可以看成两个点。Principal angle是求这两堆数据的对应维度的夹角之和。

对于两个矩阵 $\mathbf{X},\mathbf{Y}$ ，计算方法：首先正交化(用 PCA)两个矩阵，然后：


$$
PA(\mathbf{X},\mathbf{Y})=\sum_{i=1}^{\min(m,n)} \sin \theta_i
$$

其中 $m,n$ 分别是两个矩阵的维度， $\theta_i$ 是两个矩阵第 $i$ 个维度的夹角， $\Theta=\{\theta_1,\theta_2,\cdots,\theta_t\}$ 是两个矩阵 SVD 后的角度：

$$
\mathbf{X}^\top\mathbf{Y}=\mathbf{U} (\cos \Theta) \mathbf{V}^\top
$$







# 相关

- [迁移学习简明手册](https://github.com/jindongwang/transferlearning-tutorial)  [王晋东](https://zhuanlan.zhihu.com/p/35352154)
