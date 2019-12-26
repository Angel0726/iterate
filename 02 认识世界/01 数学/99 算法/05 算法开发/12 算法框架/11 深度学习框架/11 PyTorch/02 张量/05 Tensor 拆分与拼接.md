---
title: 05 Tensor 拆分与拼接
toc: true
date: 2019-12-06
---
# Tensor 拆分与拼接

举例：

```py
import torch as T

t = T.Tensor([[1, 2, 3], [2, 2, 5]])
print(t)
print(T.cat((t, t), 1))  # 按第 di 的维度将 tuple 里面的 tensor 进行拼接
print(T.chunk(t, 3, 0))  # 在 di 维上将 t 分成 i 份，最后一份的维度不定（若不能整除）
```

输出：

```
tensor([[1., 2., 3.],
        [2., 2., 5.]])
tensor([[1., 2., 3., 1., 2., 3.],
        [2., 2., 5., 2., 2., 5.]])
(tensor([[1., 2., 3.]]), tensor([[2., 2., 5.]]))
```

说明：

- `T.cat((t,t,...),di)` ：按第 di 的维度将 tuple 里面的 tensor 进行拼接
- `T.chunk(t,i,di)` ：在 di 维上将 t 分成 i 份，最后一份的维度不定（若不能整除）






# 相关

- [PyTorch中的 Tensor](https://blog.csdn.net/tfcy694/article/details/80330616)
