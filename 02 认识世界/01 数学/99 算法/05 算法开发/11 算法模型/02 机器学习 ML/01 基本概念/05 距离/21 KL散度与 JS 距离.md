---
title: 21 KL散度与 JS 距离
toc: true
date: 2019-08-28
---
# 可以补充进来的

- 为什么式子是这样的？

# KL 散度


KL散度

Kullback–Leibler divergence，简称 KLD

KL散度在信息系统中称为相对熵（relative entropy），在连续时间序列中称为 randomness，在统计模型推断中称为信息增益（information gain）。也称信息散度（information divergence）。

## 说明

KL散度是两个概率分布 P 和 Q 差别的非对称性的度量。 KL散度是用来度量使用基于 Q 的分布来编码服从 P 的分布的样本所需的额外的平均比特数。典型情况下，P表示数据的真实分布，Q表示数据的理论分布、估计的模型分布、或 P 的近似分布。


$$
D_{K L}(P \| Q)=-\sum_{x \in X} P(x) \log \frac{1}{P(x)}+\sum_{x \in X} P(x) \log \frac{1}{Q(x)}=\sum_{x \in X} P(x) \log \frac{P(x)}{Q(x)}
$$

## 定义


对于离散随机变量，其概率分布 P 和 Q的 KL 散度可按下式定义为

$$
D_{\mathrm{KL}}(P \| Q)=-\sum_{i} P(i) \ln \frac{Q(i)}{P(i)}
$$

等价于

$$
D_{\mathrm{KL}}(P \| Q)=\sum_{i} P(i) \ln \frac{P(i)}{Q(i)}
$$

即按概率 $P$ 求得的 $P$ 和 $Q$ 的对数商的平均值。KL散度仅当概率 P 和 Q 各自总和均为 1，且对于任何 i 皆满足 $Q(i)>0$ 及 $P(i)>0$ 时，才有定义。式中出现 $0\ln 0$ 的情况，其值按 0 处理。

对于连续随机变量，其概率分布 P 和 Q 可按积分方式定义为：

$$
D_{\mathrm{KL}}(P \| Q)=\int_{-\infty}^{\infty} p(x) \ln \frac{p(x)}{q(x)} \mathrm{d} x
$$

其中 p 和 q 分别表示分布 P 和 Q 的密度。

更一般的，若 P 和 Q 为集合 X 的概率测度，且 P 关于 Q 绝对连续，则从 P 到 Q 的 KL 散度定义为

$$
D_{\mathrm{KL}}(P \| Q)=\int_{X} \ln \frac{\mathrm{d} P}{\mathrm{d} Q} \mathrm{d} P
$$

其中，假定右侧的表达形式存在，则 $\frac{\mathrm{d} Q}{\mathrm{d} P}$ 为 Q 关于 P 的 R–N导数。

相应的，若 P 关于 Q 绝对连续，则

$$
D_{\mathrm{KL}}(P \| Q)=\int_{X} \ln \frac{\mathrm{d} P}{\mathrm{d} Q} \mathrm{d} P=\int_{X} \frac{\mathrm{d} P}{\mathrm{d} Q} \ln \frac{\mathrm{d} P}{\mathrm{d} Q} \mathrm{d} Q
$$

即为 P 关于 Q 的相对熵。


## 特性

因为对数函数是凸函数，所以 KL 散度的值为非负数。

$$
D_{\mathrm{KL}}(P \| Q) \geq 0
$$

由吉布斯不等式可知，当且仅当 P = Q时 $D_{\mathrm{kL}}(A | Q)$ 为零。

## 不是真正的距离

有时会将 KL 散度称为 KL 距离，但它并不满足距离的性质。

因为 KL 散度不具有对称性：从分布 P 到 Q 的距离通常并不等于从 Q 到 P 的距离。

$$
D_{\mathrm{KL}}(P \| Q) \neq D_{\mathrm{KL}}(Q \| P)
$$


KL散度也不满足三角不等式。


# 相关

- wiki
KL散度和 JS 距离是迁移学习中被广泛应用的度量手段。
