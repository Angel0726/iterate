---
title: 08 GPU 加速运算
toc: true
date: 2019-06-12
---
# 可以补充进来的

- 重新按照新的书补充下，这本有点过时了。

# GPU加速运算

该包增加了对 CUDA 张量类型的支持，实现了与 CPU 张量相同的功能，但使用 GPU 进行计算。


返回当前所选设备的索引：

```py
Torch.cuda.current_device()
```

更改所选设备：

```py
torch.cuda.device(idx)
```

参数说明如下。

- idx(int)：设备索引选择。如果这个参数是负的，则是无效操作。

例子如下。

```py
import torch
import torch.nn as nn
import torch.optim as optim
from torch.autograd import Variable
import torchvision



x = torch.cuda.FloatTensor(1)
y = torch.FloatTensor(1).cuda()

with torch.cuda.device(1):
    a = torch.cuda.FloatTensor(1)
    b = torch.FloatTensor(1).cuda()
    c = a + b
    z = x + y
    d = torch.randn(2).cuda(2)
```

<span style="color:red;">这个例子没试，感觉这本书可能比较老了，还是要看最新的基于 1.1.0 的一些书。重新补充下。</span>




# 相关

- 《深度学习框架 Pytorch 快速开发与实战》
