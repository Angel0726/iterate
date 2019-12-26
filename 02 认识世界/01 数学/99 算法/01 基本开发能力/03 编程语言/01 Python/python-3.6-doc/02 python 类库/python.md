---
title: python
toc: true
date: 2019-06-30
---
29. Python运行时服务
********************

本章里描述的模块提供了和 Python 解释器及其环境交互相关的广泛服务。以下是
综述：

* 29.1. "sys" --- 系统相关的参数和函数

* 29.2. "sysconfig" --- Provide access to Python's configuration
  information

  * 29.2.1. 配置变量

  * 29.2.2. 安装路径

  * 29.2.3. 其他功能

  * 29.2.4. Using "sysconfig" as a script

* 29.3. "builtins" --- 内建对象

* 29.4. "__main__" --- 顶层脚本环境

* 29.5. "warnings" --- Warning control

  * 29.5.1. 警告类别

  * 29.5.2. The Warnings Filter

    * 29.5.2.1. Default Warning Filters

  * 29.5.3. 暂时禁止警告

  * 29.5.4. 测试警告

  * 29.5.5. Updating Code For New Versions of Python

  * 29.5.6. Available Functions

  * 29.5.7. Available Context Managers

* 29.6. "contextlib" --- Utilities for "with"-statement contexts

  * 29.6.1. 工具

  * 29.6.2. 例子和配方

    * 29.6.2.1. Supporting a variable number of context managers

    * 29.6.2.2. Simplifying support for single optional context
      managers

    * 29.6.2.3. Catching exceptions from "__enter__" methods

    * 29.6.2.4. Cleaning up in an "__enter__" implementation

    * 29.6.2.5. Replacing any use of "try-finally" and flag
      variables

    * 29.6.2.6. Using a context manager as a function decorator

  * 29.6.3. Single use, reusable and reentrant context managers

    * 29.6.3.1. Reentrant context managers

    * 29.6.3.2. Reusable context managers

* 29.7. "abc" --- 抽象基类

* 29.8. "atexit" --- 退出处理器

  * 29.8.1. "atexit" 示例

* 29.9. "traceback" --- 打印或检索堆栈回溯

  * 29.9.1. "TracebackException" Objects

  * 29.9.2. "StackSummary" Objects

  * 29.9.3. "FrameSummary" Objects

  * 29.9.4. Traceback Examples

* 29.10. "__future__" --- Future 语句定义

* 29.11. "gc" --- 垃圾回收器接口

* 29.12. "inspect" --- 检查对象

  * 29.12.1. 类型和成员

  * 29.12.2. Retrieving source code

  * 29.12.3. Introspecting callables with the Signature object

  * 29.12.4. 类与函数

  * 29.12.5. The interpreter stack

  * 29.12.6. Fetching attributes statically

  * 29.12.7. Current State of Generators and Coroutines

  * 29.12.8. Code Objects Bit Flags

  * 29.12.9. Command Line Interface

* 29.13. "site" --- Site-specific configuration hook

  * 29.13.1. Readline configuration

  * 29.13.2. Module contents

* 29.14. "fpectl" --- Floating point exception control

  * 29.14.1. Example

  * 29.14.2. Limitations and other considerations
