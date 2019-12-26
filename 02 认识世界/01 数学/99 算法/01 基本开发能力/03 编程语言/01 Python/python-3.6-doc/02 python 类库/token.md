---
title: token
toc: true
date: 2019-06-30
---
32.5. "token" --- 与 Python 解析树一起使用的常量
**********************************************

**源码：** Lib/token.py

======================================================================

此模块提供表示解析树（终端令牌）的叶节点的数值的常量。 请参阅 Python
发行版中的文件 "Grammar/Grammar" ，以获取语言语法上下文中名称的定义。
名称映射到的特定数值可能会在 Python 版本之间更改。

该模块还提供从数字代码到名称和一些函数的映射。 这些函数镜像了 Python C
头文件中的定义。

token.tok_name

   将此模块中定义的常量的数值映射回名称字符串的字典，允许生成更加人类
   可读的解析树表示。

token.ISTERMINAL(x)

   对终端标记值返回 true 。

token.ISNONTERMINAL(x)

   对非终端标记值返回 true。

token.ISEOF(x)

   如果 *x* 是表示输入结束的标记，则返回 true。

标记常量是：

token.ENDMARKER
token.NAME
token.NUMBER
token.STRING
token.NEWLINE
token.INDENT
token.DEDENT
token.LPAR
token.RPAR
token.LSQB
token.RSQB
token.COLON
token.COMMA
token.SEMI
token.PLUS
token.MINUS
token.STAR
token.SLASH
token.VBAR
token.AMPER
token.LESS
token.GREATER
token.EQUAL
token.DOT
token.PERCENT
token.LBRACE
token.RBRACE
token.EQEQUAL
token.NOTEQUAL
token.LESSEQUAL
token.GREATEREQUAL
token.TILDE
token.CIRCUMFLEX
token.LEFTSHIFT
token.RIGHTSHIFT
token.DOUBLESTAR
token.PLUSEQUAL
token.MINEQUAL
token.STAREQUAL
token.SLASHEQUAL
token.PERCENTEQUAL
token.AMPEREQUAL
token.VBAREQUAL
token.CIRCUMFLEXEQUAL
token.LEFTSHIFTEQUAL
token.RIGHTSHIFTEQUAL
token.DOUBLESTAREQUAL
token.DOUBLESLASH
token.DOUBLESLASHEQUAL
token.AT
token.ATEQUAL
token.RARROW
token.ELLIPSIS
token.OP
token.AWAIT
token.ASYNC
token.ERRORTOKEN
token.N_TOKENS
token.NT_OFFSET

   在 3.5 版更改: Added "AWAIT" and "ASYNC" tokens. Starting with
   Python 3.7, "async" and "await" will be tokenized as "NAME" tokens,
   and "AWAIT" and "ASYNC" will be removed.
