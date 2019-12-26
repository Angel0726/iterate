---
title: compound_stmts
toc: true
date: 2019-06-30
---
8. 复合语句
***********

复合语句是包含其它语句（语句组）的语句；它们会以某种方式影响或控制所包
含其它语句的执行。 通常，复合语句会跨越多行，虽然在某些简单形式下整个
复合语句也可能包含于一行之内。

"if", "while" 和 "for" 语句用来实现传统的控制流程构造。 "try" 语句为一
组语句指定异常处理和/和清理代码，而 "with" 语句允许在一个代码块周围执
行初始化和终结化代码。 函数和类定义在语法上也属于复合语句。

一条复合语句由一个或多个‘子句’组成。 一个子句则包含一个句头和一个‘句体
’。 特定复合语句的子句头都处于相同的缩进层级。 每个子句头以一个作为唯
一标识的关键字开始并以一个冒号结束。 子句体是由一个子句控制的一组语句
。 子句体可以是在子句头的冒号之后与其同处一行的一条或由分号分隔的多条
简单语句，或者也可以是在其之后缩进的一行或多行语句。 只有后一种形式的
子句体才能包含嵌套的复合语句；以下形式是不合法的，这主要是因为无法分清
某个后续的 "else" 子句应该属于哪个 "if" 子句:

   if test1: if test2: print(x)

还要注意的是在这种情形下分号的绑定比冒号更紧密，因此在以下示例中，所有
"print()" 调用或者都不执行，或者都执行:

   if x < y < z: print(x); print(y); print(z)

总结:

   compound_stmt ::= if_stmt
                     | while_stmt
                     | for_stmt
                     | try_stmt
                     | with_stmt
                     | funcdef
                     | classdef
                     | async_with_stmt
                     | async_for_stmt
                     | async_funcdef
   suite         ::= stmt_list NEWLINE | NEWLINE INDENT statement+ DEDENT
   statement     ::= stmt_list NEWLINE | compound_stmt
   stmt_list     ::= simple_stmt (";" simple_stmt)* [";"]

请注意语句总是以 "NEWLINE" 结束，之后可能跟随一个 "DEDENT"。 还要注意
可选的后续子句总是以一个不能作为语句开头的关键字作为开头，因此不会产生
歧义（‘悬空的 "else"’问题在 Python 中是通过要求嵌套的 "if" 语句必须缩
进来解决的)。

为了保证清晰，以下各节中语法规则采用将每个子句都放在单独行中的格式。


8.1. The "if" statement
=======================

"if" 语句用于有条件的执行:

   if_stmt ::= "if" expression ":" suite
               ("elif" expression ":" suite)*
               ["else" ":" suite]

它通过对表达式逐个求值直至找到一个真值（请参阅 布尔运算 了解真值与假值
的定义）在子句体中选择唯一匹配的一个；然后执行该子句体（而且 "if" 语句
的其他部分不会被执行或求值）。 如果所有表达式均为假值，则如果 "else"
子句体如果存在就会被执行。


8.2. The "while" statement
==========================

"while" 语句用于在表达式保持为真的情况下重复地执行:

   while_stmt ::= "while" expression ":" suite
                  ["else" ":" suite]

This repeatedly tests the expression and, if it is true, executes the
first suite; if the expression is false (which may be the first time
it is tested) the suite of the "else" clause, if present, is executed
and the loop terminates.

A "break" statement executed in the first suite terminates the loop
without executing the "else" clause's suite.  A "continue" statement
executed in the first suite skips the rest of the suite and goes back
to testing the expression.


8.3. The "for" statement
========================

"for" 语句用于对序列（例如字符串、元组或列表）或其他可迭代对象中的元素
进行迭代:

   for_stmt ::= "for" target_list "in" expression_list ":" suite
                ["else" ":" suite]

The expression list is evaluated once; it should yield an iterable
object.  An iterator is created for the result of the
"expression_list".  The suite is then executed once for each item
provided by the iterator, in the order returned by the iterator.  Each
item in turn is assigned to the target list using the standard rules
for assignments (see 赋值语句), and then the suite is executed.  When
the items are exhausted (which is immediately when the sequence is
empty or an iterator raises a "StopIteration" exception), the suite in
the "else" clause, if present, is executed, and the loop terminates.

A "break" statement executed in the first suite terminates the loop
without executing the "else" clause's suite.  A "continue" statement
executed in the first suite skips the rest of the suite and continues
with the next item, or with the "else" clause if there is no next
item.

