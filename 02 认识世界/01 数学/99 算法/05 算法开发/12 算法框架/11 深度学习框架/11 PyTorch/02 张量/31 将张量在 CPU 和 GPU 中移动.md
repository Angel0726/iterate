---
title: 31 将张量在 CPU 和 GPU 中移动
toc: true
date: 2019-12-06
---
# 将张量在 CPU 和 GPU 中移动

举例：

```py
import torch as T


x = T.Tensor(2, 3)
if T.cuda.is_available():
    device = T.device("cuda")  # 一个 CUDA 设备对象
    y = T.ones_like(x, device=device)  # 直接在 GPU 中创建一个张量
    x = x.to(device)  # 把在 CPU 中创建的 x 移动到 cuda 中
    z = x + y
    print(z)
    print(z.to("cpu", T.double))  # 移动回 CPU 中。
```

输出：

```
tensor([[1., 1., 1.],
        [1., 1., 1.]], device='cuda:0')
tensor([[1., 1., 1.],
        [1., 1., 1.]], dtype=torch.float64)
```

说明：

- `is_available` 函数判断是否有 cuda 可以使用
- `x.to(device)` 将张量移动到指定的设备中。


不清楚的：

- 想知道 这种移动的消耗大吗？比如一个很大的张量，这种频繁的移动的话会不会比较消耗时间。



# 相关


- [pytorch-handbook](https://github.com/zergtant/pytorch-handbook)
