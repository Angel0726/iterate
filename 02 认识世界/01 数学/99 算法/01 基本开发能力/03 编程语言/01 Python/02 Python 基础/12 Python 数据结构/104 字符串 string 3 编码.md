---
title: 104 字符串 string 3 编码
toc: true
date: 2018-11-25
---
#### 字符串和编码

### 字符编码

我们已经讲过了，字符串也是一种数据类型，但是，字符串比较特殊的是还有一个编码问题。

因为计算机只能处理数字，如果要处理文本，就必须先把文本转换为数字才能处理。最早的计算机在设计时采用 8 个比特（bit）作为一个字节（byte），所以，一个字节能表示的最大的整数就是 255（二进制 11111111=十进制 255），如果要表示更大的整数，就必须用更多的字节。比如两个字节可以表示的最大整数是`65535`，4个字节可以表示的最大整数是`4294967295`。

由于计算机是美国人发明的，因此，最早只有 127 个字符被编码到计算机里，也就是大小写英文字母、数字和一些符号，这个编码表被称为`ASCII`编码，比如大写字母`A`的编码是`65`，小写字母`z`的编码是`122`。

但是要处理中文显然一个字节是不够的，至少需要两个字节，而且还不能和 ASCII 编码冲突，所以，中国制定了`GB2312`编码，用来把中文编进去。

你可以想得到的是，全世界有上百种语言，日本把日文编到`Shift_JIS`里，韩国把韩文编到`Euc-kr`里，各国有各国的标准，就会不可避免地出现冲突，结果就是，在多语言混合的文本中，显示出来会有乱码。


因此，Unicode应运而生。Unicode把所有语言都统一到一套编码里，这样就不会再有乱码问题了。<span style="color:red;">嗯，前面的我知道，但是 Unicode 把所有的语言都统一到一套编码里，这个不知道。嗯怪不得这么常用到。</span>

Unicode标准也在不断发展，但最常用的是用两个字节表示一个字符（如果要用到非常偏僻的字符，就需要 4 个字节）。现代操作系统和大多数编程语言都直接支持 Unicode。

现在，捋一捋 ASCII 编码和 Unicode 编码的区别：ASCII编码是 1 个字节，而 Unicode 编码通常是 2 个字节。

字母`A`用 ASCII 编码是十进制的`65`，二进制的`01000001`；

字符`0`用 ASCII 编码是十进制的`48`，二进制的`00110000`，注意字符`'0'`和整数`0`是不同的；

汉字`中`已经超出了 ASCII 编码的范围，用 Unicode 编码是十进制的`20013`，二进制的`01001110 00101101`。

你可以猜测，如果把 ASCII 编码的`A`用 Unicode 编码，只需要在前面补 0 就可以，因此，`A`的 Unicode 编码是`00000000 01000001`。

新的问题又出现了：如果统一成 Unicode 编码，乱码问题从此消失了。但是，如果你写的文本基本上全部是英文的话，用 Unicode 编码比 ASCII 编码需要多一倍的存储空间，在存储和传输上就十分不划算。<span style="color:red;">嗯，是的。</span>

所以，本着节约的精神，又出现了把 Unicode 编码转化为“可变长编码”的 `UTF-8` 编码。UTF-8 编码把一个 Unicode 字符根据不同的数字大小编码成 1-6 个字节，常用的英文字母被编码成 1 个字节，汉字通常是 3 个字节，只有很生僻的字符才会被编码成 4-6 个字节。如果你要传输的文本包含大量英文字符，用 UTF-8 编码就能节省空间：<span style="color:red;">嗯，那么这个时候为什么不废除 Unicode 的使用？而是全面使用 UTF-8 ？为什么还要以 Unicode 作为通用的形式呢？</span>

| 字符 | ASCII    | Unicode           | UTF-8                      |
| ---- | -------- | ----------------- | -------------------------- |
| A    | 01000001 | 00000000 01000001 | 01000001                   |
| 中   | x        | 01001110 00101101 | 11100100 10111000 10101101 |

从上面的表格还可以发现，UTF-8 编码有一个额外的好处，就是 ASCII 编码实际上可以被看成是 UTF-8 编码的一部分，所以，大量只支持 ASCII 编码的历史遗留软件可以在 UTF-8 编码下继续工作。<span style="color:red;">嗯，没想到还有这个好处，看来这也是必须开发 UTF-8 的原因吧。 </span>

