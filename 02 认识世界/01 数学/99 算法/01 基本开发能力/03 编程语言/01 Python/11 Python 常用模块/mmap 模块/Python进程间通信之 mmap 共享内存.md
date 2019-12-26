---
title: Python进程间通信之 mmap 共享内存
toc: true
date: 2019-06-20
---
# 可以补充进来的

- mmap 模块之前还真的不知道，之前进程之间的图片数据传输使用的是流，嗯，感觉这个应该比流还要快一些，不知道管理起来方不方便，以及进程之间读写这块内存的时候有没有冲突的问题？如果有要怎么解决？
- 例子暂时没有自己试过，后面使用时，还是要参考官方文档。


# Python 进程间通信之 mmap 共享内存

Python 进程间通信之共享内存。

我们在 Python 中也可以使用命名管道进行数据的传输的，但是要在 windows 使用命名管道，需要使用 Python 调研 windows api，太麻烦。

实际上，Python 中的进程之间的数据传输也可以通过共享内存的方式来实现的。查了一下，Python中可以使用 mmap 模块来实现这一功能。

Python 中的 mmap 模块是通过映射同一个普通文件实现共享内存的。文件被映射到进程地址空间后，进程可以像访问内存一样对文件进行访问。

不过，mmap 在 linux 和 windows 上的 API 有些许的不一样，具体细节可以查看 mmap 的文档。

下面看一个例子：

server.py

这个程序使用 test.dat 文件来映射内存，并且分配了 1024 字节的大小，每隔一秒更新一下内存信息。

```py
import mmap
import contextlib
import time

with open("test.dat", "w") as f:
  f.write('\x00' * 1024)

with open('test.dat', 'r+') as f:
  with contextlib.closing(mmap.mmap(f.fileno(), 1024, access=mmap.ACCESS_WRITE)) as m:
    for i in range(1, 10001):
      m.seek(0)
      s = "msg " + str(i)
      s.rjust(1024, '\x00')
      m.write(s)
      m.flush()
      time.sleep(1)
```

client.py

这个程序从上面映射的文件 test.dat 中加载数据到内存中。

```py
import mmap
import contextlib
import time

while True:
  with open('test.dat', 'r') as f:
    with contextlib.closing(mmap.mmap(f.fileno(), 1024, access=mmap.ACCESS_READ)) as m:
      s = m.read(1024).replace('\x00', '')
      print s
  time.sleep(1)
```

上面的代码可以在 linux 和 windows 上运行，因为我们明确指定了使用 `test.dat` 文件来映射内存。如果我们只需要在 windows 上实现共享内存，可以不用指定使用的文件，而是通过指定一个 `tagname` 来标识，所以可以简化上面的代码。如下：

server.py

```py
import mmap
import contextlib
import time

with contextlib.closing(mmap.mmap(-1, 1024, tagname='test', access=mmap.ACCESS_WRITE)) as m:
  for i in range(1, 10001):
    m.seek(0)
    m.write("msg " + str(i))
    m.flush()
    time.sleep(1)
```

client.py

```py
import mmap
import contextlib
import time

while True:
  with contextlib.closing(mmap.mmap(-1, 1024, tagname='test', access=mmap.ACCESS_READ)) as m:
    s = m.read(1024).replace('\x00', '')
    print s
  time.sleep(1)
```




# 相关

- [Python进程间通信之共享内存详解](https://www.jb51.net/article/127123.htm)
