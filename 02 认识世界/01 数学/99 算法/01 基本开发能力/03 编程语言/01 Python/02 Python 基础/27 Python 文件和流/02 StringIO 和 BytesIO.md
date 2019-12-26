---
title: 02 StringIO 和 BytesIO
toc: true
date: 2019-02-09
---
# 可以补充进来的

- 什么时候使用 StringIO 和 BytesIO？

# StringIO和 BytesIO


很多时候，数据读写不一定是文件，也可以在内存中读写。

StringIO 顾名思义就是在内存中读写 str。

要把 str 写入 StringIO，我们需要先创建一个 StringIO，然后，像文件一样写入即可：<span style="color:red;">嗯。</span>

```py
>>> from io import StringIO
>>> f = StringIO()
>>> f.write('hello')
5
>>> f.write(' ')
1
>>> f.write('world!')
6
>>> print(f.getvalue())
hello world!
```

`getvalue()` 方法用于获得写入后的 str。

要读取 StringIO，可以用一个 str 初始化 StringIO，然后，像读文件一样读取：

```py
>>> from io import StringIO
>>> f = StringIO('Hello!\nHi!\nGoodbye!')
>>> while True:
...     s = f.readline()ddd
...     if s == '':
...         break
...     print(s.strip())
...
Hello!
Hi!
Goodbye!
```

<span style="color:red;">嗯，StringIO，一般什么时候使用这个？</span>

## BytesIO

StringIO 操作的只能是 str，如果要操作二进制数据，就需要使用 BytesIO。

BytesIO 实现了在内存中读写 bytes，我们创建一个 BytesIO，然后写入一些 bytes：

```py
>>> from io import BytesIO
>>> f = BytesIO()
>>> f.write('中文'.encode('utf-8'))
6
>>> print(f.getvalue())
b'\xe4\xb8\xad\xe6\x96\x87'
```

请注意，写入的不是 str，而是经过 UTF-8 编码的 bytes。<span style="color:red;">嗯，这个地方知道了，encode 之后是 bytes。</span>

和 StringIO 类似，可以用一个 bytes 初始化 BytesIO，然后，像读文件一样读取：

```py
>>> from io import BytesIO
>>> f = BytesIO(b'\xe4\xb8\xad\xe6\x96\x87')
>>> f.read()
b'\xe4\xb8\xad\xe6\x96\x87'
```

<span style="color:red;">`f.getvalue()` 与 `f.read()` 有什么区别？什么时候使用这个 BytesIO？</span>

## 小结

StringIO 和 BytesIO 是在内存中操作 str 和 bytes 的方法，使得和读写文件具有一致的接口。<span style="color:red;">为什么要在内存中操作 str 和 bytes 呢？</span>



# 原文及引用

- [StringIO和 BytesIO](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001431918785710e86a1a120ce04925bae155012c7fc71e000)