The for-loop makes assignments to the variables(s) in the target list.
This overwrites all previous assignments to those variables including
those made in the suite of the for-loop:

   for i in range(10):
       print(i)
       i = 5             # this will not affect the for-loop
                         # because i will be overwritten with the next
                         # index in the range

目标列表中的名称在循环结束时不会被删除，但如果序列为空，则它们根本不会
被循环所赋值。 提示：内置函数 "range()" 会返回一个可迭代的整数序列，适
用于模拟 Pascal 中的 "for i := a to b do" 这种效果；例如
"list(range(3))" 会返回列表 "[0, 1, 2]"。

注解: 当序列在循环中被修改时会有一个微妙的问题（这只可能发生于可变序
  列例如 列表中）。 会有一个内部计数器被用来跟踪下一个要使用的项，每次
  迭代都 会使计数器递增。 当计数器值达到序列长度时循环就会终止。 这意
  味着如果 语句体从序列中删除了当前（或之前）的一项，下一项就会被跳过
  （因为其标 号将变成已被处理的当前项的标号）。 类似地，如果语句体在序
  列当前项的 前面插入一个新项，当前项会在循环的下一轮中再次被处理。 这
  会导致麻烦 的程序错误，避免此问题的办法是对整个序列使用切片来创建一
  个临时副本， 例如

     for x in a[:]:
         if x < 0: a.remove(x)


8.4. The "try" statement
========================

"try" 语句可为一组语句指定异常处理器和/或清理代码:

   try_stmt  ::= try1_stmt | try2_stmt
   try1_stmt ::= "try" ":" suite
                 ("except" [expression ["as" identifier]] ":" suite)+
                 ["else" ":" suite]
                 ["finally" ":" suite]
   try2_stmt ::= "try" ":" suite
                 "finally" ":" suite

The "except" clause(s) specify one or more exception handlers. When no
exception occurs in the "try" clause, no exception handler is
executed. When an exception occurs in the "try" suite, a search for an
exception handler is started.  This search inspects the except clauses
in turn until one is found that matches the exception.  An expression-
less except clause, if present, must be last; it matches any
exception.  For an except clause with an expression, that expression
is evaluated, and the clause matches the exception if the resulting
object is "compatible" with the exception.  An object is compatible
with an exception if it is the class or a base class of the exception
object or a tuple containing an item compatible with the exception.

如果没有 except 子句与异常相匹配，则会在周边代码和发起调用栈上继续搜索
异常处理器。 [1]

如果在对 except 子句头中的表达式求值时引发了异常，则原来对处理器的搜索
会被取消，并在周边代码和调用栈上启动对新异常的搜索（它会被视作是整个
"try" 语句所引发的异常）。

When a matching except clause is found, the exception is assigned to
the target specified after the "as" keyword in that except clause, if
present, and the except clause's suite is executed.  All except
clauses must have an executable block.  When the end of this block is
reached, execution continues normally after the entire try statement.
(This means that if two nested handlers exist for the same exception,
and the exception occurs in the try clause of the inner handler, the
outer handler will not handle the exception.)

当使用 "as" 将目标赋值为一个异常时，它将在 except 子句结束时被清除。
这就相当于

   except E as N:
       foo

被转写为

   except E as N:
       try:
           foo
       finally:
           del N

这意味着异常必须赋值给一个不同的名称才能在 except 子句之后引用它。 异
常会被清除是因为在附加了回溯信息的情况下，它们会形成堆栈帧的循环引用，
使得所有局部变量保持存活直到发生下一次垃圾回收。

在一个 except 子句体被执行之前，有关异常的详细信息存放在 "sys" 模块中
，可通过 "sys.exc_info()" 来访问。 "sys.exc_info()" 返回一个 3 元组，
由异常类、异常实例和回溯对象组成（参见 标准类型层级结构 一节），用于在
程序中标识异常发生点。 当从处理异常的函数返回时 "sys.exc_info()" 的值
会恢复为（调用前的）原值。

The optional "else" clause is executed if the control flow leaves the
"try" suite, no exception was raised, and no "return", "continue", or
"break" statement was executed.  Exceptions in the "else" clause are
not handled by the preceding "except" clauses.

