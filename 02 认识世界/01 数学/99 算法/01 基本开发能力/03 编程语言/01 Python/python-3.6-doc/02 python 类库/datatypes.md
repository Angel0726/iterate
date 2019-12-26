---
title: datatypes
toc: true
date: 2019-06-30
---
8. 数据类型
***********

本章节描述的模块提供了一系列专门的数据类型例如日期与实践，固定类型的数
组，堆队列，同步队列与集合。

Python 同样提供一些内置的数据类型，特别的， "dict" ， "list" ， "set"
与 "fromzenset" 以及 "tuple" 。 "str" 类通常指 Unicode 字符串，并且
"bytes" 通常指二进制数据。

本章记录以下模块：

* 8.1. "datetime" --- 基础 日期 和 时间 数据类型

  * 8.1.1. 有效的类型

  * 8.1.2. "timedelta" 类对象

  * 8.1.3. "date" 对象

  * 8.1.4. "datetime" 对象

  * 8.1.5. "time" Objects

  * 8.1.6. "tzinfo" 对象

  * 8.1.7. "timezone" Objects

  * 8.1.8. "strftime()" and "strptime()" Behavior

* 8.2. "calendar" --- 日历相关函数

* 8.3. "collections" --- 容器数据类型

  * 8.3.1. "ChainMap" 对象

    * 8.3.1.1. "ChainMap" 例子和方法

  * 8.3.2. "Counter" 对象

  * 8.3.3. "deque" 对象

    * 8.3.3.1. "deque" 用法

  * 8.3.4. "defaultdict" 对象

    * 8.3.4.1. "defaultdict" 例子

  * 8.3.5. "namedtuple()" 命名元组的工厂函数

  * 8.3.6. "OrderedDict" 对象

    * 8.3.6.1. "OrderedDict" 例子和用法

  * 8.3.7. "UserDict" 对象

  * 8.3.8. "UserList" 对象

  * 8.3.9. "UserString" 对象

* 8.4. "collections.abc" --- 容器的抽象基类

  * 8.4.1. Collections Abstract Base Classes

* 8.5. "heapq" --- 堆队列算法

  * 8.5.1. 基本示例

  * 8.5.2. Priority Queue Implementation Notes

  * 8.5.3. Theory

* 8.6. "bisect" --- Array bisection algorithm

  * 8.6.1. Searching Sorted Lists

  * 8.6.2. Other Examples

* 8.7. "array" --- Efficient arrays of numeric values

* 8.8. "weakref" --- 弱引用

  * 8.8.1. 弱引用对象

  * 8.8.2. 示例

  * 8.8.3. Finalizer Objects

  * 8.8.4. Comparing finalizers with "__del__()" methods

* 8.9. "types" --- Dynamic type creation and names for built-in
  types

  * 8.9.1. Dynamic Type Creation

  * 8.9.2. Standard Interpreter Types

  * 8.9.3. Additional Utility Classes and Functions

  * 8.9.4. Coroutine Utility Functions

* 8.10. "copy" --- 浅层 (shallow) 和深层 (deep) 复制操作

* 8.11. "pprint" --- 数据美化输出

  * 8.11.1. PrettyPrinter Objects

  * 8.11.2. 示例

* 8.12. "reprlib" --- Alternate "repr()" implementation

  * 8.12.1. Repr Objects

  * 8.12.2. Subclassing Repr Objects

* 8.13. "enum" --- Support for enumerations

  * 8.13.1. 模块内容

  * 8.13.2. Creating an Enum

  * 8.13.3. Programmatic access to enumeration members and their
    attributes

  * 8.13.4. Duplicating enum members and values

  * 8.13.5. Ensuring unique enumeration values

  * 8.13.6. Using automatic values

  * 8.13.7. Iteration

  * 8.13.8. 比较

  * 8.13.9. Allowed members and attributes of enumerations

  * 8.13.10. Restricted subclassing of enumerations

  * 8.13.11. Pickling

  * 8.13.12. Functional API

  * 8.13.13. Derived Enumerations

    * 8.13.13.1. IntEnum

    * 8.13.13.2. IntFlag

    * 8.13.13.3. 标志

    * 8.13.13.4. Others

  * 8.13.14. Interesting examples

    * 8.13.14.1. Omitting values

      * 8.13.14.1.1. Using "auto"

      * 8.13.14.1.2. Using "object"

      * 8.13.14.1.3. Using a descriptive string

      * 8.13.14.1.4. Using a custom "__new__()"

    * 8.13.14.2. OrderedEnum

    * 8.13.14.3. DuplicateFreeEnum

    * 8.13.14.4. Planet

  * 8.13.15. How are Enums different?

    * 8.13.15.1. Enum Classes

    * 8.13.15.2. Enum Members (aka instances)

    * 8.13.15.3. Finer Points

      * 8.13.15.3.1. Supported "__dunder__" names

      * 8.13.15.3.2. Supported "_sunder_" names

      * 8.13.15.3.3. "Enum" member type

      * 8.13.15.3.4. Boolean value of "Enum" classes and members

      * 8.13.15.3.5. "Enum" classes with methods

      * 8.13.15.3.6. Combining members of "Flag"
