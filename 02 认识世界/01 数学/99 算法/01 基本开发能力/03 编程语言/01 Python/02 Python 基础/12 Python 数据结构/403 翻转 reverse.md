---
title: 403 翻转 reverse
toc: true
date: 2019-11-17
---
# 翻转 reverse

reverse 可以倒叙迭代序列：

```
list(reversed(range(10)))
```

输出：

```
[9, 8, 7, 6, 5, 4, 3, 2, 1, 0]
```

注意：

- revered是一个生成器 generator，所以必须需要 list 来具现化。


<span style="color:red;">这个 revered 和 [::-1] 有区别吗？</span>
