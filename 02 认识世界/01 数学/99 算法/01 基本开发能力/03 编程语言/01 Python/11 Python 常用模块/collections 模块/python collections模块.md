---
title: python collections模块
toc: true
date: 2018-07-13 19:48:57
---
# Python collections模块

## 缘由：

collections是 Python 内建的一个集合模块，里面提供了除了 Python 原生的之外的一些集合结构，还是很有用的

## 要点：

### 1.namedtuple

```Python
# 感觉这个 namedtyple 就像创建了一个类一样
# 比如这个就像创建了一个 Point 的类，然后实例化为 p
from collections import namedtuple

Point = namedtuple('Point', ['x', 'y'])
print(Point)
p = Point(1, 2)
print(p.x, p.y)
```

输出：


```Python
<class '__main__.Point'>
1 2
```

<span style="color:blue;">这个 namedtuple 一般用在什么地方？嗯，这个一般是作为没有方法的类使用的。</span>



### 2.deque

```Python
from collections import deque

# deque 是为了高效实现插入和删除操作的双向列表，适合用于队列和栈
q = deque(['a', 'b', 'c'])
q.append('x')
q.appendleft('y')
print(q)
```

输出：


```
deque(['y', 'a', 'b', 'c', 'x'])
```


这个应该用的很多


### 3.defaultdict 与 Ordereddict


```Python
from collections import defaultdict
import traceback

d = dict({'key1': 1})
print(d['key1'])
try:
    print(d['key2'])# 普通的 dict 如果没有这个 key 会报 KeyError
except:
    traceback.print_exc()

dd = defaultdict(lambda: 'N/A')  # 为什么这个地方是一个 lambda 表达式？
dd['key1'] = 'abc'
print(dd['key1'])  # key1存在
print(dd['key2'])  # key2不存在，返回默认值
```





```Python
from collections import OrderedDict

d = dict([('a', 1), ('b', 2), ('c', 3)])
print(d)  # dict的 Key 是无序的，因此结果是不一定的 {'a': 1, 'c': 3, 'b': 2}
od = OrderedDict([('a', 1), ('b', 2), ('c', 3)])
print(od)  # OrderedDict的 Key 是有序的，OrderedDict([('a', 1), ('b', 2), ('c', 3)])
```



输出：


```
1
Traceback (most recent call last):
  File "E:/01.Learn/01.Python/01.PythonBasic/c7.py", line 6, in <module>
    print(d['key2'])
KeyError: 'key2'
abc
N/A
{'a': 1, 'b': 2, 'c': 3}
OrderedDict([('a', 1), ('b', 2), ('c', 3)])
```


注：为什么 default 的初始化用的是一个 lambda 表达式？而且为什么是 N/A？


### 4.Counter




```Python
from collections import Counter

g = {}
c = Counter()
for ch in 'programming':
    c[ch] = c[ch] + 1
    if not g.__contains__(ch):
        g[ch] = 1
    else:
        g[ch] = g[ch] + 1
print(g)
print(c)  # Counter({'g': 2, 'm': 2, 'r': 2, 'a': 1, 'i': 1, 'o': 1, 'n': 1, 'p': 1})
```


输出：


```
Counter({'r': 2, 'g': 2, 'm': 2, 'p': 1, 'o': 1, 'a': 1, 'i': 1, 'n': 1})
{'p': 1, 'r': 2, 'o': 1, 'g': 2, 'a': 1, 'm': 2, 'i': 1, 'n': 1}
```


注：感觉这个 Counter 就类似一个 dict 一样，不过用起来稍微方便些。counter的确是 dict 的一个子类


## COMMENT：


感觉 deque 用的应该会很多，namedtuple、defaultdict、Ordereddict 和 Counter 感觉都用的比较少
