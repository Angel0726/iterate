---
title: index
toc: true
date: 2019-06-30
---
Python 标准库
*************

Python 语言参考 描述了 Python 语言的具体语法和语义，这份库参考则介绍了
与 Python 一同发行的标准库。它还描述了通常包含在 Python 发行版中的一些
可选组件。

Python 标准库非常庞大，所提供的组件涉及范围十分广泛，正如以下内容目录
所显示的。这个库包含了多个内置模块 (以 C 编写)，Python 程序员必须依靠
它们来实现系统级功能，例如文件 I/O，此外还有大量以 Python 编写的模块，
提供了日常编程中许多问题的标准解决方案。其中有些模块经过专门设计，通过
将特定平台功能抽象化为平台中立的 API 来鼓励和加强 Python 程序的可移植
性。

Windows 版本的 Python 安装程序通常包含整个标准库，往往还包含许多额外组
件。对于类 Unix 操作系统，Python 通常会分成一系列的软件包，因此可能需
要使用操作系统所提供的包管理工具来获取部分或全部可选组件。

在这个标准库以外还存在成千上万并且不断增加的其他组件 (从单独的程序、模
块、软件包直到完整的应用开发框架)，访问 Python 包索引 即可获取这些第三
方包。

* 1. 概述

* 2. 内置函数

* 3. 内置常量

  * 3.1. 由 "site" 模块添加的常量

* 4. 内置类型

  * 4.1. 逻辑值检测

  * 4.2. Boolean Operations --- "and", "or", "not"

  * 4.3. 比较

  * 4.4. 数字类型 --- "int", "float", "complex"

  * 4.5. 迭代器类型

  * 4.6. 序列类型 --- "list", "tuple", "range"

  * 4.7. 文本序列类型 --- "str"

  * 4.8. 二进制序列类型 --- "bytes", "bytearray", "memoryview"

  * 4.9. 集合类型 --- "set", "frozenset"

  * 4.10. 映射类型 --- "dict"

  * 4.11. 上下文管理器类型

  * 4.12. 其他内置类型

  * 4.13. 特殊属性

* 5. 内置异常

  * 5.1. 基类

  * 5.2. 具体异常

  * 5.3. 警告

  * 5.4. 异常层次结构

* 6. 文本处理服务

  * 6.1. "string" --- 常见的字符串操作

  * 6.2. "re" --- 正则表达式操作

  * 6.3. "difflib" --- 计算差异的辅助工具

  * 6.4. "textwrap" --- Text wrapping and filling

  * 6.5. "unicodedata" --- Unicode 数据库

  * 6.6. "stringprep" --- Internet String Preparation

  * 6.7. "readline" --- GNU readline interface

  * 6.8. "rlcompleter" --- GNU readline的完成函数

* 7. 二进制数据服务

  * 7.1. "struct" --- Interpret bytes as packed binary data

  * 7.2. "codecs" --- Codec registry and base classes

* 8. 数据类型

  * 8.1. "datetime" --- 基础 日期 和 时间 数据类型

  * 8.2. "calendar" --- 日历相关函数

  * 8.3. "collections" --- 容器数据类型

  * 8.4. "collections.abc" --- 容器的抽象基类

  * 8.5. "heapq" --- 堆队列算法

  * 8.6. "bisect" --- Array bisection algorithm

  * 8.7. "array" --- Efficient arrays of numeric values

  * 8.8. "weakref" --- 弱引用

  * 8.9. "types" --- Dynamic type creation and names for built-in
    types

  * 8.10. "copy" --- 浅层 (shallow) 和深层 (deep) 复制操作

  * 8.11. "pprint" --- 数据美化输出

  * 8.12. "reprlib" --- Alternate "repr()" implementation

  * 8.13. "enum" --- Support for enumerations

* 9. 数字和数学模块

  * 9.1. "numbers" --- 数字的抽象基类

  * 9.2. "math" --- 数学函数

  * 9.3. "cmath" ——关于复数的数学函数

  * 9.4. "decimal" --- 十进制定点和浮点运算

  * 9.5. "fractions" --- 分数

  * 9.6. "random" --- 生成伪随机数

  * 9.7. "statistics" --- Mathematical statistics functions

* 10. 函数式编程模块

  * 10.1. "itertools" --- 为高效循环而创建迭代器的函数

  * 10.2. "functools" --- 高阶函数和可调用对象上的操作

  * 10.3. "operator" --- 标准运算符替代函数

