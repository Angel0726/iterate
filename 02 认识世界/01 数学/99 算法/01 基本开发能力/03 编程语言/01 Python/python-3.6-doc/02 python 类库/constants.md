---
title: constants
toc: true
date: 2019-06-30
---
3. 内置常量
***********

有少数的常量存在于内置命名空间中。 它们是：

False

   "bool" 类型的假值。 给 "False" 赋值是非法的并会引发 "SyntaxError"。

True

   "bool" 类型的真值。 给 "True" 赋值是非法的并会引发 "SyntaxError"。

None

   "NoneType" 类型的唯一值。 "None" 经常用于表示缺少值，当因为默认参数
   未传递给函数时。 给 "None" 赋值是非法的并会引发 "SyntaxError"。

NotImplemented

   二进制特殊方法应返回的特殊值（例如，"__eq__()"、"__lt__()"、"__add
   __()"、"__rsub__()" 等）表示操作没有针对其他类型实现；为了相同的目
   的，可以通过就地二进制特殊方法（例如，"__imul __()"、"__
   rightnd__()" 等）返回。 它的逻辑值为真。

   注解: 当二进制（或就地）方法返回``NotImplemented``时，解释器将尝
     试对另 一种类型（或其他一些回滚操作，取决于运算符）的反射操作。
     如果所有 尝试都返回``NotImplemented``，则解释器将引发适当的异常。
     错误返回 的``NotImplemented``将导致误导性错误消息或返回到 Python 代
     码中的 ``NotImplemented``值。参见 Implementing the arithmetic
     operations 为例。

   注解: "NotImplementedError" 和 "NotImplemented" 不可互换，即使它
     们有相 似的名称和用途。 有关何时使用它的详细信息，请参阅
     "NotImplementedError"。

Ellipsis

   与省略号文字字面 “"..."” 相同。 特殊值主要与用户定义的容器数据类型
   的扩展切片语法结合使用。

__debug__

   如果 Python 没有以 "-O" 选项启动，则此常量为真值。 另请参见
   "assert" 语句。

注解: 变量名 "None"，"False"，"True" 和 "__ debug__" 无法重新赋值（
  赋值给 它们，即使是属性名，将引发 "SyntaxError" ），所以它们可以被认
  为是“真 正的”常数。


3.1. 由 "site" 模块添加的常量
=============================

"site" 模块（在启动期间自动导入，除非给出 "-S" 命令行选项）将几个常量
添加到内置命名空间。 它们对交互式解释器 shell 很有用，并且不应在程序中
使用。

quit(code=None)
exit(code=None)

   当打印此对象时，会打印出一条消息，例如“Use quit() or Ctrl-D (i.e.
   EOF) to exit”，当调用此对象时，将使用指定的退出代码来引发
   "SystemExit"。

copyright
credits

   打印或调用的对象分别打印版权或作者的文本。

license

   当打印此对象时，会打印出一条消息“Type license() to see the full
   license text”，当调用此对象时，将以分页形式显示完整的许可证文本（每
   次显示一屏）。
