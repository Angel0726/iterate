---
title: index
toc: true
date: 2019-06-30
---
Python/C API 参考手册
*********************

本手册描述了希望编写扩展模块并将 Python 解释器嵌入其应用程序中的 C 和
C++ 程序员可用的 API。同时可以参阅 扩展和嵌入 Python 解释器 ，其中描述
了扩展编写的一般原则，但没有详细描述 API 函数。

* 概述

  * 包含文件

  * 对象、类型和引用计数

  * 异常

  * 嵌入 Python

  * 调试构建

* 稳定的应用程序二进制接口

* The Very High Level Layer

* Reference Counting

* 异常处理

  * Printing and clearing

  * 抛出异常

  * Issuing warnings

  * Querying the error indicator

  * Signal Handling

  * Exception Classes

  * Exception Objects

  * Unicode Exception Objects

  * Recursion Control

  * 标准异常

  * 标准警告类别

* 工具

  * 操作系统实用程序

  * 系统功能

  * 过程控制

  * 导入模块

  * Data marshalling support

  * 语句解释及变量编译

  * 字符串转换与格式化

  * 反射

  * 编解码器注册与支持功能

* 抽象对象层

  * Object Protocol

  * 数字协议

  * 序列协议

  * Mapping Protocol

  * 迭代器协议

  * 缓冲协议

  * Old Buffer Protocol

* 具体的对象层

  * 基本对象

  * 数值对象

  * 序列对象

  * 容器对象

  * 函数对象

  * 其他对象

* Initialization, Finalization, and Threads

  * Initializing and finalizing the interpreter

  * Process-wide parameters

  * Thread State and the Global Interpreter Lock

  * Sub-interpreter support

  * Asynchronous Notifications

  * Profiling and Tracing

  * Advanced Debugger Support

* 内存管理

  * 概述

  * 原始内存接口

  * 内存接口

  * 对象分配器

  * Customize Memory Allocators

  * The pymalloc allocator

  * 示例

* 对象实现支持

  * 在堆中分配对象

  * Common Object Structures

  * Type 对象

  * Number Object Structures

  * Mapping Object Structures

  * Sequence Object Structures

  * Buffer Object Structures

  * Async Object Structures

  * 使对象类型支持循环垃圾回收

* API 和 ABI 版本管理