* 11. 文件和目录访问

  * 11.1. "pathlib" --- 面向对象的文件系统路径

  * 11.2. "os.path" --- 常见路径操作

  * 11.3. "fileinput" --- Iterate over lines from multiple input
    streams

  * 11.4. "stat" --- Interpreting "stat()" results

  * 11.5. "filecmp" --- File and Directory Comparisons

  * 11.6. "tempfile" --- Generate temporary files and directories

  * 11.7. "glob" --- Unix style pathname pattern expansion

  * 11.8. "fnmatch" --- Unix filename pattern matching

  * 11.9. "linecache" --- Random access to text lines

  * 11.10. "shutil" --- High-level file operations

  * 11.11. "macpath" --- Mac OS 9 路径操作函数

* 12. 数据持久化

  * 12.1. "pickle" —— Python 对象序列化

  * 12.2. "copyreg" --- Register "pickle" support functions

  * 12.3. "shelve" --- Python object persistence

  * 12.4. "marshal" --- Internal Python object serialization

  * 12.5. "dbm" --- Interfaces to Unix "databases"

  * 12.6. "sqlite3" --- SQLite 数据库 DB-API 2.0 接口模块

* 13. 数据压缩和存档

  * 13.1. "zlib" --- 与 **gzip** 兼容的压缩

  * 13.2. "gzip" --- 对 **gzip** 格式的支持

  * 13.3. "bz2" --- 对 **bzip2** 压缩算法的支持

  * 13.4. "lzma" --- 用 LZMA 算法压缩

  * 13.5. "zipfile" --- 使用 ZIP 存档

  * 13.6. "tarfile" --- 读写 tar 归档文件

* 14. 文件格式

  * 14.1. "csv" --- CSV 文件读写

  * 14.2. "configparser" --- Configuration file parser

  * 14.3. "netrc" --- netrc file processing

  * 14.4. "xdrlib" --- Encode and decode XDR data

  * 14.5. "plistlib" --- Generate and parse Mac OS X ".plist" files

* 15. 加密服务

  * 15.1. "hashlib" --- 安全哈希与消息摘要

  * 15.2. "hmac" --- 基于密钥的消息验证

  * 15.3. "secrets" --- Generate secure random numbers for managing
    secrets

* 16. 通用操作系统服务

  * 16.1. "os" --- 操作系统接口模块

  * 16.2. "io" --- 处理流的核心工具

  * 16.3. "time" --- 时间的访问和转换

  * 16.4. "argparse" --- 命令行选项、参数和子命令解析器

  * 16.5. "getopt" --- C-style parser for command line options

  * 16.6. 模块 "logging" --- Python 的日志记录工具

  * 16.7. "logging.config" --- 日志记录配置

  * 16.8. "logging.handlers" --- Logging handlers

  * 16.9. "getpass" --- 便携式密码输入工具

  * 16.10. "curses" --- 终端字符单元显示的处理

  * 16.11. "curses.textpad" --- Text input widget for curses
    programs

  * 16.12. "curses.ascii" --- Utilities for ASCII characters

  * 16.13. "curses.panel" --- A panel stack extension for curses

  * 16.14. "platform" ---  获取底层平台的标识数据

  * 16.15. "errno" --- Standard errno system symbols

  * 16.16. "ctypes" --- Python 的外部函数库

* 17. 并发执行

  * 17.1. "threading" --- 基于线程的并行

  * 17.2. "multiprocessing" --- 基于进程的并行

  * 17.3. "concurrent" 包

  * 17.4. "concurrent.futures" --- 启动并行任务

  * 17.5. "subprocess" --- 子进程管理

  * 17.6. "sched" --- 事件调度器

  * 17.7. "queue" --- 一个同步的队列类

  * 17.8. "dummy_threading" ---  可直接替代 "threading" 模块。

  * 17.9. "_thread" --- 底层多线程 API

  * 17.10. "_dummy_thread" --- "_thread" 的替代模块

* 18. Interprocess Communication and Networking

  * 18.1. "socket" --- 底层网络接口

  * 18.2. "ssl" --- TLS/SSL wrapper for socket objects

  * 18.3. "select" --- Waiting for I/O completion

  * 18.4. "selectors" --- 高级 I/O 复用库

  * 18.5. "asyncio" --- Asynchronous I/O, event loop, coroutines and
    tasks

  * 18.6. "asyncore" --- 异步 socket 处理器

  * 18.7. "asynchat" --- 异步 socket 指令/响应 处理器

  * 18.8. "signal" --- Set handlers for asynchronous events

  * 18.9. "mmap" --- 内存映射文件支持

* 19. 互联网数据处理

  * 19.1. "email" --- 电子邮件与 MIME 处理包

  * 19.2. "json" --- JSON 编码和解码器

  * 19.3. "mailcap" --- Mailcap file handling

  * 19.4. "mailbox" --- Manipulate mailboxes in various formats

  * 19.5. "mimetypes" --- Map filenames to MIME types

  * 19.6. "base64" --- Base16, Base32, Base64, Base85 数据编码

  * 19.7. "binhex" --- 对 binhex4 文件进行编码和解码

  * 19.8. "binascii" --- 二进制和 ASCII 码互转

  * 19.9. "quopri" --- Encode and decode MIME quoted-printable data

  * 19.10. "uu" --- Encode and decode uuencode files

