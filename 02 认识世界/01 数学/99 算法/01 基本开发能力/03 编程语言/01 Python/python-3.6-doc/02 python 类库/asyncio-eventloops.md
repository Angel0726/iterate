---
title: asyncio-eventloops
toc: true
date: 2019-06-30
---
18.5.2. 事件循环
****************

**源码:** Lib/asyncio/events.py


18.5.2.1. 事件循环函数
======================

下面的函数是访问全局策略方法的便利快捷方式。注意这只是提供了对默认策略
的访问，除非要在进程开始之前设置替代的策略。

asyncio.get_event_loop()

   与调用 "get_event_loop_policy().get_event_loop()" 等价。

asyncio.set_event_loop(loop)

   等价于调用 "get_event_loop_policy().set_event_loop(loop)"。

asyncio.new_event_loop()

   等价于调用 "get_event_loop_policy().new_event_loop()"。


18.5.2.2. 可用的事件循环
========================

asyncio当前提供两种事件循环实现： "SelectorEventLoop" 和
"ProactorEventLoop" 。

class asyncio.SelectorEventLoop

   基于 "selectors" 模块的事件循环。 "AbstractEventLoop" 的子类。

   使用对应平台上最高效的选择器。

   在 Windows 上，只支持套接字（举例：不支持管线）：参见 *MSDN关于 select
   的文档 <https://msdn.microsoft.com/en-
   us/library/windows/desktop/ms740141%28v=vs.85%29.aspx>* _ 。

class asyncio.ProactorEventLoop

   Windows上的 Proactor 事件循环，使用的是 “I/O 完成端口”，也叫做 IOCP
   。为 "AbstractEventLoop" 的子类。

   可用性：Windows。

   参见: MSDN上关于 I/O完成端口的文档 。

在 Windows 上使用 "ProactorEventLoop" 的例子:

   import asyncio, sys

   if sys.platform == 'win32':
       loop = asyncio.ProactorEventLoop()
       asyncio.set_event_loop(loop)


18.5.2.3. 平台支持
==================

"asyncio" 模块被设计为可移植的，但是每个平台仍旧有细微的不同，而且不一
定支持 "asyncio" 的所有特性。


18.5.2.3.1. Windows
-------------------

Windows事件循环的一般限制：

* 不支持 "create_unix_connection()" 和 "create_unix_server()"：
  "socket.AF_UNIX" 套接字族只在 UNIX 上可用

* 不支持 "add_signal_handler()" 和 "remove_signal_handler()"

* 不支持 "EventLoopPolicy.set_child_watcher()" 。"ProactorEventLoop"
  支持子进程。监控子进程的实现只有一种，不需要对其进行设置。

特定于 "SelectorEventLoop" 的限制：

* "SelectSelector" 只被用于支持套接字，并且最多支持 512 个套接字。

* "add_reader()" 和 "add_writer()" 只接受套接字的文件描述符

* 不支持管线（示例："connect_read_pipe()", "connect_write_pipe()"）

* 不支持 Subprocesses  （示例: "subprocess_exec()",
  "subprocess_shell()"）

特定于 "ProactorEventLoop" 的限制：

* 不支持 "create_datagram_endpoint()" (UDP)

* 不支持 "add_reader()" 和 "add_writer()"

Windows上单调时钟的分辨率大约为 15.6 毫秒。最佳的分辨率是 0.5 毫秒。分
辨率依赖于具体的硬件（HPET 的可用性）和 Windows 的设置。参见
:ref:*asyncio delayed calls <asyncio-delayed-calls>* 。

在 3.5 版更改: "ProactorEventLoop"  现在支持 SSL 了。


18.5.2.3.2. Mac OS X
--------------------

类似于 PTY 的字符设备在 Mavericks （Mac OS 10.9）之前并未有很好的支持。在
Mac OS 10.5以及更老的版本上则根本不支持。

在 Mac OS 10.6、10.7和 10.8上，默认的事件循环是 "SelectorEventLoop" ，其
使用了 "selectors.KqueueSelector" 。 "selectors.KqueueSelector" 在这些
版本上并不支持字符设备。"SelectorEventLoop" 可用同 "SelectSelector" 或
者 "PollSelector" 一同使用来在这些版本的 Mac OS X上支持块设备。 例如:

   import asyncio
   import selectors

   selector = selectors.SelectSelector()
   loop = asyncio.SelectorEventLoop(selector)
   asyncio.set_event_loop(loop)


18.5.2.4. 事件循环策略和默认策略
================================

使用一个 *策略* 模式对事件循环进行管理，用以为特定平台和框架提供最大限
度的灵活性。在一个进程的执行中，唯一的全局策略对象，基于调用的上下文，
管理着此进程所有可用的事件循环。一个策略就是一个实现了
"AbstractEventLoopPolicy" 的对象。

对于 "asyncio" 的多数使用者来说，从来都不需要显式地处理策略，因为默认
的全局策略就已经足够了（看下面）。

模块级别的函数 "get_event_loop()" 和 "set_event_loop()" 对于默认策略管
理的事件循环的便利访问方式。


18.5.2.5. 事件循环策略接口
==========================

一个事件循环必须实现如下的接口：

class asyncio.AbstractEventLoopPolicy

   事件循环策略。

   get_event_loop()

      为当前上下文获取事件循环。

      返回一个实现了 "AbstractEventLoop" 接口的事件循环对象。如果是从
      一个协程中调用，就会返回当前正在运行的事件循环。

      如果没有为当前上下文设置一个事件循环，并且当前策略不能创建一个事
      件循环，就会抛出一个错误。其务必不能返回 "None" 。

      在 3.6 版更改.

   set_event_loop(loop)

      将当前上下文的事件循环设置为  *loop* 。

   new_event_loop()

      根据策略的规则，创建并返回一个新的事件循环对象。

      如果有需要设置这个循环作为当前上下文的事件循环，就必须显式调用
      "set_event_loop()"  。

The default policy defines context as the current thread, and manages
an event loop per thread that interacts with "asyncio".  If the
current thread doesn't already have an event loop associated with it,
the default policy's "get_event_loop()" method creates one when called
from the main thread, but raises "RuntimeError" otherwise.


18.5.2.6. 访问全局循环策略
==========================

asyncio.get_event_loop_policy()

   获取当前事件循环策略。

asyncio.set_event_loop_policy(policy)

   设置当前事件循环策略。 如果 *policy* 为 "None" ，就会恢复使用默认的
   策略。


18.5.2.7. 自定义事件循环策略
============================

要实现一个新的事件循环策略，推荐你继承具体的默认事件循环策略
"DefaultEventLoopPolicy" 并且重写你想要改变行为的方法，例如:

   class MyEventLoopPolicy(asyncio.DefaultEventLoopPolicy):

       def get_event_loop(self):
           """Get the event loop.

           This may be None or an instance of EventLoop.
           """
           loop = super().get_event_loop()
           # Do something with loop ...
           return loop

   asyncio.set_event_loop_policy(MyEventLoopPolicy())
