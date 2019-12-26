---
title: 203 元组 tuple 2 unpackeing
toc: true
date: 2019-11-17
---

这种 unpacking 通常用在迭代序列上：


```
seq = [(1, 2, 3), (4, 5, 6), (7, 8, 9)]
for a, b, c in seq:
    print('a={0}, b={1}, c={2}'.format(a, b, c))
```
输出：
```
a=1, b=2, c=3
a=4, b=5, c=6
a=7, b=8, c=9
```

另一种更高级的 unpacking 方法是用于只取出 tuple 中开头几个元素，剩下的元素直接赋给`*rest`：==没想到还可以这样，厉害了==

```
values = 1, 2, 3, 4, 5
a, b, *rest = values
print(a,b)
print(rest)
```
输出：
```
1 2
[3, 4, 5]
```

如果 rest 部分是你想要丢弃的，名字本身无所谓，通常用下划线来代替：==嗯，厉害，这个是经常看到的==

```
a, b, *_ = values
```
