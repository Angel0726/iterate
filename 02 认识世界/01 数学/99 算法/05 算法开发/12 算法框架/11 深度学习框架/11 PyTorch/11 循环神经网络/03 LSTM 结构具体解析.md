---
title: 03 LSTM 结构具体解析
toc: true
date: 2019-06-16
---
# 可以补充进来的

- 合并到主框架里面。


# LSTM 结构具体解析

我们先理解门（gates）的结构。

- 输入门 $i_t$：控制有多少信息可以流入记忆细胞。
- 遗忘门 $f_t$：控制有多少上一时刻的记忆细胞中的信息可以累积到当前时刻的记忆细胞中。
- 输出门 $o_t$：控制有多少当前时刻的记忆细胞中的信息可以流入当前隐藏状态 $h_t$ 中。


## LSTM 的遗忘门

<center>

![](http://images.iterate.site/blog/image/20190616/8Pzsh7S3LnwT.png?imageslim){ width=55% }

</center>


LSTM 的关键就是细胞核的状态。细胞核的状态类似于一种传送带。它直接在整个链上穿过，附带一些少量的线性交互，让信息在上面流传而保持不变。

遗忘是通过一种叫作门（gates）的结构，让信息有选择性地通过。它们是由一个 Sigmoid 神经网络层和一个 Pointwise 的乘法操作组成的。

遗忘门的内部结构：

<center>

![](http://images.iterate.site/blog/image/20190616/tzdYyXgg5slP.png?imageslim){ width=55% }

</center>

## 步骤

第一步：利用遗忘门层，决定从细胞状态中丢弃什么信息，衰减系数计算如图 8.10所示。读取 $h_{t-1}$ 和 $x_t$，输出一个在 $0$ 到 $1$ 之间的数值给每个在细胞状态 $C_{t-1}$ 中的数字。这决定我们会从细胞状态中丢弃什么信息。由于 Sigmoid 输出结果为 $0$ 到 $1$，所以用 $1$ 表示“完全保留”, $0$ 表示“完全舍弃”。


衰减系数计算过程：

<center>

![](http://images.iterate.site/blog/image/20190616/FfFGf6lYOVxf.png?imageslim){ width=55% }
</center>

$$
f_{t}=\sigma\left(W_{f}\left[h_{t-1}, x_{t}\right]+b_{f}\right)
$$

第二步：更新信息。首先，Sigmoid 层为“输入门层”，决定什么值我们将要更新。然后，tanh 层创建一个新的候选值向量。计算过程如图 8.11所示。

$t$ 时刻的记忆计算过程：

<center>

![](http://images.iterate.site/blog/image/20190616/Wt2h8AGXEtN0.png?imageslim){ width=55% }

</center>


$$
i_{t}=\sigma\left(W_{i}\left[h_{t-1}, x_{t}\right]+b_{i}\right)
$$


$$
\tilde{C}_{t}=\tanh \left(W_{C}\left[h_{t-1}, x_{t}\right]+b_{C}\right)
$$


第三步：更新旧细胞状态的时间，$C_{t-1}$ 更新为 $C_{t}$ 。计算过程如图 8.12所示。

$C_{t-1}$ 更新为 $C_{t}$ 过程：

<center>

![](http://images.iterate.site/blog/image/20190616/a1CjADOAkGrx.png?imageslim){ width=55% }
</center>

$$
C_{t}=f_{t} C_{t-1}+i_{t} \tilde{C}_{t}
$$


第四步：输出门，确定输出什么值。此时的输出是根据上述第三步的 $C_t$ 状态进行计算的。计算过程如图所示：

确定输出值：

<center>

![](http://images.iterate.site/blog/image/20190616/Mgv4LzHrDijY.png?imageslim){ width=55% }
</center>

$$
o_{t}=\sigma\left(W_{o}\left[h_{t-1}, x_{t}\right]+b_{o}\right)
$$


$$
h_{t}=o_{t} \tanh \left(C_{t}\right)
$$



## 汇总之后展开公式


$$
i_{t}=\operatorname{sigmoid}\left(W_{x i} x_{t}+W_{h i} h_{t-1}+b_{i}\right)
$$

$$
f_{t}=\operatorname{sigmoid}\left(W_{x f} x_{t}+W_{h f} h_{t-1}+b_{f}\right)
$$

$$
o_{t}=\operatorname{sigmoid}\left(W_{x o} x_{t}+W_{h o} h_{t-1}+b_{o}\right)
$$

$$
c_{t}=f_{t} c_{t-1}+i_{t} \tanh \left(W_{x c} x_{t}+W_{h c} h_{t-1}+b_{c}\right)
$$


$$
h_{t}=o_{t} \tanh \left(\mathrm{c}_{\mathrm{t}}\right)
$$





# 相关

- 《深度学习框架 Pytorch 快速开发与实战》
