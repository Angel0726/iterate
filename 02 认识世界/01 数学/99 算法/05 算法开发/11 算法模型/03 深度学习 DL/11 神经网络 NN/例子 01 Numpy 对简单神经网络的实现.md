---
title: 例子 01 Numpy 对简单神经网络的实现
toc: true
date: 2019-06-28
---
# numpy 对简单神经网络的实现

## 使用 numpy 实现双层神经网络 并对随机数据进行拟合

Before introducing PyTorch, we will first implement the network using numpy.

Numpy provides an n-dimensional array object, and many functions for manipulating these arrays. Numpy is a generic framework for scientific computing; it does not know anything about computation graphs, or deep learning, or gradients. However we can easily use numpy to fit a two-layer network to random data by manually implementing the forward and backward passes through the network using numpy operations:

```py
# -*- coding: utf-8 -*-
import numpy as np

# N is batch size; D_in is input dimension;
# H is hidden dimension; D_out is output dimension.
N, D_in, H, D_out = 64, 1000, 100, 10

# Create random input and output data
x = np.random.randn(N, D_in)
y = np.random.randn(N, D_out)

# Randomly initialize weights
w1 = np.random.randn(D_in, H)
w2 = np.random.randn(H, D_out)

learning_rate = 1e-6
for t in range(500):
    # Forward pass: compute predicted y
    h = x.dot(w1)
    h_relu = np.maximum(h, 0)
    y_pred = h_relu.dot(w2)

    # Compute and print loss
    loss = np.square(y_pred - y).sum()
    print(t, loss)

    # Backprop to compute gradients of w1 and w2 with respect to loss
    grad_y_pred = 2.0 * (y_pred - y)
    grad_w2 = h_relu.T.dot(grad_y_pred)
    grad_h_relu = grad_y_pred.dot(w2.T)
    grad_h = grad_h_relu.copy()
    grad_h[h < 0] = 0
    grad_w1 = x.T.dot(grad_h)

    # Update weights
    w1 -= learning_rate * grad_w1
    w2 -= learning_rate * grad_w2
```

输出：

```
0 27749173.17404762
1 24712592.99294218
2 26130142.494734924
3 27990526.91602715
略..
223 1.0644148539146285
224 1.0145271528430595
225 0.9669778862448415
略..
496 3.023962725102998e-06
497 2.887605619589804e-06
498 2.757406536636644e-06
499 2.6330866798532966e-06
```

<span style="color:red;">哇塞！震惊了，原来这就是使用 numpy 实现的双层神经网络的拟合，厉害呀！赞！！</span>


Numpy is a great framework, but it cannot utilize GPUs to accelerate its numerical computations. For modern deep neural networks, GPUs often provide speedups of [50x or greater](https://github.com/jcjohnson/cnn-benchmarks), so unfortunately numpy won’t be enough for modern deep learning.




# 相关

- [pytorch-handbook](https://github.com/zergtant/pytorch-handbook)
