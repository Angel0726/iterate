---
title: 104 综合使用 map reduce filter
toc: true
date: 2019-11-23
---


## 综合使用 map reduce filter

### 举例 1：


把 `str` 转换为 `int`：


```py
from functools import reduce

DIGITS = {'0': 0, '1': 1, '2': 2, '3': 3, '4': 4, '5': 5, '6': 6, '7': 7, '8': 8, '9': 9}

def char2num(s):
    return DIGITS[s]


def str2int(s):
    return reduce(lambda x, y: x * 10 + y, map(char2num, s))

print(char2num('5'))
print(str2int('6468434'))
```

输出：

```
5
6468434
```



### 举例 2：

将列表中的偶数取出来，然后乘以 3，再转化为 string。

```py
from functools import reduce

def even_filter(nums):
    return filter(lambda x: x % 2 == 0, nums)

def multiply_by_three(nums):
    return map(lambda x: x * 3, nums)

def convert_to_string(nums):
    return map(lambda x: 'The Number: %s' % x, nums)


# 可以这样调用，但是看起来就不是很美观
nums = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
pipeline = convert_to_string(
    multiply_by_three(
        even_filter(nums)
    ))
print(list(pipeline))


def pipeline_func(data, funcs):
    # 将 data 作为初始值，
    # 将 funcs 的第一个和第二个函数拿出来，执行出来一个结果然后传入第三个函数
    # y 是 func ，x 是 data。
    return reduce(lambda x, y: y(x), funcs, data)

res = pipeline_func(nums, [even_filter, multiply_by_three, convert_to_string])
print(list(res))
```

输出：

```
['The Number: 6', 'The Number: 12', 'The Number: 18', 'The Number: 24', 'The Number: 30']
['The Number: 6', 'The Number: 12', 'The Number: 18', 'The Number: 24', 'The Number: 30']
```

说明：

- 上面分别用三个lambda 实现了三个函数，然后，在如何在一个 list 上使用这三个函数的时候，**pipeline_func 又使用了 reduce，来排列使用这三个函数。让他们依次作用在数据列表上。**



# 相关：


- [函数式编程](https://coolshell.cn/articles/10822.html)
- [map/reduce](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/0014317852443934a86aa5bb5ea47fbbd5f35282b331335000)
