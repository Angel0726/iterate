---
title: 11 高级ufunc用法
toc: true
date: 2018-08-03 11:23:43
---

# A.4 Advanced ufunc Usage（高级 ufunc 用法）

尽管很多 numpy 用户只利用通用函数的点对点操作（element-wise operations），其实还有一些其他方法能帮助我们在不用循环的前提下写得更简洁。

# 1 ufunc Instance Methods（ufunc实例方法）

numpy的 ufuncs 有特别的方法用来进行特殊的向量化操作。下表进行了总结。这里我们给一些更具体的例子。

![](http://images.iterate.site/blog/image/180803/33ak1DaBfe.png?imageslim){ width=55% }

reduce接受一个数组，并对其进行聚合，可以选择沿着哪个轴。例如，用 np.add.reduce来对一个数组中的元素进行相加：




```Python
import numpy as np
```


```Python
arr = np.arange(10)
arr
```




    array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])




```Python
np.add.reduce(arr)
```




    45




```Python
arr.sum()
```




    45



开始的值（即 0）取决于 ufunc。如果传入了轴，降维会沿着轴进行。我们可以利用 np.logical_and来检查每一行的值是不是排好了序：


```Python
np.random.seed(12346)
```


```Python
arr = np.random.randn(5, 5)
arr
```




    array([[  5.22417588e-01,   1.06416038e-01,   1.02713364e-01,
             -1.08232609e-01,   5.48599204e-02],
           [  1.96365303e-01,  -1.93872621e-01,  -1.45657748e+00,
              8.57447625e-01,  -7.41575581e-01],
           [ -7.80362529e-01,  -1.06424500e-01,   5.93712721e-01,
             -1.28346227e+00,   4.77960478e-01],
           [  1.29244703e+00,   1.51649202e-01,  -1.46631428e+00,
             -1.43337431e+00,  -9.77525491e-02],
           [  1.23514614e+00,   1.35506346e-01,  -7.05498872e-04,
              2.53602483e-01,  -1.83245736e-01]])




```Python
arr[::2].sort(1) # sort a few rows
arr
```




    array([[ -1.08232609e-01,   5.48599204e-02,   1.02713364e-01,
              1.06416038e-01,   5.22417588e-01],
           [  1.96365303e-01,  -1.93872621e-01,  -1.45657748e+00,
              8.57447625e-01,  -7.41575581e-01],
           [ -1.28346227e+00,  -7.80362529e-01,  -1.06424500e-01,
              4.77960478e-01,   5.93712721e-01],
           [  1.29244703e+00,   1.51649202e-01,  -1.46631428e+00,
             -1.43337431e+00,  -9.77525491e-02],
           [ -1.83245736e-01,  -7.05498872e-04,   1.35506346e-01,
              2.53602483e-01,   1.23514614e+00]])




```Python
arr[:, :-1] < arr[:, 1:]
```




    array([[ True,  True,  True,  True],
           [False, False,  True, False],
           [ True,  True,  True,  True],
           [False, False,  True,  True],
           [ True,  True,  True,  True]], dtype=bool)




```Python
np.logical_and.reduce(arr[:, :-1] < arr[:, 1:], axis=1)
```




    array([ True, False,  True, False,  True], dtype=bool)



注意 logical_and.reduce和 all 方法效果一样。

accumulate之于 reduce，就像 cumsum 之于 sum。accumulate会产生一个和原数组一样形状的数组，然后展示出累计的中间结果：


```Python
arr = np.arange(15).reshape((3, 5))
arr
```




    array([[ 0,  1,  2,  3,  4],
           [ 5,  6,  7,  8,  9],
           [10, 11, 12, 13, 14]])




```Python
np.add.accumulate(arr, axis=1)
```




    array([[ 0,  1,  3,  6, 10],
           [ 5, 11, 18, 26, 35],
           [10, 21, 33, 46, 60]])



outer能对两个数组进行叉乘（cross-product）:


```Python
arr = np.arange(3).repeat([1, 2, 3])
arr
```




    array([0, 1, 1, 2, 2, 2])




```Python
np.multiply.outer(arr, np.arange(5))
```




    array([[0, 0, 0, 0, 0],
           [0, 1, 2, 3, 4],
           [0, 1, 2, 3, 4],
           [0, 2, 4, 6, 8],
           [0, 2, 4, 6, 8],
           [0, 2, 4, 6, 8]])



outer的输出中，有一个维度是用来计算输入的维度的和：


```Python
x, y = np.random.randn(3, 4), np.random.randn(5)
```


```Python
x
```




    array([[-0.7066303 ,  0.42676074, -0.27757707, -0.82828458],
           [-2.76283358,  0.98349424,  0.43775139, -0.84956379],
           [ 0.71876344,  0.73289771,  0.50470465, -0.7892592 ]])




```Python
y
```




    array([ 0.5391877 ,  1.29070685,  0.86761856,  0.41133011,  0.44593599])




```Python
result = np.subtract.outer(x, y)
result.shape
```




    (3, 4, 5)



reduceat，会进行局部降维（local reduce），就是对一个数组进行 groupby 操作后，再聚合到一起。这个方法接受桶范围（bin edge，其实就是边界），用来指定如何分割和聚合：


```Python
arr = np.arange(10)
arr
```




    array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])




```Python
np.add.reduceat(arr, [0, 5, 8])
```




    array([10, 18, 17])



结果是对 arr[0:5], arr[5:8], arr[8:]上进行了降维（这里为 sum）。我们也可以传入一个轴参数：


```Python
arr = np.multiply.outer(np.arange(4), np.arange(5))
arr
```




    array([[ 0,  0,  0,  0,  0],
           [ 0,  1,  2,  3,  4],
           [ 0,  2,  4,  6,  8],
           [ 0,  3,  6,  9, 12]])



# 2 Writing New ufuncs in Python（在 Python 中写新的 ufuncs）

想要创建自己的 numpy ufuncs，最普遍的方法是用 numpy C API，但是这个超出了本书的范围。这里我们只用纯 Python 的 ufuncs。

numpy.formpyfunc接受一个 pytohn 函数以及用来指定输入和输出的数字。例如，一个点对点相加的函数可以写为：


```Python
def add_elements(x, y):
    return x + y
```


```Python
add_them = np.frompyfunc(add_elements, 2, 1)
add_them
```




    <ufunc '? (vectorized)'>




```Python
add_them(np.arange(8), np.arange(8))
```




    array([0, 2, 4, 6, 8, 10, 12, 14], dtype=object)



用 frompyfunc 构造的函数，返回的永远是一个由 Python 对象构成的数组，这是很不方便的。不过有一个方法可以指定输出的类型，numpy.vectorize:


```Python
add_them = np.vectorize(add_elements, otypes=[np.float64])
```


```Python
add_them(np.arange(8), np.arange(8))
```




    array([  0.,   2.,   4.,   6.,   8.,  10.,  12.,  14.])



这些函数能让我们构建像 ufunc 一样的函数，但是这些函数相对于基于 numpy C构建的函数来说，运算比较慢：


```Python
arr = np.random.randn(10000)
```


```Python
%timeit add_them(arr, arr)
```

    100 loops, best of 3: 2.54 ms per loop



```Python
%timeit np.add(arr, arr)
```

    The slowest run took 37.36 times longer than the fastest. This could mean that an intermediate result is being cached.
    100000 loops, best of 3: 5.25 µs per loop


在这一章之后我们会介绍如何使用[Numba project](http://numba.pydata.org/)来构建更快的函数。


