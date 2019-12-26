---
title: 02 创建 Tensor
toc: true
date: 2019-12-06
---
# 创建 Tensor


## 指定维度

举例：

```py
import torch as T

t1 = T.Tensor(2, 3, dtype=T.long)
t2 = T.ones(2, 3)
t3 = T.zeros(2, 3)
t4 = T.eye(2, 3)
print(t1)
print(t2)
print(t3)
print(t4)
```

输出：

```
tensor([[0, 0, 0],
        [0, 0, 0]])
tensor([[1., 1., 1.],
        [1., 1., 1.]])
tensor([[0., 0., 0.],
        [0., 0., 0.]])
tensor([[1., 0., 0.],
        [0., 1., 0.]])
```

说明：

- 可以使用 `dtype=T.long` 来指定数据类型。

注意：

- eye() 的参数仅包括 1-2个 int。


## 创建与现有 Tensor 相同 size 的 Tensor

举例：

```py
import torch as T

t = T.Tensor(2, 3)

t1 = T.Tensor(t.size())
t2 = T.ones(t.size())
t3 = T.ones_like(t)
t4 = T.zeros(t.size())
t5 = T.zeros_like(t)

print(t)
print(t1)
print(t2)
print(t3)
print(t4)
print(t5)
```

输出：

```
tensor([[0., 0., 0.],
        [0., 0., 0.]])
tensor([[0., 0., 0.],
        [0., 0., 0.]])
tensor([[1., 1., 1.],
        [1., 1., 1.]])
tensor([[1., 1., 1.],
        [1., 1., 1.]])
tensor([[0., 0., 0.],
        [0., 0., 0.]])
tensor([[0., 0., 0.],
        [0., 0., 0.]])
```


## 创建空 Tensor 和全值 Tensor

```py
import torch as T

t = T.rand(2, 1)

t1 = T.empty(t.size())
t2 = T.empty_like(t)
t3 = T.full(t.size(), 1)
t4 = T.full_like(t, 1)

print(t1)
print(t2)
print(t3)
print(t4)
```

输出：

```
tensor([[4.2039e-45],
        [0.0000e+00]])
tensor([[4.2039e-45],
        [0.0000e+00]])
tensor([[1.],
        [1.]])
tensor([[1.],
        [1.]])
```


## 使用均分区间生成 Tensor

```py
import torch as T

t1 = T.arange(1, 9, 2)
t2 = T.linspace(1, 9, 4)

print(t1)
print(t2)
```

输出：

```
tensor([1, 3, 5, 7])
tensor([1.0000, 3.6667, 6.3333, 9.0000])
```

说明：

- `T.arange(1, 9, 2)` ：`[m,n)`中 `m` 开始以步长 `step_length` 生成
- `T.linspace(1, 9,4)`：`[m,n]`中以 `m` 为首项，`n`为末项，均分区间为 `step_num`段


注意：

- `T.range()` 已经取消了，现在使用 `T.arange()`。

## 随机化生成


```py
import torch as T

t1 = T.rand(2, 3)  # 均匀分布
t2 = T.randn(2, 3)  # 标准正态分布
t3 = T.normal(t1, 0.0001)

print(t1)
print(t2)
print(t3)
```

输出：

```
tensor([[0.9452, 0.4288, 0.3420],
        [0.1350, 0.7723, 0.2293]])
tensor([[ 1.9312, -0.1604,  1.1843],
        [ 0.5186, -0.7506, -0.4517]])
tensor([[0.9453, 0.4288, 0.3422],
        [0.1351, 0.7722, 0.2295]])
```

说明：

- `T.normal(t1, 0.0001)` 是在 t1 的基础上进行以标准差为 1 的正态采样。





# 相关

- [PyTorch中的 Tensor](https://blog.csdn.net/tfcy694/article/details/80330616)
