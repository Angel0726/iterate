---
title: 105 sorted
toc: true
date: 2019-02-05
---
# 可以补充进来的

- 非常好，这个 key 的设定。


# sorted

举例：


```py
l = sorted([36, 5, -12, 9, -21])
print(l)
l = sorted(['bob', 'about', 'Zoo', 'Credit'])
print(l)
```

输出：

```
[-21, -12, 5, 9, 36]
['Credit', 'Zoo', 'about', 'bob']
```

注意：

- **默认小的排在前面**。
- 字符串的大小是按照 ASCII 的大小比较的，由于`'Z' < 'a'`，结果，大写字母`Z`会排在小写字母`a`的前面。



## 使用 key


可以接收一个 `key` 函数来实现**自定义的排序**。

举例：

```py
l = sorted([36, 5, -12, 9, -21], key=abs)
print(l)
l = sorted(['bob', 'about', 'Zoo', 'Credit'], key=str.lower)
print(l)
```

输出：

```
[5, 9, -12, -21, 36]
['about', 'bob', 'Credit', 'Zoo']
```

## 使用 reverse 反向排序


要进行反向排序，可以传入第三个参数`reverse=True`：

```py
l = sorted(['bob', 'about', 'Zoo', 'Credit'], key=str.lower, reverse=True)
print(l)
```

输出：

```
['Zoo', 'Credit', 'bob', 'about']
```



# 原文及引用

- [sorted](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/0014318230588782cac105d0d8a40c6b450a232748dc854000)
2748dc854000)
