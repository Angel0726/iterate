---
title: index
toc: true
date: 2019-06-30
---
扩展和嵌入 Python 解释器
************************

本文档描述了如何使用 C 或 C++ 编写模块以使用新模块来扩展 Python 解释器
的功能。 这些模块不仅可以定义新的函数，还可以定义新的对象类型及其方法
。 该文档还描述了如何将 Python 解释器嵌入到另一个应用程序中，以用作扩
展语言。 最后，它展示了如何编译和链接扩展模块，以便它们可以动态地（在
运行时）加载到解释器中，如果底层操作系统支持此特性的话。

本文档假设你具备有关 Python 的基本知识。有关该语言的非正式介绍，请参阅
Python 教程 。 Python 语言参考 给出了更正式的语言定义。 Python 标准库
包含现有的对象类型、函数和模块（内置和用 Python 编写）的文档，使语言具
有广泛的应用范围。

关于整个 Python/C API 的详细介绍，请参阅独立的 Python/C API 参考手册
。


推荐的第三方工具
================

本指南仅介绍了作为此 CPython 版本的一部分提供的创建扩展的基本工具。 第
三方工具，如 Cython 、 cffi 、 SWIG 和 Numba 提供了更简单和更复杂的方
法来为 Python 创建 C 和 C ++ 扩展。

参见:

  Python Packaging User Guide: Binary Extensions
     “ Python Packaging User Guide ”不仅涵盖了几个简化二进制扩展创建的
     可用工具，还讨论了为什么首先创建扩展模块的各种原因。


不使用第三方工具创建扩展
========================

本指南的这一部分包括在没有第三方工具帮助的情况下创建 C 和 C ++ 扩展。
它主要用于这些工具的创建者，而不是建议你创建自己的 C 扩展的方法。

* 1. 使用 C 或 C++ 扩展 Python

  * 1.1. 一个简单的例子

  * 1.2. 关于错误和异常

  * 1.3. 回到例子

  * 1.4. 模块方法表和初始化函数

  * 1.5. 编译和链接

  * 1.6. 在 C 中调用 Python 函数

  * 1.7. 提取扩展函数的参数

  * 1.8. 给扩展函数的关键字参数

  * 1.9. 构造任意值

  * 1.10. 引用计数

  * 1.11. 在 C++中编写扩展

  * 1.12. 给扩展模块提供 C API

* 2. 自定义扩展类型：教程

  * 2.1. 基础

  * 2.2. Adding data and methods to the Basic example

  * 2.3. Providing finer control over data attributes

  * 2.4. Supporting cyclic garbage collection

  * 2.5. Subclassing other types

* 3. 定义扩展类型：已分类主题

  * 3.1. 终结和内存释放

  * 3.2. 对象展示

  * 3.3. Attribute Management

  * 3.4. Object Comparison

  * 3.5. Abstract Protocol Support

  * 3.6. Weak Reference Support

  * 3.7. 更多建议

* 4. 构建 C/C++扩展

  * 4.1. 使用 distutils 构建 C 和 C++扩展

  * 4.2. 发布你的扩展模块

* 5. 在 Windows 平台编译 C 和 C++扩展

  * 5.1. A Cookbook Approach

  * 5.2. Differences Between Unix and Windows

  * 5.3. Using DLLs in Practice


在更大的应用程序中嵌入 CPython 运行时
=====================================

有时，不是要创建在 Python 解释器中作为主应用程序运行的扩展，而是希望将
CPython 运行时嵌入到更大的应用程序中。 本节介绍了成功完成此操作所涉及
的一些细节。

* 1. Embedding Python in Another Application

  * 1.1. Very High Level Embedding

  * 1.2. Beyond Very High Level Embedding: An overview

  * 1.3. Pure Embedding

  * 1.4. Extending Embedded Python

  * 1.5. Embedding Python in C++

  * 1.6. Compiling and Linking under Unix-like systems