搞清楚了 ASCII、Unicode 和 UTF-8 的关系，我们就可以总结一下现在计算机系统通用的字符编码工作方式：

在计算机内存中，统一使用 Unicode 编码，当需要保存到硬盘或者需要传输的时候，就转换为 UTF-8 编码。<span style="color:red;">嗯，还是很不错的，不过，为什么内存里面不也改成 UTF-8 编码？会降低处理速度吗？</span>

用记事本编辑的时候，从文件读取的 UTF-8 字符被转换为 Unicode 字符到内存里，编辑完成后，保存的时候再把 Unicode 转换为 UTF-8 保存到文件：<span style="color:red;">这个之前不知道，也可能看到忘记了。</span>

![](http://images.iterate.site/blog/image/20181206/cjlEB0aPRXHv.png?imageslim){ width=55% }

浏览网页的时候，服务器会把动态生成的 Unicode 内容转换为 UTF-8 再传输到浏览器：<span style="color:red;">嗯。</span>

![](http://images.iterate.site/blog/image/20181206/2I90IAH1BRzV.png?imageslim){ width=55% }

所以你看到很多网页的源码上会有类似 `<meta charset="UTF-8" />` 的信息，表示该网页正是用的 UTF-8 编码。<span style="color:red;">嗯。</span>

### Python的字符串

搞清楚了令人头疼的字符编码问题后，我们再来研究 Python 的字符串。

在最新的 Python 3版本中，字符串是以 Unicode 编码的，也就是说，Python的字符串支持多语言，例如：<span style="color:red;">这个地方，想问下，字符串以 Unicode 编码到底是什么意思？</span>

```
>>> print('包含中文的 str')
包含中文的 str
```

对于单个字符的编码，Python提供了 `ord()` 函数获取字符的整数表示，`chr()` 函数把编码转换为对应的字符：<span style="color:red;">嗯，以前好像看到过 `ord` 和 `chr` ，但是还从来没用过，这个一般什么时候会使用？</span><span style="color:blue;">这个厉害了，之前，想获得 opencv 的 `waitkey(0)` 的返回的值是什么 key 的时候，一直不知道该怎么办，查了 ASCII 表，然后才对应的，实际上可以使用 `chr` 来得到的，没想到，这个知识点之前完全忘记了。</span>

```py
>>> ord('A')
65
>>> ord('中')
20013
>>> chr(66)
'B'
>>> chr(25991)
'文'
```

如果知道字符的整数编码，还可以用十六进制这么写`str`：

```py
>>> '\u4e2d\u6587'
'中文'
```

<span style="color:red;">嗯，原来上面这个 \u 的十六进制的这种格式就是 Unicode 格式，这个在网站接口的中传中文的时候还是经常看到的。</span>

两种写法完全是等价的。

由于 Python 的字符串类型是`str`，在内存中以 Unicode 表示，一个字符对应若干个字节。如果要在网络上传输，或者保存到磁盘上，就需要把`str`变为以字节为单位的`bytes`。<span style="color:red;">为什么要把 str 变成以字节为单位的 bytes？是因为 str 的几个字节还是要存成一个一个的字节的吗？</span>

Python对`bytes`类型的数据用带`b`前缀的单引号或双引号表示：

```py
x = b'ABC'
```

要注意区分`'ABC'`和`b'ABC'`，前者是`str`，后者虽然内容显示得和前者一样，但`bytes`的每个字符都只占用一个字节。<span style="color:red;">嗯，是这样吗？那么它的编码是 ASCII 吗？对于 byte 感觉还是有点不是很理解，到底为什么要有 byte 这个数据类型？一般用在什么场景下？</span>

以 Unicode 表示的`str`通过`encode()`方法可以编码为指定的`bytes`，例如：

```py
>>> 'ABC'.encode('ascii')
b'ABC'
>>> '中文'.encode('utf-8')
b'\xe4\xb8\xad\xe6\x96\x87'
>>> '中文'.encode('ascii')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
UnicodeEncodeError: 'ascii' codec can't encode characters in position 0-1: ordinal not in range(128)
```

纯英文的`str`可以用`ASCII`编码为`bytes`，内容是一样的，含有中文的`str`可以用`UTF-8`编码为`bytes`。含有中文的`str`无法用`ASCII`编码，因为中文编码的范围超过了`ASCII`编码的范围，Python会报错。

在`bytes`中，无法显示为 ASCII 字符的字节，用`\x##`显示。

反过来，如果我们从网络或磁盘上读取了字节流，那么读到的数据就是`bytes`。要把`bytes`变为`str`，就需要用`decode()`方法：

```
>>> b'ABC'.decode('ascii')
'ABC'
>>> b'\xe4\xb8\xad\xe6\x96\x87'.decode('utf-8')
'中文'
```

如果`bytes`中包含无法解码的字节，`decode()`方法会报错：

```
>>> b'\xe4\xb8\xad\xff'.decode('utf-8')
Traceback (most recent call last):
  ...
UnicodeDecodeError: 'utf-8' codec can't decode byte 0xff in position 3: invalid start byte
```

如果`bytes`中只有一小部分无效的字节，可以传入`errors='ignore'`忽略错误的字节：

```
>>> b'\xe4\xb8\xad\xff'.decode('utf-8', errors='ignore')
'中'
```

要计算`str`包含多少个字符，可以用`len()`函数：

```
>>> len('ABC')
3
>>> len('中文')
2
```

`len()`函数计算的是`str`的字符数，如果换成`bytes`，`len()`函数就计算字节数：

```
>>> len(b'ABC')
3
>>> len(b'\xe4\xb8\xad\xe6\x96\x87')
6
>>> len('中文'.encode('utf-8'))
6
```

可见，1个中文字符经过 UTF-8编码后通常会占用 3 个字节，而 1 个英文字符只占用 1 个字节。

在操作字符串时，我们经常遇到`str`和`bytes`的互相转换。为了避免乱码问题，应当始终坚持使用 UTF-8编码对`str`和`bytes`进行转换。

由于 Python 源代码也是一个文本文件，所以，当你的源代码中包含中文的时候，在保存源代码时，就需要务必指定保存为 UTF-8编码。当 Python 解释器读取源代码时，为了让它按 UTF-8编码读取，我们通常在文件开头写上这两行：

```
#!/usr/bin/env Python3
# -*- coding: utf-8 -*-
```

第一行注释是为了告诉 Linux/OS X系统，这是一个 Python 可执行程序，Windows系统会忽略这个注释；

第二行注释是为了告诉 Python 解释器，按照 UTF-8编码读取源代码，否则，你在源代码中写的中文输出可能会有乱码。

申明了 UTF-8编码并不意味着你的`.py`文件就是 UTF-8编码的，必须并且要确保文本编辑器正在使用 UTF-8 without BOM编码：

![](http://images.iterate.site/blog/image/20181206/SFvJQX5tRoKS.png?imageslim){ width=55% }

如果`.py`文件本身使用 UTF-8编码，并且也申明了`# -*- coding: utf-8 -*-`，打开命令提示符测试就可以正常显示中文：

![](http://images.iterate.site/blog/image/20181206/fNVIVJloqei1.png?imageslim){ width=55% }





### 小结

Python 3的字符串使用 Unicode，直接支持多语言。

当`str`和`bytes`互相转换时，需要指定编码。最常用的编码是`UTF-8`。Python当然也支持其他编码方式，比如把 Unicode 编码成`GB2312`：

```py
print('中文'.encode('gb2312'))
```

输出：

```
b'\xd6\xd0\xce\xc4'
```


但这种方式纯属自找麻烦，如果没有特殊业务要求，请牢记仅使用`UTF-8`编码。<span style="color:red;">嗯。</span>

格式化字符串的时候，可以用 Python 的交互式环境测试，方便快捷。







## Unicode 字符串


Python 中定义一个 Unicode 字符串和定义一个普通字符串一样简单：


    >>> u'Hello World !'
    u'Hello World !'



引号前小写的"u"表示这里创建的是一个 Unicode 字符串。如果你想加入一个特殊字符，可以使用 Python 的 Unicode-Escape 编码。如下例所示：


    >>> u'Hello\u0020World !'
    u'Hello World !'



被替换的 \u0020 标识表示在给定位置插入编码值为 0x0020 的 Unicode 字符（空格符）。


# 相关

- [字符串和编码](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001431664106267f12e9bef7ee14cf6a8776a479bdec9b9000)
