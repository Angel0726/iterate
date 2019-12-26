---
title: 04 使用 backward 来进行反向传播计算
toc: true
date: 2019-06-27
---
# 可以补充进来的



# 使用 backward 来进行反向传播计算

**举例 1：**


```python
import torch

x = torch.ones(2, 2, requires_grad=True)
y = x + 2
z = y * y * 3
out = z.mean()

print(x)
print(y)
print(z)
print(out)

out.backward()
print(x.grad)
```

输出：

```
tensor([[1., 1.],
        [1., 1.]], requires_grad=True)
tensor([[3., 3.],
        [3., 3.]], grad_fn=<AddBackward0>)
tensor([[27., 27.],
        [27., 27.]], grad_fn=<MulBackward0>)
tensor(27., grad_fn=<MeanBackward0>)
tensor([[4.5000, 4.5000],
        [4.5000, 4.5000]])
```

计算过程说明：

由 $x$,$y$,$z$,$o$ 的过程我们知道：


$$y_i = x_i+2=3$$

$$z_i = 3(y_i)^2=27$$

$$o = \frac{1}{4}\sum_i z_i=27$$

我们知道，数学中，计算张量  $\vec{y}=f(\vec{x})$ 的导数是 Jacobian 矩阵：

$$
\begin{aligned}J=\left(\begin{array}{ccc}  \frac{\partial y_{1}}{\partial x_{1}} & \cdots & \frac{\partial y_{1}}{\partial x_{n}}\\  \vdots & \ddots & \vdots\\  \frac{\partial y_{m}}{\partial x_{1}} & \cdots & \frac{\partial y_{m}}{\partial x_{n}}  \end{array}\right)\end{aligned}
$$

由于 `out` 是一个纯量（scalar），`out.backward()` 等于 `out.backward(torch.tensor(1))`。

所以：

$$\frac{\partial o}{\partial x_i} =\frac{1}{4}\frac{\partial z_i}{\partial x_i}=\frac{6}{4}\frac{y_i\partial y_i}{\partial x_i}=\frac{6\times 3}{4}=4.5$$


**举例 2：**

在上面的例子上做一些改动：


```py
import torch

x = torch.ones(2, 2, requires_grad=True)
y = x + 2
z = y * y * 3
out = z.mean()

print(x)
print(y)
print(z)
print(out)


gradients = torch.tensor([[0.1, 1.0], [1.0,1.0]], dtype=torch.float)
z.backward(gradients,retain_graph=True)
print(x.grad)

out.backward()
print(x.grad)
```

输出：

```
tensor([[1., 1.],
        [1., 1.]], requires_grad=True)
tensor([[3., 3.],
        [3., 3.]], grad_fn=<AddBackward0>)
tensor([[27., 27.],
        [27., 27.]], grad_fn=<MulBackward0>)
tensor(27., grad_fn=<MeanBackward0>)
tensor([[ 1.8000, 18.0000],
        [18.0000, 18.0000]])
tensor([[ 6.3000, 22.5000],
        [22.5000, 22.5000]])
```


计算过程说明：

由 $x$,$y$,$z$,$o$ 的过程我们知道：


$$y_i = x_i+2=3$$

$$z_i = 3(y_i)^2=27$$

$$o = \frac{1}{4}\sum_i z_i=27$$

我们先用 `z.backward` 对 `x` 求导：

$$\frac{\partial z_1}{\partial x_1} =0.1\times 6\frac{y_i\partial y_i}{\partial x_i}=0.1\times 6\times 3=1.8$$

然后，我们使用了 `out.backward()` 再次求导：


$$\frac{\partial o_1}{\partial x_1} =\frac{1}{4}\frac{\partial z_i}{\partial x_i}+1.8=\frac{6}{4}\frac{y_i\partial y_i}{\partial x_i}+1.8=\frac{6\times 3}{4}+1.8=6.3$$


注意：

- 怎么清掉上次计算出的偏导数？**可以使用 `x.grad=None` 来清掉上次计算的偏导数。**

不是很清楚的：

- <span style="color:red;">为什么要加上上次计算的偏导数呢？</span>



## 对 torch.autograd 的说明


Generally speaking, `torch.autograd` is an engine for computing vector-Jacobian product. That is, given any vector $v=\left(\begin{array}{cccc} v_{1} & v_{2} & \cdots & v_{m}\end{array}\right)^{T}$, compute the product $v^{T}\cdot J$ . If $v$ happens to be the gradient of a scalar function $l=g\left(\vec{y}\right)$, that is,$v=\left(\begin{array}{ccc}\frac{\partial l}{\partial y_{1}} & \cdots & \frac{\partial l}{\partial y_{m}}\end{array}\right)^{T}$, then by the chain rule, the vector-Jacobian product would be the gradient of lwith respect to $\vec{x}$:


$$
\begin{aligned}J^{T}\cdot v=\left(\begin{array}{ccc}  \frac{\partial y_{1}}{\partial x_{1}} & \cdots & \frac{\partial y_{m}}{\partial x_{1}}\\  \vdots & \ddots & \vdots\\  \frac{\partial y_{1}}{\partial x_{n}} & \cdots & \frac{\partial y_{m}}{\partial x_{n}}  \end{array}\right)\left(\begin{array}{c}  \frac{\partial l}{\partial y_{1}}\\  \vdots\\  \frac{\partial l}{\partial y_{m}}  \end{array}\right)=\left(\begin{array}{c}  \frac{\partial l}{\partial x_{1}}\\  \vdots\\  \frac{\partial l}{\partial x_{n}}  \end{array}\right)\end{aligned}
$$

(Note that $v^{T}\cdot J$ gives a row vector which can be treated as a column vector by taking $J^{T}\cdot v$.)

This characteristic of vector-Jacobian product makes it very convenient to feed external gradients into a model that has non-scalar output.



# 相关

- [pytorch-handbook](https://github.com/zergtant/pytorch-handbook)
- `autograd` 和 `Function` 的官方文档 https://pytorch.org/docs/autograd
