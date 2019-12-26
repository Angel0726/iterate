---
title: rlcompleter
toc: true
date: 2019-06-30
---
6.8. "rlcompleter" --- GNU readline的完成函数
*********************************************

**源代码:** Lib/rlcompleter.py

======================================================================

"rlcompeleter" 通过补全有效的 Python 标识符和关键字为 "readline" 模块
定义了一个补全功能套装.

当此模块在具有可用的 "readline" 模块的 Unix 平台被导入, 一个
"Completer" 实例将被自动创建并且它的 "complete()" 方法将设置为
"readline" 的补全器.

示例:

   >>> import rlcompleter
   >>> import readline
   >>> readline.parse_and_bind("tab: complete")
   >>> readline. <TAB PRESSED>
   readline.__doc__          readline.get_line_buffer(  readline.read_init_file(
   readline.__file__         readline.insert_text(      readline.set_completer(
   readline.__name__         readline.parse_and_bind(
   >>> readline.

"rlcompleter" 模块是为了使用 Python 的 交互模式 而设计的. 除非 Python
是通过 "-S" 选项运行, 这个模块总是自动地被导入且配置(参考 Readline
configuration).

在没有 "readline" 的平台, 此模块定义的 "Completer" 类仍然可以用于自定
义行为.


6.8.1. Completer对象
====================

Completer对象具有以下方法：

Completer.complete(text, state)

   为 *text* 返回 *state*th 补全.

   如果要求 *text* 不包含句点字符 ("'.'"), 它将从当前定义的名称中完成
   "__main__", "builtins" 和 keywords (就和通过 "keyword" 模块定义的一
   样).

   如果为加点的名称执行调用，它将尝试尽量求值直到最后一部分为止而不产
   生附带影响（函数不会被求值，但它可以生成对 "__getattr__()" 的调用）
   ，并通过 "dir()" 函数来匹配剩余部分。 在对表达式求值期间引发的任何
   异常都会被捕获、静默处理并返回 "None"。
