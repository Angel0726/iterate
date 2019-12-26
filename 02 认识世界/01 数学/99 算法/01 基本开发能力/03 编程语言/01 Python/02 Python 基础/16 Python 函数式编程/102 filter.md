---
title: 102 filter
toc: true
date: 2019-11-23
---

# filter

作用：用于过滤序列。

说明：

- `filter()` 接收一个函数和一个序列。把传入的函数依次作用于每个元素，若 `True` 则保存， `False` 则丢弃。
- `filter()` 函数返回的是一个 `Iterator`，也就是一个惰性序列，所以要强迫 `filter()` 完成计算结果，可以用 `list()` 函数获得所有结果并返回 list。<span style="color:red;">嗯，是的。</span>

## filter 与 lambda 函数


filter()函数可以对序列做过滤处理，就是说可以使用一个自定的函数过滤一个序列，把序列的每一项传到自定义的过滤函数里处理，并返回结果做过滤。最终一次性返回过滤后的结果。

和 map()类似，filter()也接收一个函数和一个序列。和 map()不同的时，filter()把传入的函数依次作用于每个元素，然后根据返回值是 True 还是 False 决定保留还是丢弃该元素。

举例：

```py
l = [1, 2, 3, 4]
new_list = list(filter(lambda x: x < 3, l))
print(new_list)
```

输出：

```
[1, 2]
```




## 用 filter 求素数

计算[素数](http://baike.baidu.com/view/10626.htm)的一个方法是[埃氏筛法](http://baike.baidu.com/view/3784258.htm)，它的算法理解起来非常简单：

首先，列出从 `2` 开始的所有自然数，构造一个序列：

2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, ...

取序列的第一个数`2`，它一定是素数，然后用`2`把序列的`2`的倍数筛掉：

3, ~~4~~, 5, ~~6~~, 7, ~~8~~, 9, ~~10~~, 11, ~~12~~, 13, ~~14~~, 15, ~~16~~, 17, ~~18~~, 19, ~~20~~, ...

取新序列的第一个数`3`，它一定是素数，然后用`3`把序列的`3`的倍数筛掉：

5, ~~6~~, 7, ~~8~~, ~~9~~, ~~10~~, 11, ~~12~~, 13, ~~14~~, ~~15~~, ~~16~~, 17, ~~18~~, 19, ~~20~~, ...

取新序列的第一个数`5`，然后用`5`把序列的`5`的倍数筛掉：

7, ~~8~~, ~~9~~, ~~10~~, 11, ~~12~~, 13, ~~14~~, ~~15~~, ~~16~~, 17, ~~18~~, 19, ~~20~~, ...

不断筛下去，就可以得到所有的素数。

用 Python 来实现这个算法，可以先构造一个从`3`开始的奇数序列：

```py
def _odd_iter():
    n = 1
    while True:
        n = n + 2
        yield n
```

注意这是一个生成器，并且是一个无限序列。

然后定义一个筛选函数：<span style="color:red;">这个地方没有想到，没想到可以这么写函数，而且把 lambda 封装到函数里。不错。想再看下一个例子。</span>

```py
def _not_divisible(n):
    return lambda x: x % n > 0
```

最后，定义一个生成器，不断返回下一个素数：

```py
def primes():
    yield 2
    it = _odd_iter() # 初始序列
    while True:
        n = next(it) # 返回序列的第一个数
        yield n
        it = filter(_not_divisible(n), it) # 构造新序列
```

<span style="color:red;">不错呀，这个</span>

这个生成器先返回第一个素数`2`，然后，利用`filter()`不断产生筛选后的新的序列。

由于`primes()`也是一个无限序列，所以调用时需要设置一个退出循环的条件：<span style="color:red;">嗯，不错。</span>

```py
# 打印 1000 以内的素数:
for n in primes():
    if n < 1000:
        print(n)
    else:
        break
```

注意到 `Iterator` 是惰性计算的序列，所以我们可以用 Python 表示“全体自然数”，“全体素数”这样的序列，而代码非常简洁。<span style="color:red;">没想到可以这样，有些厉害。</span>



# 相关：

- [函数式编程](https://coolshell.cn/articles/10822.html)
- [filter](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001431821084171d2e0f22e7cc24305ae03aa0214d0ef29000)
- [map/reduce](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/0014317852443934a86aa5bb5ea47fbbd5f35282b331335000)
