---
title: python enumerate
toc: true
date: 2018-06-22 22:03:54
---
## 可以补充进来的
- 之前我用这个 enumerate 的时候发现了个问题，就是 对一个 list enumerate 然后进行遍历之后，再遍历这个 enumerate 的结果就不行了，好像这个结果并没有保存下来。<font color=red>确认下</font>
-


# 缘由：


之前看到别人 for 循环中使用 enumerate，将一个 list 转为 index 和 value，有些厉害，总结一下。
这个 enumerate 用起来还是挺简单的，现在看来，是真的很方便。


# 要点：


代码如下：
```py
#enumerate 在列表或元组既要遍历索引又要遍历元素的时候使用
l = [1, 2, 3]
for i in range(len(l)):
    print(i, l[i])

for index, value in enumerate(l):
    print(index, value)
print()

e=enumerate(l)
print(e)
print(type(e))#格式为 enumerate
```

输出：

```text
0 1
1 2
2 3
0 1
1 2
2 3

<enumerate object at 0x000001D802AA74C8>
<class 'enumerate'>
```
