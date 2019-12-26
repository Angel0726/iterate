---
title: 103 reduce
toc: true
date: 2019-11-23
---
# reduce


## reduce 与 lambda 函数

Python中的 reduce 内建函数是一个二元操作函数，他用来将一个数据集合(列表，元组等)中的所有数据进行如下操作：

传给 reduce 中的函数 func() (必须是一个二元操作函数)先对集合中的第 1，2个数据进行操作，得到的结果再与第三个数据用 func()函数运算，最后得到一个结果。

即：reduce就是要把一个 list 给缩成一个值。所以你必须用二元操作函数。

```py
from functools import reduce

l = [1, 2, 3, 4, 5]

print(reduce(lambda x, y: x + y, l))
print(reduce(lambda x, y: x + y, l, 10))

def add(x, y):
    return x * 10 + y

l = list(range(10))
print(reduce(add, l))
```

输出：

```
15
25
123456789
```

说明：

- 把 list 中的值，一个个放进 lamda 的 x, y中
- 初始值 10，可以放在 list 后面， x开始的时候被赋值为 10，然后依次类推。




## 使用 reduce 合并函数

### 合并两个函数


我们来实现 `f(g(x))`。

举例：

```py
def compose2(f, g):
    return lambda x: f(g(x))


def inc(x):
    return x + 1


def double(x):
    return x * 2


inc_and_double = compose2(double, inc)
print(inc_and_double(10))
```

输出：

```
22
```

### 合并多个函数

上面是两个函数，那么多个函数这样如何实现？

我们先看三个：


```py
def compose2(f, g):
    return lambda x: f(g(x))


def inc(x):
    return x + 1


def double(x):
    return x * 2


def dec(x):
    return x - 1


inc_double_and_dec = compose2(compose2(dec, double), inc)
print(inc_double_and_dec(10))
```

输出：

```
21
```

可见，按照这个模式是可行的，先合并两个，再继续进行合并。

OK，那么，我们就可以使用 reduce 实现：


```py
import functools

def compose(*functions):
    def compose2(f, g):
        return lambda x: f(g(x))
    return functools.reduce(compose2, functions, lambda x: x)


def inc(x):
    return x + 1


def double(x):
    return x * 2


def dec(x):
    return x - 1


inc_double_and_dec = compose(dec,double,inc)
print(inc_double_and_dec(10))
```

输出：

```
21
```

有点疑惑的：

- 这个 `10` 作为 `x` 是怎么传入到 compose 函数里面然后被 `lambda x: x` 使用的？<span style="color:red;">不明白的点。</span>

更紧凑一点：

Or in a more compact way:

```py
import functools

def compose(*functions):
    return functools.reduce(lambda f, g: lambda x: f(g(x)), functions, lambda x: x)


def inc(x):
    return x + 1


def double(x):
    return x * 2


def dec(x):
    return x - 1


inc_double_and_dec = compose(dec,double,inc)
print(inc_double_and_dec(10))
```

输出：

```
21
```

### 一些应用

举例：


```py
import functools
import operator


def compose(*functions):
    return functools.reduce(lambda f, g: lambda x: f(g(x)), functions, lambda x: x)

def second(*args):
    print(args)
    return args[1]
def second_wrapper(lst):
    return second(*lst)
pipeline = compose(second_wrapper, list, range)
print(pipeline(5))


def sub(a, b):
    print(a,b)
    return a - b
pipeline = compose(functools.partial(sub, b=4), operator.neg)
print(pipeline(-6))
```

输出：

```
(0, 1, 2, 3, 4)
1
6 4
2
```






# 相关：


- [函数式编程](https://coolshell.cn/articles/10822.html)
- [map/reduce](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/0014317852443934a86aa5bb5ea47fbbd5f35282b331335000)
- [Function Composition in Python](https://mathieularose.com/function-composition-in-Python/)
