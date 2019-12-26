---
title: 06 提取 Tensor 中的部分数据
toc: true
date: 2019-12-06
---
# 提取 Tensor 中的部分数据

举例：

```py
import torch as T
import numpy as np

t = T.Tensor([[1, 0, 0, 2, 3, 0], [4, 5, 0, 0, 2, 6]])
indices=T.LongTensor(range(1,5,2))
mask = T.ByteTensor(np.eye(2, 6))

print(t)
print(indices)
print(mask)

print('\n')

print(t[:, 1]) # 可以使用 NumPy 类似的索引操作

print(T.index_select(t, 1, indices))  # 在第 di 维上将 t 的 indices 抽取出来组成新 Tensor。
print(T.masked_select(t, mask))  # 按照 0-1 Tensor mask的格式筛选 t，返回一维 Tensor
print(T.nonzero(t))  # 输出 n×2维 Tensor，非零元素的 index

# 获取单个元素
x = T.Tensor([[1]])
print(x)
print(x.item())
```

输出：

```
tensor([[1., 0., 0., 2., 3., 0.],
        [4., 5., 0., 0., 2., 6.]])
tensor([1, 3])
tensor([[1, 0, 0, 0, 0, 0],
        [0, 1, 0, 0, 0, 0]], dtype=torch.uint8)

tensor([0., 5.])
tensor([[0., 2.],
        [5., 0.]])
tensor([1., 5.])
tensor([[0, 0],
        [0, 3],
        [0, 4],
        [1, 0],
        [1, 1],
        [1, 4],
        [1, 5]])
tensor([[1.]])
1.0
```

说明：

- `T.index_select(t, di, indices)` ：在第 di 维上将 t 的 indices 抽取出来组成新 Tensor。
- `T.masked_select(t, mask)`：按照 0-1Tensor mask的格式筛选 t，返回一维 Tensor
- `T.nonzero(t)`：输出 n×2维 Tensor，非零元素的 index
- `print(t[:, 1])`：可以使用 NumPy 类似的索引操作
- `x.item()`：如果你有一个元素 tensor ，使用 `.item()` 来获得这个 value 。


注意：

- `x.item()` 只有当x 是一个元素时才可以使用。

# 相关

- [PyTorch中的 Tensor](https://blog.csdn.net/tfcy694/article/details/80330616)
