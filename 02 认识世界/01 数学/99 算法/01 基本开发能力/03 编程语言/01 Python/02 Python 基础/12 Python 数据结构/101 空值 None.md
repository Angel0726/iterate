---
title: 101 空值 None
toc: true
date: 2018-08-19
---
# 可以补充进来的


# 空值 None

## None 的理解

空值是 Python 里一个特殊的值，用`None`表示。

对于 None 的理解：

- None 本质是一个值。
- `None`不能理解为`0`，因为`0`是有意义的，而`None`是一个特殊的空值。

## None 与别的数据类型的比较

与空的 list 的比较：

```py
a=list()
print(type(a))
print(a)
print(a is None)
print(a == None)
```

输出：

```
<class 'list'>
[]
False
False
```

可见：空的 list 仍然是一个 list，并不是 None。


与用 opencv 加载的图片进行比较：

```py
import cv2

a=cv2.imread('./b.png')
print(type(a))
print(a)
print(a is None)
print(a == None)
```

当不存在 b.png 图片时，输出：

```
<class 'NoneType'>
None
True
True
```

当存在 b.png 图片时，输出：

```
<class 'numpy.ndarray'>
[[[0 0 0]
  [0 0 0]
  [0 0 0]
  ...
  [0 0 0]
  [0 0 0]
  [0 0 0]]
 ...
 ...
 [[0 0 0]
  [0 0 0]
  [0 0 0]
  ...
  [0 0 0]
  [0 0 0]
  [0 0 0]]]
False
[[[False False False]
  [False False False]
  [False False False]
  ...
  [False False False]
  [False False False]
  [False False False]]
 ...
 ...
 [[False False False]
  [False False False]
  [False False False]
  ...
  [False False False]
  [False False False]
  [False False False]]]
```

从上面可以看出：**判断一个对象是否是 None，最好用 is None 来判断。**


# 相关

- [数据类型和变量](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001431658624177ea4f8fcb06bc4d0e8aab2fd7aa65dd95000)