* 20. 结构化标记处理工具

  * 20.1. "html" --- 超文本标记语言支持

  * 20.2. "html.parser" --- 简单的 HTML 和 XHTML 解析器

  * 20.3. "html.entities" --- HTML 一般实体的定义

  * 20.4. XML处理模块

  * 20.5. "xml.etree.ElementTree" --- The ElementTree XML API

  * 20.6. "xml.dom" --- The Document Object Model API

  * 20.7. "xml.dom.minidom" --- Minimal DOM implementation

  * 20.8. "xml.dom.pulldom" --- Support for building partial DOM
    trees

  * 20.9. "xml.sax" --- Support for SAX2 parsers

  * 20.10. "xml.sax.handler" --- Base classes for SAX handlers

  * 20.11. "xml.sax.saxutils" --- SAX Utilities

  * 20.12. "xml.sax.xmlreader" --- Interface for XML parsers

  * 20.13. "xml.parsers.expat" --- Fast XML parsing using Expat

* 21. 互联网协议和支持

  * 21.1. "webbrowser" --- 方便的 Web 浏览器控制器

  * 21.2. "cgi" --- Common Gateway Interface support

  * 21.3. "cgitb" --- Traceback manager for CGI scripts

  * 21.4. "wsgiref" --- WSGI Utilities and Reference Implementation

  * 21.5. "urllib" --- URL 处理模块

  * 21.6. "urllib.request" --- 用于打开 URL 的可扩展库

  * 21.7. "urllib.response" --- Response classes used by urllib

  * 21.8. "urllib.parse" --- Parse URLs into components

  * 21.9. "urllib.error" --- Exception classes raised by
    urllib.request

  * 21.10. "urllib.robotparser" ---  Parser for robots.txt

  * 21.11. "http" --- HTTP 模块

  * 21.12. *http.client* --- HTTP协议客户端

  * 21.13. "ftplib" --- FTP protocol client

  * 21.14. "poplib" --- POP3 protocol client

  * 21.15. "imaplib" --- IMAP4 protocol client

  * 21.16. "nntplib" --- NNTP protocol client

  * 21.17. "smtplib" ---SMTP协议客户端

  * 21.18. "smtpd" --- SMTP Server

  * 21.19. "telnetlib" --- Telnet client

  * 21.20. "uuid" --- UUID objects according to **RFC 4122**

  * 21.21. "socketserver" --- A framework for network servers

  * 21.22. "http.server" --- HTTP 服务器

  * 21.23. "http.cookies" --- HTTP state management

  * 21.24. "http.cookiejar" --- Cookie handling for HTTP clients

  * 21.25. "xmlrpc" --- XMLRPC 服务端与客户端模块

  * 21.26. "xmlrpc.client" --- XML-RPC client access

  * 21.27. "xmlrpc.server" --- Basic XML-RPC servers

  * 21.28. "ipaddress" --- IPv4/IPv6 manipulation library

* 22. 多媒体服务

  * 22.1. "audioop" --- Manipulate raw audio data

  * 22.2. "aifc" --- Read and write AIFF and AIFC files

  * 22.3. "sunau" --- 读写 Sun AU 文件

  * 22.4. "wave" --- 读写 WAV 格式文件

  * 22.5. "chunk" --- Read IFF chunked data

  * 22.6. "colorsys" --- 颜色系统间的转换

  * 22.7. "imghdr" --- 推测图像类型

  * 22.8. "sndhdr" --- 推测声音文件的类型

  * 22.9. "ossaudiodev" --- Access to OSS-compatible audio devices

* 23. 国际化

  * 23.1. "gettext" --- 多语种国际化服务

  * 23.2. "locale" --- 国际化服务

* 24. 程序框架

  * 24.1. "turtle" --- 海龟绘图

  * 24.2. "cmd" --- 支持面向行的命令解释器

  * 24.3. "shlex" --- Simple lexical analysis

* 25. Tk图形用户界面(GUI)

  * 25.1. "tkinter" --- Tcl/Tk的 Python 接口

  * 25.2. "tkinter.ttk" --- Tk themed widgets

  * 25.3. "tkinter.tix" --- Extension widgets for Tk

  * 25.4. "tkinter.scrolledtext" --- 滚动文字控件

  * 25.5. IDLE

  * 25.6. 其他图形用户界面（GUI）包

