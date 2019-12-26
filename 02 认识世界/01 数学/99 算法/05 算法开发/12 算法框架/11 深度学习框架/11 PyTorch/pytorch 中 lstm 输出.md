---
title: pytorch 中 lstm 输出
toc: true
date: 2019-07-04
---
# 聊一聊 PyTorch 中 LSTM 的输出格式


基础知识忘记的看这篇博客 [http://colah.github.io/posts/2015-08-Understanding-LSTMs/](https://link.zhihu.com/?target=http%3A//colah.github.io/posts/2015-08-Understanding-LSTMs/)
先看看官方文档 [https://pytorch.org/docs/stable/nn.html#lstm](https://link.zhihu.com/?target=https%3A//pytorch.org/docs/stable/nn.html%23lstm)

下图是官方文档中给出的 LSTM 输出结构描述，初次查看时我的内心是这样的
经过一番奋勇搏斗，终于将其撕开，下面来跟大家聊一聊。

<center>

![](http://images.iterate.site/blog/image/20190704/THXKodBNlX7C.png?imageslim){ width=55% }

</center>


**先上结论：**

1. output保存了最后一层，每个 time step的输出 h，如果是双向 LSTM，每个 time step的输出 h = [h正向, h逆向] (同一个 time step的正向和逆向的 h 连接起来)。
2. h_n保存了每一层，最后一个 time step的输出 h，如果是双向 LSTM，单独保存前向和后向的最后一个 time step的输出 h。
3. c_n与 h_n一致，只是它保存的是 c 的值。


**下面单独分析三个输出：**

1. output是一个三维的张量，第一维表示序列长度，第二维表示一批的样本数(batch)，第三维是 hidden_size(隐藏层大小) * num_directions ，这里是我遇到的第一个不理解的地方，hidden_sizes由我们自己定义，num_directions这是个什么鬼？翻看源码才明白，先贴出代码，从代码中可以发现 num_directions根据是“否为双向”取值为 1 或 2。因此，我们可以知道，output第三个维度的尺寸根据是否为双向而变化，如果不是双向，第三个维度等于我们定义的隐藏层大小；如果是双向的，第三个维度的大小等于 2 倍的隐藏层大小。为什么使用 2 倍的隐藏层大小？因为它把每个 time step的前向和后向的输出连接起来了，后面会有一个实验，方便我们记忆。

<center>

![](http://images.iterate.site/blog/image/20190704/4rL51pJ19q1o.png?imageslim){ width=55% }

</center>

2. h_n是一个三维的张量，第一维是 num_layers*num_directions，num_layers是我们定义的神经网络的层数，num_directions在上面介绍过，取值为 1 或 2，表示是否为双向 LSTM。第二维表示一批的样本数量(batch)。第三维表示隐藏层的大小。第一个维度是 h_n难理解的地方。首先我们定义当前的 LSTM 为单向 LSTM，则第一维的大小是 num_layers，该维度表示第 n 层最后一个 time step的输出。如果是双向 LSTM，则第一维的大小是 2 * num_layers，此时，该维度依旧表示每一层最后一个 time step的输出，同时前向和后向的运算时最后一个 time step的输出用了一个该维度。

- 举个例子，我们定义一个 num_layers=3的双向 LSTM，h_n第一个维度的大小就等于 6 （2*3），h_n[0]表示第一层前向传播最后一个 time
  step的输出，h_n[1]表示第一层后向传播最后一个 time step的输出，h_n[2]表示第二层前向传播最后一个 time step的输出，h_n[3]表示第二层后向传播最后一个 time step的输出，h_n[4]和 h_n[5]分别表示第三层前向和后向传播时最后一个 time step的输出。

3. c_n与 h_n的结构一样，就不重复赘述了。


**给出一个样例图（画工太差，如有错误请指正），对比前面的例子自己分析下**

<center>

![](http://images.iterate.site/blog/image/20190704/4Khx1cd99BB7.png?imageslim){ width=55% }

</center>


**最后上一段代码结束战斗**

```py
import torch
import torch.nn as nn
```

定义一个两层双向的 LSTM，input size为 10，hidden size为 20。

随机生成一个输入样本，sequence length为 5，batch size为 3，input size与定义的网络一致，为 10。

手动初始化 h0 和 c0，两个结构一致(num_layers * 2, batch, hidden_size) = (4, 3, 20)。

如果不初始化，PyTorch默认初始化为全零的张量。

```py
bilstm = nn.LSTM(input_size=10, hidden_size=20, num_layers=2, bidirectional=True)
input = torch.randn(5, 3, 10)
h0 = torch.randn(4, 3, 20)
c0 = torch.randn(4, 3, 20)
output, (hn, cn) = bilstm(input, (h0, c0))
```

查看 output，hn，cn的维度

```py
print('output shape: ', output.shape)
print('hn shape: ', hn.shape)
print('cn shape: ', cn.shape)
输出：
output shape:  torch.Size([5, 3, 40])
hn shape:  torch.Size([4, 3, 20])
cn shape:  torch.Size([4, 3, 20])
```

根据一开始结论，我们来验证下。

1.前向传播时，output中最后一个 time step的前 20 个与 hn 最后一层前向传播的输出应该一致。

```py
output[4, 0, :20] == hn[2, 0]
输出：
tensor([ 1,  1,  1,  1,  1,  1,  1,  1,  1,  1,  1,  1,  1,  1,
         1,  1,  1,  1,  1,  1], dtype=torch.uint8)
```

2.后向传播时，output中最后一个 time step的后 20 个与 hn 最后一层后向传播的输出应该一致。

```py
output[0, 0, 20:] == hn[3, 0]
输出：
tensor([ 1,  1,  1,  1,  1,  1,  1,  1,  1,  1,  1,  1,  1,  1,
         1,  1,  1,  1,  1,  1], dtype=torch.uint8)
```




# 相关

- [聊一聊 PyTorch 中 LSTM 的输出格式](https://zhuanlan.zhihu.com/p/39191116)
