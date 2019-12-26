---
title: 401 枚举 enumerate
toc: true
date: 2019-11-17
---

# 枚举 enumerate

enumerate 通常用来把一个 list 中的位置和值映射到一个 dcit 字典里：

```py
some_list = ['foo', 'bar', 'baz']
mapping = {}
for i, v in enumerate(some_list):
    mapping[v] = i
print(mapping)
```

输出：

```
{'bar': 1, 'baz': 2, 'foo': 0}
```