If "finally" is present, it specifies a 'cleanup' handler.  The "try"
clause is executed, including any "except" and "else" clauses.  If an
exception occurs in any of the clauses and is not handled, the
exception is temporarily saved. The "finally" clause is executed.  If
there is a saved exception it is re-raised at the end of the "finally"
clause.  If the "finally" clause raises another exception, the saved
exception is set as the context of the new exception. If the "finally"
clause executes a "return" or "break" statement, the saved exception
is discarded:

   >>> def f():
   ...     try:
   ...         1/0
   ...     finally:
   ...         return 42
   ...
   >>> f()
   42

在 "finally" 子句执行期间，程序不能获取异常信息。

When a "return", "break" or "continue" statement is executed in the
"try" suite of a "try"..."finally" statement, the "finally" clause is
also executed 'on the way out.' A "continue" statement is illegal in
the "finally" clause. (The reason is a problem with the current
implementation --- this restriction may be lifted in the future).

The return value of a function is determined by the last "return"
statement executed.  Since the "finally" clause always executes, a
"return" statement executed in the "finally" clause will always be the
last one executed:

   >>> def foo():
   ...     try:
   ...         return 'try'
   ...     finally:
   ...         return 'finally'
   ...
   >>> foo()
   'finally'

有关异常的更多信息可以在 异常 一节找到，有关使用 "raise" 语句生成异常
的信息可以在 The raise statement 一节找到。


8.5. The "with" statement
=========================

"with" 语句用于包装带有使用上下文管理器 (参见 with 语句上下文管理器 一
节) 定义的方法的代码块的执行。 这允许对普通的
"try"..."except"..."finally" 使用模式进行封装以方便地重用。

   with_stmt ::= "with" with_item ("," with_item)* ":" suite
   with_item ::= expression ["as" target]

带有一个“项目”的 "with" 语句的执行过程如下:

1. 对上下文表达式 (在 "with_item" 中给出的表达式) 求值以获得一个上
   下文 管理器。

2. 载入上下文管理器的 "__exit__()" 以便后续使用。

3. 发起调用上下文管理器的 "__enter__()" 方法。

4. 如果 "with" 语句中包含一个目标，来自 "__enter__()" 的返回值将被
   赋值 给它。

   注解: "with" 语句会保证如果 "__enter__()" 方法返回时未发生错误，
     则 "__exit__()" 将总是被调用。 因此，如果在对目标列表赋值期间发生
     错 误，则会将其视为在语句体内部发生的错误。 参见下面的第 6 步。

5. 执行语句体。

6. 发起调用上下文管理器的 "__exit__()" 方法。 如果语句体的退出是由
   异常 导致的，则其类型、值和回溯信息将被作为参数传递给 "__exit__()"
   。 否 则的话，将提供三个 "None" 参数。

   如果语句体的退出是由异常导致的，并且来自 "__exit__()" 方法的返回值
   为假，则该异常会被重新引发。 如果返回值为真，则该异常会被抑制，并会
   继续执行 "with" 语句之后的语句。

   如果语句体由于异常以外的任何原因退出，则来自 "__exit__()" 的返回值
   会被忽略，并会在该类退出正常的发生位置继续执行。

如果有多个项目，则会视作存在多个 "with" 语句嵌套来处理多个上下文管理器
:

   with A() as a, B() as b:
       suite

等价于

   with A() as a:
       with B() as b:
           suite

在 3.1 版更改: 支持多个上下文表达式。

参见:

  **PEP 343** - "with" 语句
     Python "with" 语句的规范描述、背景和示例。


8.6. 函数定义
=============

函数定义就是对用户自定义函数的定义（参见 标准类型层级结构 一节）:

   funcdef                 ::= [decorators] "def" funcname "(" [parameter_list] ")"
               ["->" expression] ":" suite
   decorators              ::= decorator+
   decorator               ::= "@" dotted_name ["(" [argument_list [","]] ")"] NEWLINE
   dotted_name             ::= identifier ("." identifier)*
   parameter_list          ::= defparameter ("," defparameter)* ["," [parameter_list_starargs]]
                      | parameter_list_starargs
   parameter_list_starargs ::= "*" [parameter] ("," defparameter)* ["," ["**" parameter [","]]]
                               | "**" parameter [","]
   parameter               ::= identifier [":" expression]
   defparameter            ::= parameter ["=" expression]
   funcname                ::= identifier

函数定义是一条可执行语句。 它执行时会在当前局部命名空间中将函数名称绑
定到一个函数对象（函数可执行代码的包装器）。 这个函数对象包含对当前全
局命名空间的引用，作为函数被调用时所使用的全局命名空间。

函数定义并不会执行函数体；只有当函数被调用时才会执行此操作。 [2]

