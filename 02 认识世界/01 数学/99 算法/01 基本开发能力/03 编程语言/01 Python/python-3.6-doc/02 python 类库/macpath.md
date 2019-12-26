---
title: macpath
toc: true
date: 2019-06-30
---
11.11. "macpath" --- Mac OS 9 路径操作函数
******************************************

**源码：** Lib/macpath.py

======================================================================

该模块是 "os.path" 模块的 Mac OS 9（及更早版本）实现。 它可用于在 Mac
OS X（或任何其他平台）上操作旧式 Macintosh 路径名。

该模块中提供了以下函数："normcase()" 、 "normpath()" 、 "isabs()" 、
"join()" 、 "split()" 、 "isdir()" 、 "isfile()" 、 "walk()" 、
"exists()"。 对于其他可用的函数 "os.path" 虚拟对应可用。
