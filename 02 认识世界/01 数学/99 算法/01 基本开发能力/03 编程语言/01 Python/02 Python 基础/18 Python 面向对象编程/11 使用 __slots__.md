---
title: 11 使用 __slots__
toc: true
date: 2019-02-07
---
# 可以补充进来的

- 这个 `__slots__` 到底用在什么场景呢？

# 使用 `__slots__`


正常情况下，当我们定义了一个 class，创建了一个 class的实例后，我们可以给该实例绑定任何属性和方法，这就是动态语言的灵活性。先定义 class：<span style="color:red;">是呀，这也太灵活了，有什么约定俗称的使用规则吗？</span>

```py
class Student(object):
    pass
```

然后，尝试给实例绑定一个属性：

```py
>>> s = Student()
>>> s.name = 'Michael' # 动态给实例绑定一个属性
>>> print(s.name)
Michael
```

还可以尝试给实例绑定一个方法：

```py
>>> def set_age(self, age): # 定义一个函数作为实例方法
...     self.age = age
...
>>> from types import MethodType
>>> s.set_age = MethodType(set_age, s) # 给实例绑定一个方法
>>> s.set_age(25) # 调用实例方法
>>> s.age # 测试结果
25
```

<span style="color:red;">有点震惊！上面这个之前不知道可以这么写，有些不错，`MethodType` 为什么传入的参数是函数和对象？这个对象有必要传吗？</span>

但是，给一个实例绑定的方法，对另一个实例是不起作用的：

```py
>>> s2 = Student() # 创建新的实例
>>> s2.set_age(25) # 尝试调用方法
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'Student' object has no attribute 'set_age'
```

为了给所有实例都绑定方法，可以给 class 绑定方法：

```py
>>> def set_score(self, score):
...     self.score = score
...
>>> Student.set_score = set_score
```

<span style="color:red;">还可以这样！不错，但是感觉这样还是会使得类的方法变得混乱。除了写代码的人，其他人看的话估计要找一段时间吧？有什么约定俗称的规矩吗？</span>

给 class 绑定方法后，所有实例均可调用：

```py
>>> s.set_score(100)
>>> s.score
100
>>> s2.set_score(99)
>>> s2.score
99
```

通常情况下，上面的`set_score`方法可以直接定义在 class 中，但动态绑定允许我们在程序运行的过程中动态给 class 加上功能，这在静态语言中很难实现。<span style="color:red;">一般什么时候会要这样？在程序运行的时候动态添加功能？想知道这样使用的场景是什么。</span>

### 使用 `__slots__`

但是，如果我们想要限制实例的属性怎么办？比如，只允许对 Student 实例添加`name`和`age`属性。

为了达到限制的目的，Python允许在定义 class 的时候，定义一个特殊的`__slots__`变量，来限制该 class 实例能添加的属性：<span style="color:red;">竟然还可以这样！不错，但是为什么要限制呢？而且，这个是限制的动态添加吗？在 `__init__` 里面设定的会被限制吗？是限制类的属性还是对象的属性？有对函数进行限制的吗？</span>

```py
class Student(object):
    __slots__ = ('name', 'age') # 用 tuple 定义允许绑定的属性名称
```

然后，我们试试：

```py
>>> s = Student() # 创建新的实例
>>> s.name = 'Michael' # 绑定属性'name'
>>> s.age = 25 # 绑定属性'age'
>>> s.score = 99 # 绑定属性'score'
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'Student' object has no attribute 'score'
```

由于`'score'`没有被放到`__slots__`中，所以不能绑定`score`属性，试图绑定`score`将得到`AttributeError`的错误。

使用`__slots__`要注意，`__slots__`定义的属性仅对当前类实例起作用，对继承的子类是不起作用的：<span style="color:red;">还可以这样！那为什么还要设定 `__slots__` ？不是多此一举吗？</span>

```py
>>> class GraduateStudent(Student):
...     pass
...
>>> g = GraduateStudent()
>>> g.score = 9999
```

除非在子类中也定义`__slots__`，这样，子类实例允许定义的属性就是自身的`__slots__`加上父类的`__slots__`。<span style="color:red;">好吧，这个 `__slots__` 到底用在什么场景呢？</span>


# 原文及引用

- [使用 `__slots__`](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/00143186739713011a09b63dcbd42cc87f907a778b3ac73000)