一个函数定义可以被一个或多个 *decorator* 表达式所包装。 当函数被定义时
将在包含该函数定义的作用域中对装饰器表达式求值。 求值结果必须是一个可
调用对象，它会以该函数对象作为唯一参数被发起调用。 其返回值将被绑定到
函数名称而非函数对象。 多个装饰器会以嵌套方式被应用。 例如以下代码

   @f1(arg)
   @f2
   def func(): pass

大致等价于

   def func(): pass
   func = f1(arg)(f2(func))

不同之处在于原始函数并不会被临时绑定到名称 "func"。

当一个或多个 *形参* 具有 *形参* "=" *表达式* 这样的形式时，该函数就被
称为具有“默认形参值”。 对于一个具有默认值的形参，其对应的 *argument*
可以在调用中被省略，在此情况下会用形参的默认值来替代。 如果一个形参具
有默认值，后续所有在 ""*"" 之前的形参也必须具有默认值 --- 这个句法限制
并未在语法中明确表达。

**默认形参值会在执行函数定义时按从左至右的顺序被求值。** 这意味着当函
数被定义时将对表达式求值一次，相同的“预计算”值将在每次调用时被使用。
这一点在默认形参为可变对象，例如列表或字典的时候尤其需要重点理解：如果
函数修改了该对象（例如向列表添加了一项），则实际上默认值也会被修改。
这通常不是人们所预期的。 绕过此问题的一个方法是使用 "None" 作为默认值
，并在函数体中显式地对其进行测试，例如:

   def whats_on_the_telly(penguin=None):
       if penguin is None:
           penguin = []
       penguin.append("property of the zoo")
       return penguin

函数调用的语义在 调用 一节中有更详细的描述。 函数调用总是会给形参列表
中列出的所有形参赋值，或用位置参数，或用关键字参数，或用默认值。 如果
存在 ""*identifier"" 这样的形式，它会被初始化为一个元组来接收任何额外
的位置参数，默认为空元组。 如果存在 ""**identifier"" 这样的形式，它会
被初始化为一个新的有序映射来接收任何额外的关键字参数，默认为一个相同类
型的空映射。 在 ""*"" 或 ""*identifier"" 之后的形参都是仅关键字形参，
只能通过关键字参数传入值。

Parameters may have annotations of the form "": expression"" following
the parameter name.  Any parameter may have an annotation even those
of the form "*identifier" or "**identifier".  Functions may have
"return" annotation of the form ""-> expression"" after the parameter
list.  These annotations can be any valid Python expression and are
evaluated when the function definition is executed.  Annotations may
be evaluated in a different order than they appear in the source code.
The presence of annotations does not change the semantics of a
function.  The annotation values are available as values of a
dictionary keyed by the parameters' names in the "__annotations__"
attribute of the function object.

It is also possible to create anonymous functions (functions not bound
to a name), for immediate use in expressions.  This uses lambda
expressions, described in section lambda 表达式.  Note that the lambda
expression is merely a shorthand for a simplified function definition;
a function defined in a ""def"" statement can be passed around or
assigned to another name just like a function defined by a lambda
expression.  The ""def"" form is actually more powerful since it
allows the execution of multiple statements and annotations.

**程序员注意事项:** 函数属于一类对象。 在一个函数内部执行的 ""def"" 语
句会定义一个局部函数并可被返回或传递。 在嵌套函数中使用的自由变量可以
访问包含该 def 语句的函数的局部变量。 详情参见 命名与绑定 一节。

参见:

  **PEP 3107** - 函数标注
     最初的函数标注规范说明。


8.7. 类定义
===========

类定义就是对类对象的定义 (参见 标准类型层级结构 一节):

   classdef    ::= [decorators] "class" classname [inheritance] ":" suite
   inheritance ::= "(" [argument_list] ")"
   classname   ::= identifier

类定义是一条可执行语句。 其中继承列表通常给出基类的列表 (进阶用法请参
见 元类)，列表中的每一项都应当被求值为一个允许子类的类对象。 没有继承
列表的类默认继承自基类 "object"；因此，:

   class Foo:
       pass

等价于

   class Foo(object):
       pass

随后类体将在一个新的执行帧 (参见 命名与绑定) 中被执行，使用新创建的局
部命名空间和原有的全局命名空间。 （通常，类体主要包含函数定义。） 当类
体结束执行时，其执行帧将被丢弃而其局部命名空间会被保存。 [3] 一个类对
象随后会被创建，其基类使用给定的继承列表，属性字典使用保存的局部命名空
间。 类名称将在原有的全局命名空间中绑定到该类对象。

