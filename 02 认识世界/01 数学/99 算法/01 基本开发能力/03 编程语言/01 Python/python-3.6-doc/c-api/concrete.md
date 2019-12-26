---
title: concrete
toc: true
date: 2019-06-30
---
具体的对象层
************

本章中的函数特定于某些 Python 对象类型。 将错误类型的对象传递给它们并
不是一个好主意；如果您从 Python 程序接收到一个对象，但不确定它是否具有
正确的类型，则必须首先执行类型检查；例如，要检查对象是否为字典，请使用
"PyDict_Check()"。 本章的结构类似于 Python 对象类型的“家族树”。

警告: 虽然本章描述的函数会仔细检查传入的对象的类型，但是其中许多函数
  不会检 查传入的对象是否为 NULL，而不是一个有效的对象。允许 NULL 传入可
  能会导致 内存访问冲突和解释器的立即终止。


基本对象
========

本节描述 Python 类型对象和单一实例对象 象 None。

* Type 对象

* "None" 对象


数值对象
========

* 整数型对象

* 布尔对象

* 浮点数对象

* 复数对象

  * 表示复数的 C 结构体

  * 表示复数的 Python 对象


序列对象
========

序列对象的一般操作在前一章中讨论过；本节介绍 Python 语言固有的特定类型的
序列对象。

* 字节对象

* 字节数组对象

  * 类型检查宏

  * 直接 API 函数

  * 宏

* Unicode Objects and Codecs

  * Unicode对象

    * Unicode类型

    * Unicode字符属性

    * Creating and accessing Unicode strings

    * Deprecated Py_UNICODE APIs

    * Locale Encoding

    * File System Encoding

    * wchar_t Support

  * Built-in Codecs

    * Generic Codecs

    * UTF-8 Codecs

    * UTF-32 Codecs

    * UTF-16 Codecs

    * UTF-7 Codecs

    * Unicode-Escape Codecs

    * Raw-Unicode-Escape Codecs

    * Latin-1 Codecs

    * ASCII Codecs

    * Character Map Codecs

    * MBCS codecs for Windows

    * Methods & Slots

  * Methods and Slot Functions

* 元组对象

* Struct Sequence Objects

* 列表对象


容器对象
========

* 字典对象

* 集合对象


函数对象
========

* 函数对象

* 实例方法对象

* 方法对象

* Cell 对象

* 代码对象


其他对象
========

* 文件对象

* Module Objects

  * Initializing C modules

    * Single-phase initialization

    * Multi-phase initialization

    * Low-level module creation functions

    * Support functions

  * Module lookup

* 迭代器对象

* 描述符对象

* 切片对象

* Ellipsis Object

* MemoryView 对象

* 弱引用对象

* 胶囊

* 生成器对象

* 协程对象

* DateTime 对象
