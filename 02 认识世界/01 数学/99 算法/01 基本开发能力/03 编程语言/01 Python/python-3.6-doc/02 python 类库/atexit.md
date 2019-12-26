---
title: atexit
toc: true
date: 2019-06-30
---
29.8. "atexit" --- 退出处理器
*****************************

======================================================================

"atexit" 模块定义了清理函数的注册和反注册函数. 被注册的函数会在解释器
正常终止时执行. "atexit" 会按照注册顺序的*逆序*执行; 如果你注册了 "A",
"B" 和 "C", 那么在解释器终止时会依序执行 "C", "B", "A".

**注意:** 通过该模块注册的函数, 在程序被未被 Python 捕获的信号杀死时并
不会执行, 在检测到 Python 内部致命错误以及调用了 "os._exit()" 时也不会
执行.

atexit.register(func, *args, **kwargs)

   将 *func* 注册为终止时执行的函数.  任何传给 *func* 的可选的参数都应
   当作为参数传给 "register()".  可以多次注册同样的函数及参数.

   在正常的程序终止时 (举例来说, 当调用了 "sys.exit()" 或是主模块的执
   行完成时), 所有注册过的函数都会以后进先出的顺序执行. 这样做是假定更
   底层的模块通常会比高层模块更早引入, 因此需要更晚清理.

   If an exception is raised during execution of the exit handlers, a
   traceback is printed (unless "SystemExit" is raised) and the
   exception information is saved.  After all exit handlers have had a
   chance to run the last exception to be raised is re-raised.

   这个函数返回 *func* 对象，可以把它当作装饰器使用。

atexit.unregister(func)

   Remove *func* from the list of functions to be run at interpreter
   shutdown.  After calling "unregister()", *func* is guaranteed not
   to be called when the interpreter shuts down, even if it was
   registered more than once.  "unregister()" silently does nothing if
   *func* was not previously registered.

参见:

  模块 "readline"
     使用 "atexit" 读写 "readline" 历史文件的有用的例子。


29.8.1. "atexit" 示例
=====================

The following simple example demonstrates how a module can initialize
a counter from a file when it is imported and save the counter's
updated value automatically when the program terminates without
relying on the application making an explicit call into this module at
termination.

   try:
       with open("counterfile") as infile:
           _count = int(infile.read())
   except FileNotFoundError:
       _count = 0

   def incrcounter(n):
       global _count
       _count = _count + n

   def savecounter():
       with open("counterfile", "w") as outfile:
           outfile.write("%d" % _count)

   import atexit
   atexit.register(savecounter)

Positional and keyword arguments may also be passed to "register()" to
be passed along to the registered function when it is called:

   def goodbye(name, adjective):
       print('Goodbye, %s, it was %s to meet you.' % (name, adjective))

   import atexit
   atexit.register(goodbye, 'Donny', 'nice')

   # or:
   atexit.register(goodbye, adjective='nice', name='Donny')

作为 *decorator*: 使用:

   import atexit

   @atexit.register
   def goodbye():
       print("You are now leaving the Python sector.")

只有在函数不需要任何参数调用时才能工作.
