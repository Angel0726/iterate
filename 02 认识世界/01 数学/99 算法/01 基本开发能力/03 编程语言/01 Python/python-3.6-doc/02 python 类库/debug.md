---
title: debug
toc: true
date: 2019-06-30
---
27. 调试和分析
**************

这些库可以帮助你进行 Python 开发：调试器使你能够逐步执行代码，分析堆栈帧
并设置断点等，而分析器运行代码并为你提供执行时间的详细分类，从而使你能
够找出你程序中的瓶颈。

* 27.1. "bdb" --- Debugger framework

* 27.2. "faulthandler" --- Dump the Python traceback

  * 27.2.1. Dumping the traceback

  * 27.2.2. Fault handler state

  * 27.2.3. Dumping the tracebacks after a timeout

  * 27.2.4. Dumping the traceback on a user signal

  * 27.2.5. Issue with file descriptors

  * 27.2.6. 示例

* 27.3. "pdb" --- Python的调试器

  * 27.3.1. Debugger Commands

* 27.4. The Python Profilers

  * 27.4.1. Introduction to the profilers

  * 27.4.2. Instant User's Manual

  * 27.4.3. "profile" and "cProfile" Module Reference

  * 27.4.4. The "Stats" Class

  * 27.4.5. What Is Deterministic Profiling?

  * 27.4.6. Limitations

  * 27.4.7. Calibration

  * 27.4.8. Using a custom timer

* 27.5. "timeit" --- 测量小代码片段的执行时间

  * 27.5.1. 基本示例

  * 27.5.2. Python 接口

  * 27.5.3. 命令行界面

  * 27.5.4. 示例

* 27.6. "trace" --- Trace or track Python statement execution

  * 27.6.1. Command-Line Usage

    * 27.6.1.1. Main options

    * 27.6.1.2. Modifiers

    * 27.6.1.3. Filters

  * 27.6.2. Programmatic Interface

* 27.7. "tracemalloc" --- Trace memory allocations

  * 27.7.1. 示例

    * 27.7.1.1. Display the top 10

    * 27.7.1.2. Compute differences

    * 27.7.1.3. Get the traceback of a memory block

    * 27.7.1.4. Pretty top

  * 27.7.2. API

    * 27.7.2.1. 函数

    * 27.7.2.2. DomainFilter

    * 27.7.2.3. Filter

    * 27.7.2.4. Frame

    * 27.7.2.5. Snapshot

    * 27.7.2.6. Statistic

    * 27.7.2.7. StatisticDiff

    * 27.7.2.8. Trace

    * 27.7.2.9. Traceback
