---
title: dummy_threading
toc: true
date: 2019-06-30
---
17.8. "dummy_threading" ---  可直接替代 "threading" 模块。
**********************************************************

**源代码：** Lib/dummy_threading.py

======================================================================

This module provides a duplicate interface to the "threading" module.
It is meant to be imported when the "_thread" module is not provided
on a platform.

Suggested usage is:

   try:
       import threading
   except ImportError:
       import dummy_threading as threading

如果线程需要阻塞等待另一个线程被创建的话，可能会造成死锁，这通常是由于
阻塞 I/O 引起的。这种场景下请不要使用这个模块。