在类体内定义的属性的顺序保存在新类的 "__dict__" 中。 请注意此顺序的可
靠性只限于类刚被创建时，并且只适用于使用定义语法所定义的类。

类的创建可使用 元类 进行重度定制。

类也可以被装饰：就像装饰函数一样，:

   @f1(arg)
   @f2
   class Foo: pass

大致等价于

   class Foo: pass
   Foo = f1(arg)(f2(Foo))

装饰器表达式的求值规则与函数装饰器相同。 结果随后会被绑定到类名称。

**程序员注意事项:** 在类定义内定义的变量是类属性；它们将被类实例所共享
。 实例属性可通过 "self.name = value" 在方法中设定。 类和实例属性均可
通过 ""self.name"" 表示法来访问，当通过此方式访问时实例属性会隐藏同名
的类属性。 类属性可被用作实例属性的默认值，但在此场景下使用可变值可能
导致未预期的结果。 可以使用 描述器 来创建具有不同实现细节的实例变量。

参见:

  **PEP 3115** - Python 3000 中的元类
     将元类声明修改为当前语法的提议，以及关于如何构建带有元类的类的语
     义描述。

  **PEP 3129** - 类装饰器
     增加类装饰器的提议。 函数和方法装饰器是在 **PEP 318** 中被引入的
     。


8.8. 协程
=========

3.5 新版功能.


8.8.1. 协程函数定义
-------------------

   async_funcdef ::= [decorators] "async" "def" funcname "(" [parameter_list] ")"
                     ["->" expression] ":" suite

Execution of Python coroutines can be suspended and resumed at many
points (see *coroutine*).  In the body of a coroutine, any "await" and
"async" identifiers become reserved keywords; "await" expressions,
"async for" and "async with" can only be used in coroutine bodies.

使用 "async def" 语法定义的函数总是为协程函数，即使它们不包含 "await"
或 "async" 关键字。

It is a "SyntaxError" to use "yield from" expressions in "async def"
coroutines.

协程函数的例子:

   async def func(param1, param2):
       do_stuff()
       await some_coroutine()


8.8.2. The "async for" statement
--------------------------------

   async_for_stmt ::= "async" for_stmt

*asynchronous iterable* 能够在其 *iter* 实现中调用异步代码，而
*asynchronous iterator* 可以在其 *next* 方法中调用异步代码。

"async for" 语句允许方便地对异步迭代器进行迭代。

以下代码:

   async for TARGET in ITER:
       BLOCK
   else:
       BLOCK2

在语义上等价于:

   iter = (ITER)
   iter = type(iter).__aiter__(iter)
   running = True
   while running:
       try:
           TARGET = await type(iter).__anext__(iter)
       except StopAsyncIteration:
           running = False
       else:
           BLOCK
   else:
       BLOCK2

另请参阅 "__aiter__()" 和 "__anext__()" 了解详情。

It is a "SyntaxError" to use "async for" statement outside of an
"async def" function.


8.8.3. The "async with" statement
---------------------------------

   async_with_stmt ::= "async" with_stmt

*asynchronous context manager* 是一种 *context manager*，能够在其
*enter* 和 *exit* 方法中暂停执行。

以下代码:

   async with EXPR as VAR:
       BLOCK

在语义上等价于:

   mgr = (EXPR)
   aexit = type(mgr).__aexit__
   aenter = type(mgr).__aenter__(mgr)

   VAR = await aenter
   try:
       BLOCK
   except:
       if not await aexit(mgr, *sys.exc_info()):
           raise
   else:
       await aexit(mgr, None, None, None)

另请参阅 "__aenter__()" 和 "__aexit__()" 了解详情。

It is a "SyntaxError" to use "async with" statement outside of an
"async def" function.

参见:

  **PEP 492** - 使用 async 和 await 语法实现协程
     将协程作为 Python 中的一个正式的单独概念，并增加相应的支持语法。

-[ 脚注 ]-

[1] 异常会被传播给发起调用栈，除非存在一个 "finally" 子句正好引发
    了另 一个异常。 新引发的异常将导致旧异常的丢失。

[2] 作为函数体的第一条语句出现的字符串字面值会被转换为函数的
    "__doc__" 属性，也就是该函数的 *docstring*。

[3] 作为类体的第一条语句出现的字符串字面值会被转换为命名空间的
    "__doc__" 条目，也就是该类的 *docstring*。
