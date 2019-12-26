---
title: 14 利用Numba构建快速的NumPy函数
toc: true
date: 2018-07-08 13:36:55
---

# A.7 Writing Fast NumPy Functions with Numba（利用 Numba 构建快速的 NumPy 函数）

numba是一个开源项目，对于类似于 numpy 的数据，numba创建的函数，能利用 CPU、GPU或其他一些硬件进行快速计算。它利用[LLVM Project](http://llvm.org/)项目，把 Python 代码编译为机器代码。

我们先写一个纯 Python 的例子，用 for 循环计算(x-y).mean():


```Python
import numpy as np
```


```Python
def mean_distance(x, y):
    nx = len(x)
    result = 0.0
    count = 0
    for i in range(nx):
        result += x[i] - y[i]
        count += 1
    return result / count
```

上面的函数是很慢的：


```Python
x = np.random.randn(10000000)
y = np.random.randn(10000000)
```


```Python
%timeit mean_distance(x, y)
```

    1 loop, best of 3: 4.36 s per loop
    


```Python
%timeit (x - y).mean()
```

    10 loops, best of 3: 45.2 ms per loop
    

numpy版本快 100 倍。我们使用 numba.jit把这个函数变为 numba 函数：


```Python
import numba as nb
```


```Python
numba_mean_distance = nb.jit(mean_distance)
```

我们也可以写成装饰器（decorator）：

    @nb.jit
    def mean_distance(x, y):
        nx = len(x)
        result = 0.0
        count = 0
        for i in range(nx):
            result += x[i] - y[i]
            count += 1
        return result / count
        
结果会比向量化的 numpy 版本还要快：


```Python
%timeit numba_mean_distance(x, y)
```

    The slowest run took 30.87 times longer than the fastest. This could mean that an intermediate result is being cached.
    1 loop, best of 3: 14.2 ms per loop
    

numba的 jit 函数有一个选项，nopyhton=True，能强制通过 LLVM 对代码进行编译，而不调用任何 Python C API。jit(noPython=True)有一个简写，numba.njit.

上面的例子可以写成：


```Python
from numba import float64, njit

@njit(float64(float64[:], float64[:]))
def mean_distance(x, y):
    return (x - y).mean()
```

# 1 Creating Custom numpy.ufunc Objects with Numba（利用 Numba 创建自定义的 numpy.ufunc对象）

numba.vectorize函数能创建编译过的 numpy ufuncs，效果就像是内建的 built-in ufuncs一样。比如我们实现一个 numpy.add：


```Python
from numba import vectorize

@vectorize
def nb_add(x, y):
    return x + y
```


```Python
x = np.arange(10)
```


```Python
nb_add(x, x)
```




    array([ 0,  2,  4,  6,  8, 10, 12, 14, 16, 18])




```Python
nb_add.accumulate(x, 0)
```




    array([ 0,  1,  3,  6, 10, 15, 21, 28, 36, 45])






