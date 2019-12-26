---
title: 401 偏函数 partial
toc: true
date: 2019-02-06
---
# 可以补充进来的

- 感觉用到的地方很少，什么时候会使用这个？

# 偏函数


Partial function

举例：


```py
print(int('12345'))
print(int('12345', base=8))


def int2(x, base=2):
    return int(x, base)
print(int2('1000000'))


import functools
int2 = functools.partial(int, base=2)
print(int2('1000000'))
```

输出：

```
12345
5349
64
64
```

说明：

- `int()` 函数可以把字符串转换为整数，当仅传入字符串时，`int()` 函数默认按十进制转换，可以使用 base 来指定进制方式。
- `functools.partial` 可以帮助我们创建一个偏函数的，不需要我们自己定义`int2()`。


简单总结`functools.partial`的作用就是：

- 把一个函数的某些参数给固定住（也就是设置默认值），返回一个新的函数，调用这个新函数会更简单。

注意到上面的新的 `int2` 函数，仅仅是把 `base` 参数重新设定默认值为`2`，但也可以在函数调用时传入其他值：

## 对 partial 函数进一步说明


创建偏函数时，实际上可以接收函数对象、`*args`和`**kw`这 3 个参数，当传入：

```py
import functools

int2 = functools.partial(int, base=2)
print(int2('1000000'))

max2 = functools.partial(max, 10)
print(max2(5, 6, 7))
```

输出：


```
64
10
```

说明：

- `base=2` 实际上固定了 int()函数的关键字参数`base`，也就是相当于：

```py
kw = { 'base': 2 }
int('10010', **kw)
```

- 而 `functools.partial(max, 10)` 实际上会把`10`作为`*args`的一部分自动加到左边，也就是相当于：

```py
args = (10, 5, 6, 7)
max(*args)
```

<span style="color:red;">这个没想到是这样，不错的！</span>




# 原文及引用

- [偏函数](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/00143184474383175eeea92a8b0439fab7b392a8a32f8fa000)
