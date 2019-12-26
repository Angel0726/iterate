---
title: 03 Tensor 属性查看
toc: true
date: 2019-12-06
---
# Tensor 属性查看

x = torch.randn(1)
print(x)
print(x.item())
Out:

tensor([ 0.9422])
0.9422121644020081

举例：

```py
import torch as T

t = T.Tensor([[1,2,3],[2,2,5]])
print(t)
print(t.size())  # 返回 size 类型
print(T.numel(t))  # 返回总元素个数


```

输出：

```
tensor([[1., 2., 3.],
        [2., 2., 5.]])
torch.Size([2, 3])
6

```

说明：

- `T.numel(t)` 返回总元素个数
