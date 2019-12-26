---
title: 007 使用 type isinstance dir 获取对象信息
toc: true
date: 2019-02-07
---
# 可以补充进来的


# 获取对象信息

当我们拿到一个对象的引用时，如何知道这个对象是什么类型、有哪些方法呢？

## `type()`

`type()` 可以用来判断对象类型。

基本类型都可以用 `type()` 判断：

```py
import types

def fn():
    pass

class Animal(object):
    def run(self):
        print('Animal is running...')
    def eat(self):
        print('Animal is eating...')


print(type(123))
print(type(123)==int)
print(type('str'))
print(type('abc')==str)
print(type(None))

print(type(abs))
print(type(abs)==types.BuiltinFunctionType)
print(type(fn))
print(type(fn)==types.FunctionType)
print(type(lambda x: x))
print(type(lambda x: x)==types.LambdaType)
print(type((x for x in range(10))))
print(type((x for x in range(10)))==types.GeneratorType)


a=Animal()
print(type(Animal))
print(a)
```

输出：

```
<class 'int'>
True
<class 'str'>
True
<class 'NoneType'>
<class 'builtin_function_or_method'>
True
<class 'function'>
True
<class 'function'>
True
<class 'generator'>
True
<class 'type'>
<__main__.Animal object at 0x000002BE7FDF10C8>
```

不清楚的：

- <span style="color:red;"> `None` 是 `NoneType` 的，但是 `NoneType` 要从哪里引用？</span>




## `isinstance()`

对于 class 的继承关系来说，使用 `type()` 就很不方便。

这时，可以使用 `isinstance()` 判断 class 的类型
。


```py
class Animal(object):
    def run(self):
        print('Animal is running...')
    def eat(self):
        print('Animal is eating...')

class Dog(Animal):
    pass

a=Dog()
print(isinstance(a, Dog))
print(isinstance(a,Animal))
```

输出：

```
True
True
```

说明：

- `isinstance()` 判断的是一个对象是否是该类型本身，或者位于该类型的父继承链上。

能用 `type()` 判断的基本类型也可以用 `isinstance()` 判断：<span style="color:red;">嗯，突然想到 `isinstance` 与 `None` 可以判定吗？`isinstance(a, type(None))` 这样来判定？好像没有找到直接写 `NoneType` 的方法。</span>

```py
print(isinstance('a', str))
print(isinstance(b'a', bytes))
print(isinstance(None,type(None)))
print(isinstance([1, 2, 3], list))
print(isinstance([1, 2, 3], (list, tuple)))
```

输出：

```
True
True
True
True
True
```

说明：

- `isinstance([1, 2, 3], (list, tuple))` 可以判断是否属于某一种类型。<span style="color:red;">嗯，之前不知道 `isinstance` 可以这样使用。</span>


建议：

- 总是优先使用 `isinstance()` 判断类型，可以将指定类型及其子类“一网打尽”。<span style="color:red;">嗯。不错的</span>

## `dir()`

如果要获得一个对象的所有属性和方法，可以使用`dir()`函数。


它返回一个包含字符串的 list，比如，获得一个 str 对象的所有属性和方法：

```py
print(dir('ABC'))
```

输出：

```
['__add__', '__class__', '__contains__', '__delattr__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__getitem__', '__getnewargs__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__iter__', '__le__', '__len__', '__lt__', '__mod__', '__mul__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__rmod__', '__rmul__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', 'capitalize', 'casefold', 'center', 'count', 'encode', 'endswith', 'expandtabs', 'find', 'format', 'format_map', 'index', 'isalnum', 'isalpha', 'isascii', 'isdecimal', 'isdigit', 'isidentifier', 'islower', 'isnumeric', 'isprintable', 'isspace', 'istitle', 'isupper', 'join', 'ljust', 'lower', 'lstrip', 'maketrans', 'partition', 'replace', 'rfind', 'rindex', 'rjust', 'rpartition', 'rsplit', 'rstrip', 'split', 'splitlines', 'startswith', 'strip', 'swapcase', 'title', 'translate', 'upper', 'zfill']
```

说明：

- 类似`__xxx__`的属性和方法在 Python 中都是有特殊用途的，比如`__len__`方法返回长度。在 Python 中，如果你调用`len()`函数试图获取一个对象的长度，实际上，在`len()`函数内部，它自动去调用该对象的`__len__()`方法。

我们自己写的类，如果也想用`len(myObj)`的话，就自己写一个`__len__()`方法：<span style="color:red;">是的，不错。之前已经看过了，虽然没怎么用过，但是感觉这样挺合理的。</span>

```py
class MyDog(object):
    def __len__(self):
        return 100


dog = MyDog()
print(len(dog))
```

输出：

```
100
```


仅仅把属性和方法列出来是不够的，配合`getattr()`、`setattr()`以及`hasattr()`，我们可以直接操作一个对象的状态：

```py
class MyObject(object):
    def __init__(self):
        self.x = 9

    def power(self):
        return self.x * self.x


obj = MyObject()

print(hasattr(obj, 'x'))
print(hasattr(obj, 'y'))  # 有属性'y'吗？
setattr(obj, 'y', 19)  # 设置一个属性'y'
print(hasattr(obj, 'y'))  # 有属性'y'吗？

print(getattr(obj, 'y'))  # 获取属性'y'
print(obj.y)

print(getattr(obj, 'z', 404)) # 获取属性'z'，如果不存在，返回默认值 404

print('---')

print(hasattr(obj, 'power'))
print(hasattr(obj, 'power')) # 有属性'power'吗？
print(getattr(obj, 'power')) # 获取属性'power'
fn = getattr(obj, 'power') # 获取属性'power'并赋值到变量 fn
print(fn) # fn指向 obj.power
print(fn()) # 调用 fn()与调用 obj.power()是一样的
```

输出：

```
True
False
True
19
19
404
---
True
True
<bound method MyObject.power of <__main__.MyObject object at 0x0000027150C3BFC8>>
<bound method MyObject.power of <__main__.MyObject object at 0x0000027150C3BFC8>>
81
```

说明：

- `getattr` 可以传入一个 default 参数，如果属性不存在，就返回默认值
- `fn = getattr(obj, 'power')` 相当于一个指针指向了对象里面的一个函数。<span style="color:red;">一般什么时候使用这个呢？</span>

注意：

只有在不知道对象信息的时候，我们才会去获取对象信息。如果可以直接写：

```py
sum = obj.x + obj.y
```

就不要写：

```py
sum = getattr(obj, 'x') + getattr(obj, 'y')
```

**一个正确的用法的例子如下**：

```py
def readImage(fp):
    if hasattr(fp, 'read'):
        return readData(fp)
    return None
```

假设我们希望从文件流 fp 中读取图像，我们首先要判断该 fp 对象是否存在 `read` 方法，如果存在，则该对象是一个流，如果不存在，则无法读取。`hasattr()`就派上了用场。

请注意，在 Python 这类动态语言中，根据鸭子类型，有`read()`方法，不代表该 fp 对象就是一个文件流，它也可能是网络流，也可能是内存中的一个字节流，但只要`read()`方法返回的是有效的图像数据，就不影响读取图像的功能。<span style="color:red;">嗯，不错。看到这里，突然想起，鸭子类型是动态类型里都有的吗？</span>



# 原文及引用

- [获取对象信息](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001431866385235335917b66049448ab14a499afd5b24db000)
