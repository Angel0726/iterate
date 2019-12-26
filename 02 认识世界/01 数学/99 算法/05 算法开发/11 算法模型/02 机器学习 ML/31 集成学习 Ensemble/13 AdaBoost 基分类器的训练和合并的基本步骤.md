---
title: 13 AdaBoost 基分类器的训练和合并的基本步骤
toc: true
date: 2019-08-27
---

# Adaboost 基分类器的训练和合并的基本步骤如下。

1. 确定基分类器：这里可以选取 ID3 决策树作为基分类器。事实上，任何分类模型都可以作为基分类器，但树形模型由于结构简单且较易产生随机性所以比较常用。<span style="color:red;">比较容易产生随机性。嗯，怎么理解呢？相比之下别的不容易产生随机性吗？</span>

2. 训练基分类器：假设训练集为 $\left\{x_{i}, y_{i}\right\}$ ，$i=1, \ldots \ldots, N$ ，其中 $y_i \in \{−1,1\}$ ，并且有 $T$ 个基分类器，则可以按照如下过程来训练基分类器。
    - 初始化采样分布 $D_{1}(i)=1 / N$ ；
    - 令 $t=1,2, \ldots , T$ 循环：
    - 从训练集中，按照 $D_{t}$ 分布，采样出子集 $S_{t}=\left\{x_{i}, y_{i}\right\}, i=1, \ldots, N_{t}$；
    - 用 $S_{t}$ 训练出基分类器 $h_{t}$；
    - 计算 $h_{t}$ 的错误率：$\varepsilon_{t}=\frac{\sum_{i=1}^{N_{t}} I\left[h_{t}\left(x_{i}\right) \neq y_{i}\right] D_{t}\left(x_{i}\right)}{N_{t}}$ ，其中 $I[]$ 为判别函数；<span style="color:red;">为什么这个错误率是这么计算的？嗯，看了看又有些道理。</span>
    - 计算基分类器 $h_{t}$ 权重 $a_{t}=\log \frac{\left(1-\varepsilon_{t}\right)}{\varepsilon_{t}}$；<span style="color:red;">为什么权重是这个？</span>
    - 设置下一次采样 $D_{t+1}=\left\{\begin{array}{l}{D_{t}(i)} 或者 {\frac{D_{t}(i)\left(1-\varepsilon_{t}\right)}{\varepsilon_{t}}} &, h_{t}\left(x_{i}\right) \neq y_{i} \\ {\frac{D_{t}(i) \varepsilon_{t}}{\left(1-\varepsilon_{t}\right)}} &, { h_{t}\left(x_{i}\right)=y_{i}}\end{array}\right.$ 并将它归一化为一个概率分布函数。<span style="color:red;">嗯，相当于 已经被正确分类的样本，再次取到的概率降低。</span>


3. 合并基分类器：给定一个未知样本 $z$，输出分类结果为加权投票的结果 $\operatorname{Sign}\left(\sum_{t=1}^{T} h_{t}(z) a_{t}\right)$ 。


从 Adaboost 的例子中我们可以明显地看到 Boosting 的思想，对分类正确的样本降低了权重，对分类错误的样本升高或者保持权重不变。在最后进行模型融合的过程中，也根据错误率对基分类器进行加权融合。错误率低的分类器拥有更大的“话语权”。<span style="color:red;">嗯，挺好的。</span>




# 相关

- 《百面机器学习》
