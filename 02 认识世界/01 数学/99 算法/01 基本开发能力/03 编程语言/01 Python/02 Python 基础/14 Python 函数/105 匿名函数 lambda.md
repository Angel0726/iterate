---
title: 105 匿名函数 lambda
toc: true
date: 2019-11-17
---

# 匿名函数 lambda


Python 使用 lambda 来创建匿名函数。

## lambda 函数介绍

- lambda 只是一个表达式，函数体比 def 简单很多。
- lambda 的主体是一个表达式，而不是一个代码块。仅仅能在 lambda 表达式中封装有限的逻辑进去。
- lambda 函数拥有自己的命名空间，且不能访问自有参数列表之外或全局命名空间里的参数。
- 虽然 lambda 函数看起来只能写一行，却不等同于 C 或 C++ 的内联函数，后者的目的是调用小函数时不占用栈内存从而增加运行效率。

举例：

```py
# 可写函数说明
sum = lambda arg1, arg2: arg1 + arg2;

# 调用 sum 函数
print(sum(10, 20))
print(sum(20, 20))


g = lambda x: x * 2
print(g(3))
print((lambda x: x * 2)(4))
```

输出：

```
30
40
6
8
```

## 排序时候使用 lambda

假设你想按不同字母的数量给一组字符串排序：

```py
l = ['foo', 'card', 'bar', 'aaaa', 'abab']
l.sort(key=lambda x: len(set(list(x))))
print(l)
```

输出：

```
['aaaa', 'foo', 'abab', 'bar', 'card']
```




# 相关：


- [函数式编程](https://coolshell.cn/articles/10822.html)
- [map/reduce](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/0014317852443934a86aa5bb5ea47fbbd5f35282b331335000)
