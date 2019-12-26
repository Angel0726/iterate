---
title: 203 元组 tuple
toc: true
date: 2018-06-11 22:58:32
---

# 可以补充进来的


# 元组 tuple


元组与列表类似，不同之处在于元组的元素不能修改。

不可变的 tuple 有什么意义？

因为 tuple 不可变，所以代码更安全。如果可能，能用 tuple 代替 list 就尽量用 tuple。如果一组数据是固定不变的，还是最好创建为 tuple 。

元组使用小括号，列表使用方括号。

## 创建元组

```py
tup1 = ()
tup2 = ('aaa')
tup3 = ('aaa',)
tup4 = ('physics', 'chemistry', 1997, 2000)
tup5 = (1, 2, 3, 4, 5 )
tup6 = "a", "b", "c", "d"

print(tup1)
print(tup2)
print(tup3)
print(tup4)
print(tup5)
print(tup6)
```

创建空元组

```
()
aaa
('aaa',)
('physics', 'chemistry', 1997, 2000)
(1, 2, 3, 4, 5)
('a', 'b', 'c', 'd')
```

说明：

- 注意到，`('aaa')` 实际输出的是一个字符串。因此，当元组中只包含一个元素时，需要在元素后面添加逗号，传参的时候经常使用。


## 访问元组


元组可以使用下标索引来访问元组中的值，如下实例:


```py
tup1 = ('physics', 'chemistry', 1997, 2000);
tup2 = (1, 2, 3, 4, 5, 6, 7 );

print "tup1[0]: ", tup1[0]
print "tup2[1:5]: ", tup2[1:5]
```


以上实例输出结果：

```
tup1[0]:  physics
tup2[1:5]:  (2, 3, 4, 5)
```


## 把其他序列或迭代器转换为元组


```py
print(tuple([4, 0, 2]))
print(tuple('string'))
```

输出：

```
(4, 0, 2)
('s', 't', 'r', 'i', 'n', 'g')
```


## 用元素的 unpack 来交换变量

```
b, a = a, b
```

这样的交换更简洁一些。


## 修改元组

元组中的元素值是不允许修改的，但我们可以对元组进行连接组合，创建一个新的元组。

如下:


```py
tup1 = (12, 34.56);
tup2 = ('abc', 'xyz');

# 以下修改元组元素操作是非法的。
# tup1[0] = 100;

# 创建一个新的元组
tup3 = tup1 + tup2;
print(tup3)
```


以上实例输出结果：

```
(12, 34.56, 'abc', 'xyz')
```

## 删除元组


元组中的元素值是不允许删除的，但我们可以使用 del 语句来删除整个元组，如下实例:

```py
tup = ('physics', 'chemistry', 1997, 2000)

print tup
del tup
print "After deleting tup : "
print tup
```


以上实例元组被删除后，输出变量会有异常信息。

输出如下：

```py
('physics', 'chemistry', 1997, 2000)
After deleting tup :
Traceback (most recent call last):
File "test.py", line 9, in <module>
  print tup;
NameError: name 'tup' is not defined
```




## 元组运算符


与字符串一样，元组之间可以使用 + 号和 * 号进行运算。这就意味着他们可以组合和复制，运算后会生成一个新的元组。


| 表达式               | 结果                           | 描述 |
| ---------------------------- | ---------------------------- | ------------ |
| `len((1, 2, 3))`               | 3                            | 计算元素个数 |
| `(1, 2, 3) + (4, 5, 6)`        | (1, 2, 3, 4, 5, 6)           | 连接         |
| `['Hi!'] * 4`                  | ['Hi!', 'Hi!', 'Hi!', 'Hi!'] | 复制         |
| `3 in (1, 2, 3)`               | True                         | 元素是否存在 |
| `for x in (1, 2, 3): print x` | 1 2 3                        | 迭代         |




## 无关闭分隔符


任意无符号的对象，以逗号隔开，默认为元组，如下实例：


```py
print 'abc', -4.24e93, 18+6.6j, 'xyz';
x, y = 1, 2;
print "Value of x , y : ", x,y;
```


以上实例允许结果：

```
abc -4.24e+93 (18+6.6j) xyz
Value of x , y : 1 2
```



# 元组内置函数

Python 元组包含了以下内置函数

| 序号 | 方法               | 描述                   |
| ---- | ------------------ | ---------------------- |
|   1   | `cmp(tuple1,tuple2)` | 比较两个元组元素       |
|   2   | `len(tuple)`         | 计算元组元素个数。     |
|   3   | `max(tuple)`        | 返回元组中元素最大值。 |
|   4   | `min(tuple)`         | 返回元组中元素最小值。 |
|   5   | `tuple(seq)`         | 将列表转换为元组。     |


## tuple 中包含 list 时候的内容修改限制

最后来看一个 “可变的” tuple：

```py
>>> t = ('a', 'b', ['A', 'B'])
>>> t[2][0] = 'X'
>>> t[2][1] = 'Y'
>>> t
('a', 'b', ['X', 'Y'])
```

这个 tuple 定义的时候有 3 个元素，分别是`'a'`，`'b'`和一个 list。不是说 tuple 一旦定义后就不可变了吗？怎么后来又变了？

别急，我们先看看定义的时候 tuple 包含的 3 个元素：

![](http://images.iterate.site/blog/image/20181206/b7dKEBhP6AET.png?imageslim){ width=55% }

当我们把 list 的元素`'A'`和`'B'`修改为`'X'`和`'Y'`后，tuple变为：

![](http://images.iterate.site/blog/image/20181206/0g3Hjt7M7F2f.png?imageslim){ width=55% }

表面上看，tuple 的元素确实变了，但其实变的不是 tuple 的元素，而是 list 的元素。tuple 一开始指向的 list 并没有改成别的 list，所以，tuple所谓的“不变”是说，tuple的每个元素，指向永远不变。即指向`'a'`，就不能改成指向`'b'`，指向一个 list，就不能改成指向其他对象，但指向的这个 list 本身是可变的！<span style="color:red;">嗯，是要注意的，tuple 所谓的不变是指：tuple 的每个元素，指向永远不变。</span>

理解了“指向不变”后，要创建一个内容也不变的 tuple 怎么做？那就必须保证 tuple 的每一个元素本身也不能变。<span style="color:red;">是的，这种细节还是要明确的。</span>





# 相关

- [Python基础教程 w3cschool](https://www.w3cschool.cn/Python/)
- [Python 3 教程 菜鸟教程](http://www.runoob.com/Python3/Python3-tutorial.html)
- [使用 list 和 tuple](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/0014316724772904521142196b74a3f8abf93d8e97c6ee6000)















* * *





# COMMENT
NT
