---
title: timeit
toc: true
date: 2019-06-30
---
27.5. "timeit" --- 测量小代码片段的执行时间
*******************************************

**源码：** Lib/timeit.py

======================================================================

该模块提供了一种简单的方法来计算一小段 Python 代码的耗时。它有 命令行
界面 以及一个 可调用 方法。它避免了许多用于测量执行时间的常见陷阱。另
见 Tim Peters 对 O'Reilly 出版的 *Python Cookbook* 中“算法”章节的介绍
。


27.5.1. 基本示例
================

以下示例显示了如何使用 命令行界面 来比较三个不同的表达式：

   $ Python3 -m timeit '"-".join(str(n) for n in range(100))'
   10000 loops, best of 3: 30.2 usec per loop
   $ Python3 -m timeit '"-".join([str(n) for n in range(100)])'
   10000 loops, best of 3: 27.5 usec per loop
   $ Python3 -m timeit '"-".join(map(str, range(100)))'
   10000 loops, best of 3: 23.2 usec per loop

这可以通过 Python 接口 实现

   >>> import timeit
   >>> timeit.timeit('"-".join(str(n) for n in range(100))', number=10000)
   0.3018611848820001
   >>> timeit.timeit('"-".join([str(n) for n in range(100)])', number=10000)
   0.2727368790656328
   >>> timeit.timeit('"-".join(map(str, range(100)))', number=10000)
   0.23702679807320237

Note however that "timeit" will automatically determine the number of
repetitions only when the command-line interface is used.  In the 示例
section you can find more advanced examples.


27.5.2. Python 接口
===================

该模块定义了三个便利函数和一个公共类：

timeit.timeit(stmt='pass', setup='pass', timer=<default timer>, number=1000000, globals=None)

   使用给定语句、 *setup* 代码和 *timer* 函数创建一个 "Timer" 实例，并
   执行 *number* 次其 "timeit()" 方法。可选的 *globals* 参数指定用于执
   行代码的命名空间。

   在 3.5 版更改: 添加可选参数 *globals* 。

timeit.repeat(stmt='pass', setup='pass', timer=<default timer>, repeat=3, number=1000000, globals=None)

   使用给定语句、 *setup* 代码和 *timer* 函数创建一个 "Timer" 实例，并
   使用给定的 *repeat* 计数和 *number* 执行运行其 "repeat()" 方法。可
   选的 *globals* 参数指定用于执行代码的命名空间。

   在 3.5 版更改: 添加可选参数 *globals* 。

timeit.default_timer()

   默认的计时器，总是 "time.perf_counter()" 。

   在 3.3 版更改: "time.perf_counter()" 现在是默认计时器。

class timeit.Timer(stmt='pass', setup='pass', timer=<timer function>, globals=None)

   用于小代码片段的计数执行速度的类。

   构造函数接受一个将计时的语句、一个用于设置的附加语句和一个定时器函
   数。两个语句都默认为 "'pass'" ；计时器函数与平台有关（请参阅模块文
   档字符串）。 *stmt* 和 *setup* 也可能包含多个以 ";" 或换行符分隔的
   语句，只要它们不包含多行字符串文字即可。该语句默认在 timeit 的命名
   空间内执行；可以通过将命名空间传递给 *globals* 来控制此行为。

   要测量第一个语句的执行时间，请使用 "timeit()" 方法。 "repeat()" 和
   "autorange()" 方法是方便的方法来调用 "timeit()" 多次。

   *setup* 的执行时间从总体计时执行中排除。

   *stmt* 和 *setup* 参数也可以使用不带参数的可调用对象。这将在一个计
   时器函数中嵌入对它们的调用，然后由 "timeit()" 执行。请注意，由于额
   外的函数调用，在这种情况下，计时开销会略大一些。

   在 3.5 版更改: 添加可选参数 *globals* 。

   timeit(number=1000000)

      执行 *number* 次主要语句。这将执行一次 setup 语句，然后返回执行
      主语句多次所需的时间，以秒为单位测量为浮点数。参数是通过循环的次
      数，默认为一百万。要使用的主语句、 setup 语句和 timer 函数将传递
      给构造函数。

      注解: By default, "timeit()" temporarily turns off *garbage
        collection* during the timing.  The advantage of this approach
        is that it makes independent timings more comparable.  This
        disadvantage is that GC may be an important component of the
        performance of the function being measured.  If so, GC can be
        re-enabled as the first statement in the *setup* string.  For
        example:

           timeit.Timer('for i in range(10): oct(i)', 'gc.enable()').timeit()

   autorange(callback=None)

      自动决定调用多少次 "timeit()" 。

      This is a convenience function that calls "timeit()" repeatedly
      so that the total time >= 0.2 second, returning the eventual
      (number of loops, time taken for that number of loops). It calls
      "timeit()" with *number* set to successive powers of ten (10,
      100, 1000, ...) up to a maximum of one billion, until the time
      taken is at least 0.2 second, or the maximum is reached.

      如果给出 *callback* 并且不是 "None" ，则在每次试验后将使用两个参
      数调用它： "callback(number, time_taken)" 。

      3.6 新版功能.

   repeat(repeat=3, number=1000000)

      调用 "timeit()" 几次。

      这是一个方便的函数，它反复调用 "timeit()" ，返回结果列表。第一个
      参数指定调用 "timeit()" 的次数。第二个参数指定 "timeit()" 的
      *number* 参数。

      注解: 从结果向量计算并报告平均值和标准差这些是很诱人的。但是，
        这不是 很有用。在典型情况下，最低值给出了机器运行给定代码段的
        速度的下 限；结果向量中较高的值通常不是由 Python 的速度变化引起
        的，而是由 于其他过程干扰你的计时准确性。所以结果的 "min()" 可
        能是你应该 感兴趣的唯一数字。之后，你应该看看整个向量并应用常
        识而不是统计 。

   print_exc(file=None)

      帮助程序从计时代码中打印回溯。

      典型使用:

         t = Timer(...)       # outside the try/except
         try:
             t.timeit(...)    # or t.repeat(...)
         except Exception:
             t.print_exc()

      与标准回溯相比，优势在于将显示已编译模板中的源行。可选的 *file*
      参数指向发送回溯的位置；它默认为 "sys.stderr" 。


