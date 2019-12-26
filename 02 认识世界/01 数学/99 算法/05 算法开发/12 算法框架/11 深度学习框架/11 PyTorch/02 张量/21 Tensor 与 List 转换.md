---
title: 21 Tensor 与 List 转换
toc: true
date: 2019-12-06
---
# Tensor 与 List 转换

举例：


```py
import torch as T
import numpy as np


x=[1,2,3,4]
y=T.Tensor(x)
print(x)
print(y)
x.append(5)
print(x)
print(y)

z=T.tolist(y)
print(z)
```

输出：

```
[1, 2, 3, 4]
tensor([1., 2., 3., 4.])
[1, 2, 3, 4, 5]
tensor([1., 2., 3., 4.])
Traceback (most recent call last):
File "D:/21.Practice/demo/f.py", line 13, in <module>
  z=T.tolist(y)
AttributeError: module 'torch' has no attribute 'tolist'
```


可见：

- list 的变化不会对相应的 Tensor 有影响。

需要确认的：

- `tolist` 这个函数已经取消了吗？那么现在使用什么转化为 list？



# 相关

- [PyTorch中的 Tensor](https://blog.csdn.net/tfcy694/article/details/80330616)
