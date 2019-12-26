---
title: persistence
toc: true
date: 2019-06-30
---
12. 数据持久化
**************

本章中描述的模块支持在磁盘上以持久形式存储 Python 数据。 "pickle" 和
"marshal" 模块可以将许多 Python 数据类型转换为字节流，然后从字节中重新
创建对象。 各种与 DBM 相关的模块支持一系列基于散列的文件格式，这些格式
存储字符串到其他字符串的映射。

本章中描述的模块列表是：

* 12.1. "pickle" —— Python 对象序列化

  * 12.1.1. 与其他 Python 模块间的关系

    * 12.1.1.1. 与 "marshal" 间的关系

    * 12.1.1.2. 与 "json" 模块的比较

  * 12.1.2. Data stream format

  * 12.1.3. Module Interface

  * 12.1.4. What can be pickled and unpickled?

  * 12.1.5. Pickling Class Instances

    * 12.1.5.1. Persistence of External Objects

    * 12.1.5.2. Dispatch Tables

    * 12.1.5.3. Handling Stateful Objects

  * 12.1.6. Restricting Globals

  * 12.1.7. 性能

  * 12.1.8. 示例

* 12.2. "copyreg" --- Register "pickle" support functions

  * 12.2.1. 示例

* 12.3. "shelve" --- Python object persistence

  * 12.3.1. Restrictions

  * 12.3.2. 示例

* 12.4. "marshal" --- Internal Python object serialization

* 12.5. "dbm" --- Interfaces to Unix "databases"

  * 12.5.1. "dbm.gnu" --- GNU's reinterpretation of dbm

  * 12.5.2. "dbm.ndbm" --- Interface based on ndbm

  * 12.5.3. "dbm.dumb" --- Portable DBM implementation

* 12.6. "sqlite3" --- SQLite 数据库 DB-API 2.0 接口模块

  * 12.6.1. 模块函数和常量

  * 12.6.2. 连接对象（Connection）

  * 12.6.3. Cursor 对象

  * 12.6.4. 行对象*Row*

  * 12.6.5. 异常

  * 12.6.6. SQLite 与 Python 类型

    * 12.6.6.1. 概述

    * 12.6.6.2. Using adapters to store additional Python types in
      SQLite databases

      * 12.6.6.2.1. Letting your object adapt itself

      * 12.6.6.2.2. Registering an adapter callable

    * 12.6.6.3. Converting SQLite values to custom Python types

    * 12.6.6.4. Default adapters and converters

  * 12.6.7. Controlling Transactions

  * 12.6.8. Using "sqlite3" efficiently

    * 12.6.8.1. Using shortcut methods

    * 12.6.8.2. Accessing columns by name instead of by index

    * 12.6.8.3. 使用连接作为上下文管理器

  * 12.6.9. 常见问题

    * 12.6.9.1. 多线程
