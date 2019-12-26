---
title: 22 Tensor 与 numpy 转换
toc: true
date: 2019-12-06
---
# 可以补充进来的

- 不够，且不够理解

# Tensor 与 numpy 的转换

举例：

```py
import torch as T
import numpy as np

x = np.ones(5)
y1 = T.Tensor(x)
y2 = T.from_numpy(x)
print(x)
print(y1)
print(y2)
np.add(x, 1, out=x)
print(x)
print(y1)
print(y2)

x1 = y1.numpy()
x2 = y2.numpy()
print(x1)
print(x2)
```

输出：

```
[1. 1. 1. 1. 1.]
tensor([1., 1., 1., 1., 1.])
tensor([1., 1., 1., 1., 1.], dtype=torch.float64)
[2. 2. 2. 2. 2.]
tensor([1., 1., 1., 1., 1.])
tensor([2., 2., 2., 2., 2.], dtype=torch.float64)
[1. 1. 1. 1. 1.]
[2. 2. 2. 2. 2.]
```

注意：

- `T.Tensor(x)` 与 `T.from_numpy(x)` 两种方式转化的 Tensor 是不同的，其中 `from_numpy` 得到的 y 实际上是对 x 的一个引用，所以，当 x 修改了，y 也会修改。
- **CharTensor 类型不支持到 NumPy 的转换。**

使用说明：

- 使用 `T.from_numpy(x)` 来转换时，Tensor 和 np.array 共享内存，**所以他们之间的转换很快，而且几乎不会消耗什么资源。但这也意味着，如果其中一个变了，另外一个也会随之改变。所以有些 PyTorch 没有但 numpy 有的操作可以先转化为 numpy 进行操作再转化回来。代价可以忽略不计。**






# 相关

- 《深度学习框架 Pytorch 快速开发与实战》
- [PyTorch中的 Tensor](https://blog.csdn.net/tfcy694/article/details/80330616)
