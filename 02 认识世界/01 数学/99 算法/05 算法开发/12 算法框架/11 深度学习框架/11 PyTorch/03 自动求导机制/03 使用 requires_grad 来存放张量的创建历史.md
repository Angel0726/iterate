---
title: 03 使用 requires_grad 来存放张量的创建历史
toc: true
date: 2019-12-06
---
# 可以补充进来的

- 想知道 `grad_fn` 里面的详细内容。到底是什么信息？比如执行了两步，那么第一步会存放吗？



# 使用 `requires_grad` 来存放张量的创建历史

我们可以通过设置 `requires_grad=True` 把操作存放在张量的 `grad_fn` 属性里。

## 在创建张量时设置 `requires_grad=True`

举例：


```py
import torch

x = torch.ones(2, 2, requires_grad=True)
y = torch.ones(2, 2)
print(x)
print(y)

print('\n')

m = x + 2
n = y + 2
print(m)
print(m.grad_fn)
print(n)
print(n.grad_fn)

print('\n')

z = x * x * 3
out = z.mean()
print(z)
print(out)
z = y * y * 3
out = z.mean()
print(z)
print(out)
```

输出：

```
tensor([[1., 1.],
        [1., 1.]], requires_grad=True)
tensor([[1., 1.],
        [1., 1.]])


tensor([[3., 3.],
        [3., 3.]], grad_fn=<AddBackward0>)
<AddBackward0 object at 0x000001DC64960408>
tensor([[3., 3.],
        [3., 3.]])
None


tensor([[3., 3.],
        [3., 3.]], grad_fn=<MulBackward0>)
tensor(3., grad_fn=<MeanBackward0>)
tensor([[3., 3.],
        [3., 3.]])
tensor(3.)
```

可见：

- 设定 `requires_grad=True` 后，每个 tensor 被创建出来的时候，创建它的那一步操作就会被存在 `grad_fn` 属性里。




## 使用 `.requires_grad_()` 来修改 `requires_grad` 属性

可以使用 `.requires_grad_(True)` 来改变现有张量的 `requires_grad`属性。

如果没有指定的话，默认输入的 flag 是 `False`。

举例：

```python
import torch

a = torch.randn(2, 2)
a = a * 3
print(a.requires_grad)

a.requires_grad_(True)
print(a.requires_grad)

b = a.sum()
print(b.grad_fn)
```

输出：

```
False
True
<SumBackward0 object at 0x0000016954F9BF08>
```

## 使用 `with torch.no_grad()` 包裹来禁止 autograd 计算

如果 `.requires_grad=True` 但是你又不希望进行 autograd 的计算，那么可以将变量包裹在 `with torch.no_grad()`中。

举例：



```py
import torch

x = torch.randn(3, requires_grad=True)

print(x.requires_grad)
print((x ** 2).requires_grad)

with torch.no_grad():
	print((x ** 2).requires_grad)
```


输出：

```
True
True
False
```




# 相关

- [pytorch-handbook](https://github.com/zergtant/pytorch-handbook)
- `autograd` 和 `Function` 的官方文档 https://pytorch.org/docs/autograd
