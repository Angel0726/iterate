---
title: 04 Tensor 维度转换
toc: true
date: 2019-12-06
---
# 可以补充进来的

- 不够，补充一些实际例子


# Tensor 维度转换

举例：

```py
import torch as T

t = T.Tensor([[1,2,3],[2,2,5]])
print(t)
print(t.view(3, 2))  # 维度重整
print(t.view(-1,1)) # the size -1 is inferred from other dimensions

print(t.unsqueeze(0))  # 在 di 个维度处升维、
print(t.squeeze(0))  # 若 di 维是 1，压缩，否则不变。若无参数，压缩所有“1”维
```

输出：

```
tensor([[1., 2., 3.],
        [2., 2., 5.]])
tensor([[1., 2.],
        [3., 2.],
        [2., 5.]])
tensor([[1.],
        [2.],
        [3.],
        [2.],
        [2.],
        [5.]])
tensor([[[1., 2., 3.],
         [2., 2., 5.]]])
tensor([[1., 2., 3.],
        [2., 2., 5.]])
```

说明：

- `t.view(d1,d2,d3....)` 维度重整
- `t.unsqueeze(di)` 在 di 个维度处升维、
- `t.squeeze(di)` 若 di 维是 1，压缩，否则不变。若无参数，压缩所有“1”维

## 对于 squeeze 补充例子

举例：

```py
import torch as T

x = T.zeros(2, 1, 1)
print(x)
print(x.size())

y = T.squeeze(x)
print(y)
print(y.size())

y = T.squeeze(x, 0)
print(y)
print(y.size())

y = T.squeeze(x, 1)
print(y)
print(y.size())
```

输出：

```
tensor([[[0.]],
        [[0.]]])
torch.Size([2, 1, 1])
tensor([0., 0.])
torch.Size([2])
tensor([[[0.]],
        [[0.]]])
torch.Size([2, 1, 1])
tensor([[0.],
        [0.]])
torch.Size([2, 1])
```

可见：

- 当指定的维度是 1 时，是可以被压缩的。否则是不能压缩的。


# 相关

- 《深度学习框架 Pytorch 快速开发与实战》
