---
title: Python大神秘籍Top10
toc: true
date: 2019-11-17
---
# Python大神秘籍Top10

Python神奇方法是指一些允许在自定义类中增加“神奇”功能的方法。而在Python官方文档中，有关这些方法的介绍或描述不仅内容分散，而且组织结构也相对松散。本文便对Python神奇方法做了系统的梳理。对于初学者亦或Python行家，都或多或少的会有些帮助。

![img](https://ask.qcloudimg.com/http-save/yehe-1366542/mbsw1llxal.jpeg?imageView2/2/w/1620)


**Python神奇指南目录**

1. 简介
2. 搭建与初始化
3. 在自定义类中运用操作符       神奇方法比较       神奇方法数字
4. 描述自定义类
5. 控制属性访问
6. 制作自定义序列
7. 反射
8. 可调用对象
9. 上下文管理器
10. 构建描述符对象

**简介**

何为神奇方法呢？**它们是面向Python中的一切，是一些特殊的方法允许在自己的定义类中定义增加“神奇”的功能。**它们总是使用双下划线（比如__init__或__lt__），但它们的文档没有很好地把它们表现出来。所有这些神奇方法都出现在Python的官方文档中，但内容相对分散，组织结构也显得松散。还有你会难以发现一个实例（虽然他们被设计很棒，在语言参考中被详细描述，可之后就会伴随着枯燥的语法描述等）。

为了弥补Python官方文档的这些缺陷，作者整理了这篇有关magic method的文章，旨在用作教程、复习或参考文档。

**搭建与初始化**

相信大家都熟悉这个最基础的神奇方法__init__。它令你能自定义一个对象的初始化行为。而当我调用x=SomeClass()时，__init__并不是最先被调用的。实际上有一个叫做__new__的方法，事实上是它创建了实例，它传递任何参数给初始化程序来达到创建的目的。在对象生命周期结束时，调用__del__。让我们更近地观察下这3个神奇方法吧：

```javascript
__new__(cls, […)
```

一个对象的实例化时__new__是第一个被调用的方法。在类中传递其他任何参数到__init__。__new__很少被使用，这样做确实有其目的，特别是当一个子类继承一个不可改变的类型(一个元组或一个字符串)时。

```javascript
__init__(self, […)
```

类的初始化。创建对象后，Python解释器默认调用__init__()方法。无论主构造函数调用什么，它都会被传递。__init__几乎在Python类定义中普遍使用。

```javascript
__del__(self)
```

如果__new__和__init__构成了对象的构造函数，__ del__就是析构函数。当删除一个对象时，Python解释器也会默认调用__del__()方法。在Python中，对于开发者来说很少会直接销毁对象(如果需要，应该使用del关键字销毁)。Python的内存管理机制能够很好的胜任这份工作。也就是说,不管是手动调用del还是由Python自动回收都会触发__del__方法执行。

如下，是是__init__和__del__的例子：

```javascript
from os.path import join
class FileObject:
   '''对文件对象的包装，确保文件在关闭时得到删除'''

   def __init__(self, filepath='~', filename='sample.txt'):
       # 按filepath，读写模式打开名为filename的文件
       self.file=open(join(filepath,filename), 'r+')

   def __del__(self):
       self.file.close()
       del self.file
```

**在自定义类中运用操作符**

**神奇方法比较：**

Python有一大堆magic method，旨在使用运算符实现对象之间的直观比较，而非别扭的方法调用。它们还提供了一种方法来覆盖用于对象比较的默认Python行为。下面是这些方法的列表以及它们的作用:

```javascript
__cmp__(self, other)
```

__cmp__是神奇方法中最基础的一个。实际上它实现所有比较操作符行为(<,==,!=,等)，但它有可能不按你想要的方法工作(例如，一个实例是否等于另一个这取决于比较的准则，以及一个实例是否大于其他的这也取决于其他的准则)。如果self < other，那__cmp__应当返回一个负整数；如果self == other，则返回0；如果self > other，则返回正整数。它通常是最好的定义，而不需要你一次就全定义好它们，但当你需要用类似的准则进行所有的比较时，__cmp__会是一个很好的方式，帮你节省重复性和提高明确度。

```javascript
__eq__(self, other)
```

定义了相等操作符，==的行为。

```javascript
__ne__(self, other)
```

定义了不相等操作符，!=的行为。

```javascript
__lt__(self, other)
```

定义了小于操作符，<的行为。

```javascript
__gt__(self, other)
```

定义了大于操作符，>的行为。

```javascript
__le__(self, other)
```

定义了小于等于操作符，<=的行为。

```javascript
__ge__(self, other)
```

定义了大于等于操作符，>=的行为。

举一个例子，设想对单词进行类定义。我们可能希望按照内部对字符串的默认比较行为，即字典序(通过字母)来比较单词，也希望能够基于某些其他的准则，像是长度或音节数。在本例中，我们通过单词长度排序，以下给出实现：

```javascript
class Word(str):
  '''单词类，比较定义是基于单词长度的'''

   def __new__(cls, word):
       # 注意，我们使用了__new__,这是因为str是一个不可变类型，
       # 所以我们必须更早地初始化它（在创建时）
       if ' ' in word:
           print "单词内含有空格，截断到第一部分"
           word = word[:word.index(' ')] # 在出现第一个空格之前全是字符了现在
       return str.__new__(cls, word)

   def __gt__(self, other):
       return len(self) > len(other)
   def __lt__(self, other):
       return len(self) < len(other)
   def __ge__(self, other):
       return len(self) >= len(other)
   def __le__(self, other):
       return len(self) <= len(other)
```

**神奇方法数字：**

就像你可以通过重载比较操作符的途径来创建你自己的类实例，你同样可以重载数字操作符。

**一元操作符：**

一元运算和函数仅有一个操作数，比如负数，绝对值等。

```javascript
__pos__(self)
```

实现一元正数的行为(如：+some_object)

```javascript
__neg__(self)
```

实现负数的行为(如: -some_object)

```javascript
__abs__(self)
```

实现内建abs()函数的行为

```javascript
__invert__(self)
```

实现用~操作符进行的取反行为。

**常规算数操作符：**

现在我们涵盖了基本的二元运算符:+，-，*等等。其中大部分都是不言自明的。

```javascript
__add__(self, other)
```

实现加法

```javascript
__sub__(self, other)
```

实现减法

```javascript
__mul__(self, other)
```

实现乘法

```javascript
__floordiv__(self, other)
```

实现地板除法，使用//操作符

```javascript
__div__(self, other)
```

实现传统除法，使用/操作符

```javascript
__truediv__(self, other)
```

实现真正除法。注意，只有当你from __future__ import division时才会有效

```javascript
__mod__(self, other)
```

实现求模，使用%操作符

```javascript
__divmod__(self, other)
```

实现内建函数divmod()的行为

```javascript
__pow__(self, other)
```

实现乘方，使用**操作符

```javascript
__lshift__(self, other)
```

实现左按位位移，使用<<操作符

```javascript
__rshift__(self, other)
```

实现右按位位移，使用>>操作符

```javascript
__and__(self, other)
```

实现按位与，使用&操作符

```javascript
__or__(self, other)
```

实现按位或，使用|操作符

```javascript
__xor__(self, other)
```

实现按位异或，使用^操作符

**反射算数操作符：**

首先举个例子：some_object + other。这是“常规的”加法。而反射其实相当于一回事，除了操作数改变了改变下位置：other + some_object。在大多数情况下，反射算术操作的结果等价于常规算术操作，所以你尽可以在刚重载完__radd__就调用__add__。干脆痛快：

```javascript
__radd__(self, other)
```

实现反射加法

```javascript
__rsub__(self, other)
```

实现反射减法

```javascript
__rmul__(self, other)
```

实现反射乘法

```javascript
__rfloordiv__(self, other)
```

实现反射地板除，用//操作符

```javascript
__rdiv__(self, other)
```

实现传统除法，用/操作符

```javascript
__rturediv__(self, other)
```

实现真实除法，注意，只有当你from __future__ import division时才会有效

```javascript
__rmod__(self, other)
```

实现反射求模，用%操作符

```javascript
__rdivmod__(self, other)
```

实现内置函数divmod()的长除行为，当调用divmod(other,self)时被调用

```javascript
__rpow__(self, other)
```

实现反射乘方，用**操作符

```javascript
__rlshift__(self, other)
```

实现反射的左按位位移，使用<<操作符

```javascript
__rrshift__(self, other)
```

实现反射的右按位位移，使用>>操作符

```javascript
__rand__(self, other)
```

实现反射的按位与，使用&操作符

```javascript
__ror__(self, other)
```

实现反射的按位或，使用|操作符

```javascript
__rxor__(self, other)
```

实现反射的按位异或，使用^操作符

**增量赋值：**

Python也有各种各样的神奇方法允许用户自定义增量赋值行为。

这些方法都不会有返回值，因为赋值在Python中不会有任何返回值。反而它们只是改变类的状态。列表如下：

```javascript
__rxor__(self, other)
```

实现加法和赋值

```javascript
__isub__(self, other)
```

实现减法和赋值

```javascript
__imul__(self, other)
```

实现乘法和赋值

```javascript
__ifloordiv__(self, other)
```

实现地板除和赋值，用//=操作符

```javascript
__idiv__(self, other)
```

实现传统除法和赋值，用/=操作符

```javascript
__iturediv__(self, other)
```

实现真实除法和赋值，注意，只有当你from __future__ import division时才会有效

```javascript
__imod__(self, other)
```

实现求模和赋值，用%=操作符

```javascript
__ipow__(self, other)
```

实现乘方和赋值，用**=操作符

```javascript
__ilshift__(self, other)
```

实现左按位位移和赋值，使用<<=操作符

```javascript
__irshift__(self, other)
```

实现右按位位移和赋值，使用>>=操作符

```javascript
__iand__(self, other)
```

实现按位与和赋值，使用&=操作符

```javascript
__ior__(self, other)
```

实现按位或和赋值，使用|=操作符

```javascript
__ixor__(self, other)
```

实现按位异或和赋值，使用^=操作符

**类型转换的神奇方法：**

Python也有一组神奇方法被设计用来实现内置类型转换函数的行为，如float()。

```javascript
__int__(self)
```

实现到int的类型转换

```javascript
__long__(self)
```

实现到long的类型转换

```javascript
__float__(self)
```

实现到float的类型转换

```javascript
__complex__(self)
```

实现到复数的类型转换

```javascript
__oct__(self)
```

实现到8进制的类型转换

```javascript
__hex__(self)
```

实现到16进制的类型转换

```javascript
__index__(self)
```

实现一个当对象被切片到int的类型转换。如果你自定义了一个数值类型，考虑到它可能被切片，所以你应该重载__index__。

```javascript
__trunc__(self)
```

当math.trunc(self)被调用时调用。__trunc__应当返回一个整型的截断，(通常是long)。

```javascript
__coerce__(self, other)
```

该方法用来实现混合模式的算术。如果类型转换不可能那__coerce__应当返回None。否则，它应当返回一对包含self和other（2元组），且调整到具有相同的类型。

**描述自定义类**

用一个字符串来说明一个类这通常是有用的。在Python中提供了一些方法让你可以在你自己的类中自定义内建函数返回你的类行为的描述。

```javascript
__str__(self)
```

当你定义的类中一个实例调用了str()，用于给它定义行为

```javascript
__repr__(self)
```

当你定义的类中一个实例调用了repr()，用于给它定义行为。str()和repr()主要的区别在于它的阅读对象。repr()产生的输出主要为计算机可读(在很多情况下，这甚至可能是一些有效的Python代码)，而str()则是为了让人类可读。

```javascript
__unicode__(self)
```

当你定义的类中一个实例调用了unicode()，用于给它定义行为。unicode()像是str(),只不过它返回一个unicode字符串。警惕！如果用户用你的类中的一个实例调用了str(),而你仅定义了__unicode__(),那它是不会工作的。以防万一，你应当总是定义好__str__()，哪怕用户不会使用unicode。

```javascript
__hash__(self)
```

当你定义的类中一个实例调用了hash()，用于给它定义行为。它必须返回一个整型，而且它的结果是用于来在字典中作为快速键比对。

```javascript
__nonzero__(self)
```

当你定义的类中一个实例调用了bool()，用于给它定义行为。返回True或False，取决于你是否考虑一个实例是True或False的。

我们已经相当漂亮地干完了神奇方法无聊的部分(无示例)，至此我们已经讨论了一些基础的神奇方法，是时候让我们向高级话题移动了。

**控制属性访问**

Python通过神奇的方法实现了大量的封装，而不是通过明确的方法或字段修饰符。例如：

```javascript
__getattr__(self, name)
```

你可以为用户在试图访问不存在（不论是存在或尚未建立）的类属性时定义其行为。这对捕捉和重定向常见的拼写错误，给出使用属性警告是有用的（只要你愿意，你仍旧可选计算，返回那个属性）或抛出一个AttributeError异常。这个方法只适用于访问一个不存在的属性，所以，这不算一个真正封装的解决之道。

```javascript
__setattr__(self, name, value)
```

不像__getattr__，__setattr__是一个封装的解决方案。它允许你为一个属性赋值时候的行为，不论这个属性是否存在。这意味着你可以给属性值的任意变化自定义规则。然而，你需要在意的是你要小心使用__setattr__,在稍后的列表中会作为例子给出。

```javascript
__delattr__
```

这等价于__setattr__,但是作为删除类属性而不是set它们。它需要相同的预防措施，就像__setattr__，防止无限递归（当在__delattr__中调用del self.name会引起无限递归）。

```javascript
__getattribute__(self, name)
```

__getattribute__良好地适合它的同伴们__setattr__和__delattr__。可我却不建议你使用它。

__getattribute__只能在新式类中使用（在Python的最新版本中，所有的类都是新式类，在稍旧的版本中你可以通过继承object类来创建一个新式类。它允许你定规则，在任何时候不管一个类属性的值那时候是否可访问的。）它会因为他的同伴中的出错连坐受到某些无限递归问题的困扰（这时你可以通过调用基类的__getattribute__方法来防止发生）。当__getattribute__被实现而又只调用了该方法如果__getattribute__被显式调用或抛出一个AttributeError异常，同时也主要避免了对__getattr__的依赖。这个方法可以使用，不过我不推荐它是因为它有一个小小的用例(虽说比较少见，但我们需要特殊行为以获取一个值而不是赋值)以及它真的很难做到实现0bug。

你可以很容易地在你自定义任何类属性访问方法时引发一个问题。参考这个例子：

```javascript
def __setattr__(self, name, value):
   self.name = value
   # 当每次给一个类属性赋值时，会调用__setattr__(),这就形成了递归
   # 因为它真正的含义是 self.__setattr__('name', value)
   # 所以这方法不停地调用它自己，变成了一个无法退出的递归最终引发crash

def __setattr__(self, name, value):
   self.__dict__[name] = value # 给字典中的name赋值
   # 在此自定义行为
```

以下是一个关于特殊属性访问方法的实际例子（注意，我们使用super因为并非所有类都有__dict__类属性）：

```javascript
class AccessCounter:
   '''一个类包含一个值和实现了一个访问计数器。
   当值每次发生变化时，计数器+1'''

   def __init__(self, val):
       super(AccessCounter, self).__setattr__('counter',0)
       super(AccessCounter, self).__setattr__('value', val)

   def __setattr__(self, name, value):
       if name == 'value':
           super(AccessCounter, self).__setattr__('counter', self.counter + 1)
       # Make this unconditional.
       # 如果你想阻止其他属性被创建，抛出AttributeError(name)异常
       super(AccessCounter, self).__setattr__(name, value)

   def __delattr__(self, name)
       if name == 'value':
           super(AccessCounter, self).__setattr__('counter', self.counter + 1)
       super(AccessCounter, self).__delattr__(name)
```

**制作自定义序列**

很有多种方式可以让你的类表现得像内建序列(字典，元组，列表，字符串等)。这些是我迄今为止最喜欢的神奇方法了，因为不合理的控制它们赋予了你一种魔术般地让你的类实例整个全局函数数组漂亮工作的方式。

```javascript
__len__(self)
```

返回容器的长度。部分protocol同时支持可变和不可变容器

```javascript
__getitem__(self, key)
```

定义当某一个item被访问时的行为，使用self[key]表示法。这个同样也是部分可变和不可变容器protocol。这也可抛出适当的异常:TypeError 当key的类型错误，或没有值对应Key时。

```javascript
__setitem__(self, key, value)
```

定义当某一个item被赋值时候的行为，使用self[key]=value表示法。这也是部分可变和不可变容器protocol。再一次重申，你应当在适当之处抛出KeyError和TypeError异常。

```javascript
__delitem__(self, key)
```

定义当某一个item被删除（例如 del self[key]）时的行为。这仅是部分可变容器的protocol。在一个无效key被使用后，你必须抛出一个合适的异常。

```javascript
__iter__(self)
```

应该给容器返回一个迭代器。迭代器会返回若干内容,大多使用内建函数iter()表示。当一个容器使用形如for x in container:的循环。迭代器本身就是其对象，同时也要定义好一个__iter__方法来返回自身。

```javascript
__reversed__(self)
```

当定义调用内建函数reversed()时的行为。应该返回一个反向版本的列表。

```javascript
__contains__(self, item)
```

__contains__为成员关系，用in和not in测试时定义行为。那你会问这个为何不是一个序列的protocol的一部分？这是因为当__contains__未定义，Python就会遍历序列，如果遇到正在寻找的item就会返回True。

```javascript
__concat__(self, other)
```

最后，你可通过__concat__定义你的序列和另外一个序列的连接。应该从self和other返回一个新构建的序列。当调用2个序列时__concat__涉及操作符+

在我们的例子中，让我们看一下一个list实现的某些基础功能性的构建。可能会让你想起你使用的其他语言（比如Haskell）。

```javascript
class FunctionalList:
   '''类覆盖了一个list的某些额外的功能性魔法，像head，
   tail，init，last，drop，and take'''
   def __init__(self, values=None):
       if values is None:
           self.values = []
       else:
           self.values = values

   def __len__(self):
       return len(self.values)

   def __getitem__(self, key):
       # 如果key是非法的类型和值，那么list valuse会抛出异常
       return self.values[key]

   def __setitem__(self, key, value):
       self.values[key] = value

   def __delitem__(self, key):
       del self.values[key]

   def __iter__(self):
       return iter(self.values)

   def __reversed__(self):
       return reversed(self.values)

   def append(self, value):
       self.values.append(value)
   def head(self):
       # 获得第一个元素
       return self.values[0]
   def tail(self):
       # 获得在第一个元素后的其他所有元素
       return self.values[1:]
   def init(self):
       # 获得除最后一个元素的序列
       return self.values[:-1]
   def last(last):
       # 获得最后一个元素
       return self.values[-1]
   def drop(self, n):
       # 获得除前n个元素的序列
       return self.values[n:]
   def take(self, n):
       # 获得前n个元素
       return self.values[:n]
```

**反射**

你也可以通过定义神奇方法来控制如何反射使用内建函数isinstance()和issubclass()的行为。这些神奇方法是：

```javascript
__instancecheck__(self, instance)
```

检查一个实例是否是你定义类中的一个实例(比如，isinstance(instance, class))

```javascript
__subclasscheck__(self, subclass)
```

检查一个类是否是你定义类的子类（比如，issubclass(subclass, class)）

**可调用对象**

这是Python中一个特别的神奇方法，它允许你的类实例像函数。所以你可以“调用”它们，把他们当做参数传递给函数等等。这是另一个强大又便利的特性让Python的编程变得更可爱了。

```javascript
__call__(self, [args…])
```

允许类实例像函数一样被调用。本质上，这意味着x()等价于x.__call__()。注意，__call__需要的参数数目是可变的，也就是说可以对任何函数按你的喜好定义参数的数目定义__call__。

__call__可能对于那些经常改变状态的实例来说是极其有用的。“调用”实例是一种顺应直觉且优雅的方式来改变对象的状态。下面一个例子是一个类表示一个实体在一个平面上的位置：

```javascript
class Entity:
   '''描述实体的类，被调用的时候更新实体的位置'''

   def __init__(self, size, x, y):
       self.x, self.y = x, y
       self.size = size

   def __call__(self, x, y):
       '''改变实体的位置'''
       self.x, self.y = x, y

   #省略...
```

**上下文管理器**

上下文管理允许对对象进行设置和清理动作，用with声明进行已经封装的操作。上下文操作的行为取决于2个神奇方法：

```javascript
__enter__(self)
```

定义块用with声明创建出来时上下文管理应该在块开始做什么。

```javascript
__exit__(self,  exception_type, exception_value, traceback)
```

定义在块执行（或终止）之后上下文管理应该做什么。

你也可以使用这些方法去创建封装其他对象通用的上下文管理。看下面的例子：

```javascript
class Closer:
   '''用with声明一个上下文管理用一个close方法自动关闭一个对象'''

   def __init__(self, obj):
       self.obj = obj

   def __enter__(self):
       return self.obj # 绑定目标

   def __exit__(self, exception_type, exception_val, trace):
       try:
           self.obj.close()
       except AttributeError: #obj不具备close
           print 'Not closable.'
           return True # 成功处理异常
```

以下是一个对于Closer实际应用的一个例子，使用一个FTP连接进行的演示（一个可关闭的套接字）：

```javascript
>>> from magicmethods import Closer
>>> from ftplib import :;;
>>> with Closer(FTP('ftp.somsite.com')) as conn:
...     conn.dir()
...
# 省略的输出
>>> conn.dir()
# 一个很长的AttributeError消息， 不能关闭使用的一个连接
>>> with Closer(int(5)) as i:
...     i += 1
...
Not closeable.
>>> i
6
```

**构建描述符对象**

描述符可以改变其他对象，也可以是访问类中任一的getting,setting,deleting。

作为一个描述符，一个类必须至少实现__get__,__set__,和__delete__中的一个。让我们快点看一下这些神奇方法吧：

```javascript
__get__(self, instance, owner)
```

当描述符的值被取回时定义其行为。instance是owner对象的一个实例，owner是所有类。

```javascript
__set__(self, instance, value)
```

当描述符的值被改变时定义其行为。instance是owner对象的一个实例，value是设置的描述符的值

```javascript
__delete__(self, instance)
```

当描述符的值被删除时定义其行为。instance是owner对象的一个实例。

现在，有一个有用的描述符应用例子：单位转换策略

```javascript
class Meter(object):
   '''米描述符'''

   def __init__(self, value=0.0):
       self.value = float(value)
   def __get__(self, instance, owner):
       return self.value
   def __set__(self, instance, value):
       self.value = float(value)

class Foot(object):
   '''英尺描述符'''

   def __get__(self, instance, owner):
       return instance.meter * 3.2808
   def __set__(self, instance, value):
       instance.meter = float(value) / 3.2808

class Distance(object):
   '''表示距离的类，控制2个描述符：feet和meters'''
   meter = Meter()
   foot = Foot()
```

**总结**

这份指南的目标就是让任何人都能读懂它，不管读者们是否具备Python或面向对象的编程经验。如果你正准备学习Python，那你已经获得了编写功能丰富、优雅、易用的类的宝贵知识。如果你是一名中级Python程序员，你有可能已经拾起了一些概念、策略和一些好的方法来减少你编写的代码量。如果你是一名Python专家，你可能已经回顾了某些你可能已经遗忘的知识点，或者你又又有一些新的发现。不管你的经验等级如何，希望你在这次Python神奇方法之旅中有所收获！

原文链接：

https://rszalski.github.io/magicmethods/#appendix1


# 相关

- [【Python大神秘籍Top10】这些窍门99%的人都不知道](https://cloud.tencent.com/developer/article/1186749)