* 26. 开发工具

  * 26.1. "typing" --- 类型标注支持

  * 26.2. "pydoc" --- Documentation generator and online help system

  * 26.3. "doctest" --- 测试交互性的 Python 示例

  * 26.4. "unittest" --- 单元测试框架

  * 26.5. "unittest.mock" --- mock object library

  * 26.6. "unittest.mock" 上手指南

  * 26.7. 2to3 - 自动将 Python 2 代码转为 Python 3 代码

  * 26.8. "test" --- Regression tests package for Python

  * 26.9. "test.support" --- Utilities for the Python test suite

* 27. 调试和分析

  * 27.1. "bdb" --- Debugger framework

  * 27.2. "faulthandler" --- Dump the Python traceback

  * 27.3. "pdb" --- Python的调试器

  * 27.4. The Python Profilers

  * 27.5. "timeit" --- 测量小代码片段的执行时间

  * 27.6. "trace" --- Trace or track Python statement execution

  * 27.7. "tracemalloc" --- Trace memory allocations

* 28. 软件打包和分发

  * 28.1. "distutils" --- 构建和安装 Python 模块

  * 28.2. "ensurepip" --- Bootstrapping the "pip" installer

  * 28.3. "venv" --- 创建虚拟环境

  * 28.4. "zipapp" --- Manage executable Python zip archives

* 29. Python运行时服务

  * 29.1. "sys" --- 系统相关的参数和函数

  * 29.2. "sysconfig" --- Provide access to Python's configuration
    information

  * 29.3. "builtins" --- 内建对象

  * 29.4. "__main__" --- 顶层脚本环境

  * 29.5. "warnings" --- Warning control

  * 29.6. "contextlib" --- Utilities for "with"-statement contexts

  * 29.7. "abc" --- 抽象基类

  * 29.8. "atexit" --- 退出处理器

  * 29.9. "traceback" --- 打印或检索堆栈回溯

  * 29.10. "__future__" --- Future 语句定义

  * 29.11. "gc" --- 垃圾回收器接口

  * 29.12. "inspect" --- 检查对象

  * 29.13. "site" --- Site-specific configuration hook

  * 29.14. "fpectl" --- Floating point exception control

* 30. 自定义 Python 解释器

  * 30.1. "code" --- Interpreter base classes

  * 30.2. "codeop" --- Compile Python code

* 31. 导入模块

  * 31.1. "zipimport" --- Import modules from Zip archives

  * 31.2. "pkgutil" --- Package extension utility

  * 31.3. "modulefinder" --- 查找脚本使用的模块

  * 31.4. "runpy" --- Locating and executing Python modules

  * 31.5. "importlib" --- The implementation of "import"

* 32. Python 语言服务

  * 32.1. "parser" --- Access Python parse trees

  * 32.2. "ast" --- 抽象语法树

  * 32.3. "symtable" --- Access to the compiler's symbol tables

  * 32.4. "symbol" --- 与 Python 解析树一起使用的常量

  * 32.5. "token" --- 与 Python 解析树一起使用的常量

  * 32.6. "keyword" --- 检验 Python 关键字

  * 32.7. "tokenize" --- Tokenizer for Python source

  * 32.8. "tabnanny" --- 模糊缩进检测

  * 32.9. "pyclbr" --- Python class browser support

  * 32.10. "py_compile" --- Compile Python source files

  * 32.11. "compileall" --- Byte-compile Python libraries

  * 32.12. "dis" --- Python 字节码反汇编器

  * 32.13. "pickletools" --- Tools for pickle developers

* 33. 杂项服务

  * 33.1. "formatter" --- Generic output formatting

* 34. Windows系统相关模块

  * 34.1. "msilib" --- Read and write Microsoft Installer files

  * 34.2. "msvcrt" --- Useful routines from the MS VC++ runtime

  * 34.3. "winreg" --- Windows 注册表访问

  * 34.4. "winsound" --- Sound-playing interface for Windows

* 35. Unix 专有服务

  * 35.1. "posix" --- The most common POSIX system calls

  * 35.2. "pwd" --- 用户密码数据库

  * 35.3. "spwd" --- The shadow password database

  * 35.4. "grp" --- The group database

  * 35.5. "crypt" --- Function to check Unix passwords

  * 35.6. "termios" --- POSIX style tty control

  * 35.7. "tty" --- 终端控制功能

  * 35.8. "pty" --- Pseudo-terminal utilities

  * 35.9. "fcntl" --- The "fcntl" and "ioctl" system calls

  * 35.10. "pipes" --- Interface to shell pipelines

  * 35.11. "resource" --- Resource usage information

  * 35.12. "nis" --- Interface to Sun's NIS (Yellow Pages)

  * 35.13. Unix syslog 库例程

* 36. 被取代的模块

  * 36.1. "optparse" --- Parser for command line options

  * 36.2. "imp" --- Access the *import* internals

* 37. 未创建文档的模块

  * 37.1. 平台特定模块
