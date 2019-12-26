---
title: 104 字符串 string 4 操作
toc: true
date: 2019-11-17
---

## 字符串运算符


| 操作符 | 描述 | 实例 |
| ------ | ------------------------------------------------------------ | ------------------------------------------------------ |
| `+`      | 字符串连接                                                   | `a + b` 输出结果： HelloPython                           |
| `*`      | 重复输出字符串                                               | `a*2` 输出结果：HelloHello                               |
| `[]`     | 通过索引获取字符串中字符                                     | `a[1]` 输出结果 **e**                                    |
| `[ : ]`  | 截取字符串中的一部分                                         | `a[1:4]` 输出结果 **ell**                                |
| `in`     | 成员运算符 - 如果字符串中包含给定的字符返回 True             | **H in a** 输出结果 1                                  |
| `not in` | 成员运算符 - 如果字符串中不包含给定的字符返回 True           | **M not in a** 输出结果 1                              |
| `r/R`    | 原始字符串 - 原始字符串：所有的字符串都是直接按照字面的意思来使用，没有转义特殊或不能打印的字符。 原始字符串除在字符串的第一个引号前加上字母"r"（可以大小写）以外，与普通字符串有着几乎完全相同的语法。 | `print r'\n'` 输出 `\n` |
| `%`      | 格式字符串                                                   |                                     |


举例：

```py
a = "Hello"
b = "Python"

print(a + b)
print(a * 2)
print(a[1])
print(a[1:4])
print('H' in a)
print('M' not in a)
print(r'aa\nbb')
print(R'aa\nbb')

```

输出：

```
HelloPython
HelloHello
e
ell
True
True
aa\nbb
aa\nbb
```


## 字符串的分割与合并

```py
s = "as, asdas \r\nasda"

print(s.split())
print("".join(s.split()))
print("".join(s.split()).split(','))
```

输出：

```
['as,', 'asdas', 'asda']
as,asdasasda
['as', 'asdasasda']
```

说明：

- split 会在有空格和 `\r` `\n` 的地方进行分割。<span style="color:red;">确认下 split 是按照什么分割的。</span>
- join 会把 list 里面的字符串进行合并。







# 相关

- [Python基础教程 w3cschool](https://www.w3cschool.cn/Python/)
- [Python 3 教程 菜鸟教程](http://www.runoob.com/Python3/Python3-tutorial.html)
- [Python 字符串的 split()函数详解](http://www.cnblogs.com/douzi2/p/5579651.html)