27.5.3. 命令行界面
==================

从命令行调用程序时，使用以下表单:

   Python -m timeit [-n N] [-r N] [-u U] [-s S] [-t] [-c] [-h] [statement ...]

如果了解以下选项：

-n N, --number=N

   执行 '语句' 多少次

-r N, --repeat=N

   how many times to repeat the timer (default 3)

-s S, --setup=S

   最初要执行一次的语句（默认为 "pass" ）

-p, --process

   测量进程时间，而不是 wallclock 时间，使用 "time.process_time()" 而
   不是 "time.perf_counter()" ，这是默认值

   3.3 新版功能.

-t, --time

   use "time.time()" (deprecated)

-u, --unit=U

      specify a time unit for timer output; can select usec, msec, or
      sec

   3.5 新版功能.

-c, --clock

   use "time.clock()" (deprecated)

-v, --verbose

   打印原始计时结果；重复更多位数精度

-h, --help

   打印一条简短的使用信息并退出

可以通过将每一行指定为单独的语句参数来给出多行语句；通过在引号中包含参
数并使用前导空格可以缩进行。多个 "-s" 选项的处理方式相似。

如果 "-n" 未给出，则通过尝试 10 的连续幂次来计算合适数量的循环，直到总时
间至少为 0.2 秒。

"default_timer()" measurements can be affected by other programs
running on the same machine, so the best thing to do when accurate
timing is necessary is to repeat the timing a few times and use the
best time.  The "-r" option is good for this; the default of 3
repetitions is probably enough in most cases.  You can use
"time.process_time()" to measure CPU time.

注解: 执行 pass 语句会产生一定的基线开销。这里的代码不会试图隐藏它，
  但你应 该知道它。可以通过不带参数调用程序来测量基线开销，并且 Python
  版本之间 可能会有所不同。


27.5.4. 示例
============

可以提供一个在开头只执行一次的 setup 语句：

   $ Python -m timeit -s 'text = "sample string"; char = "g"'  'char in text'
   10000000 loops, best of 3: 0.0877 usec per loop
   $ Python -m timeit -s 'text = "sample string"; char = "g"'  'text.find(char)'
   1000000 loops, best of 3: 0.342 usec per loop

   >>> import timeit
   >>> timeit.timeit('char in text', setup='text = "sample string"; char = "g"')
   0.41440500499993504
   >>> timeit.timeit('text.find(char)', setup='text = "sample string"; char = "g"')
   1.7246671520006203

使用 "Timer" 类及其方法可以完成同样的操作:

   >>> import timeit
   >>> t = timeit.Timer('char in text', setup='text = "sample string"; char = "g"')
   >>> t.timeit()
   0.3955516149999312
   >>> t.repeat()
   [0.40193588800002544, 0.3960157959998014, 0.39594301399984033]

以下示例显示如何计算包含多行的表达式。 在这里我们对比使用 "hasattr()"
与 "try"/"except" 的开销来测试缺失与提供对象属性:

   $ Python -m timeit 'try:' '  str.__bool__' 'except AttributeError:' '  pass'
   100000 loops, best of 3: 15.7 usec per loop
   $ Python -m timeit 'if hasattr(str, "__bool__"): pass'
   100000 loops, best of 3: 4.26 usec per loop

   $ Python -m timeit 'try:' '  int.__bool__' 'except AttributeError:' '  pass'
   1000000 loops, best of 3: 1.43 usec per loop
   $ Python -m timeit 'if hasattr(int, "__bool__"): pass'
   100000 loops, best of 3: 2.23 usec per loop

   >>> import timeit
   >>> # attribute is missing
   >>> s = """\
   ... try:
   ...     str.__bool__
   ... except AttributeError:
   ...     pass
   ... """
   >>> timeit.timeit(stmt=s, number=100000)
   0.9138244460009446
   >>> s = "if hasattr(str, '__bool__'): pass"
   >>> timeit.timeit(stmt=s, number=100000)
   0.5829014980008651
   >>>
   >>> # attribute is present
   >>> s = """\
   ... try:
   ...     int.__bool__
   ... except AttributeError:
   ...     pass
   ... """
   >>> timeit.timeit(stmt=s, number=100000)
   0.04215312199994514
   >>> s = "if hasattr(int, '__bool__'): pass"
   >>> timeit.timeit(stmt=s, number=100000)
   0.08588060699912603

要让 "timeit" 模块访问你定义的函数，你可以传递一个包含 import 语句的
*setup* 参数:

   def test():
       """Stupid test function"""
       L = [i for i in range(100)]

   if __name__ == '__main__':
       import timeit
       print(timeit.timeit("test()", setup="from __main__ import test"))

另一种选择是将 "globals()" 传递给 *globals* 参数，这将导致代码在当前的
全局命名空间中执行。这比单独指定 import 更方便

   def f(x):
       return x**2
   def g(x):
       return x**4
   def h(x):
       return x**8

   import timeit
   print(timeit.timeit('[func(42) for func in (f,g,h)]', globals=globals()))
