---
title: file
toc: true
date: 2019-06-30
---
文件对象
********

这些 API 是内置文件对象的 Python 2 C API 的最小仿真，它过去依赖于 C 标准
库的缓冲 I/O（ "FILE*" ）支持。 在 Python 3中，文件和流使用新的 "io" 模
块，该模块在操作系统的低级无缓冲 I/O 上定义了几个层。 下面描述的函数是
针对这些新 API 的便捷 C 包装器，主要用于解释器中的内部错误报告；建议第三
方代码访问 "io" API。

PyFile_FromFd(int fd, const char *name, const char *mode, int buffering, const char *encoding, const char *errors, const char *newline, int closefd)

   从已打开文件 *fd* 的文件描述器创建 Python 文件对象。 参数 *name*、
   *encoding*、*errors* 和 *newline* 可以是 *NULL* 来使用默认值；
   *buffering* 可以是 *-1* 来使用默认值。 *name* 被忽略并以便向后兼容
   。 失败时返回 *NULL*。 有关参数的更全面描述，请参阅 "io.open()" 函
   数文档。

   警告: 由于 Python 流具有自己的缓冲层，因此将它们与 OS 级文件描述符
     混合会 产生各种问题（例如数据的意外排序）。

   在 3.2 版更改: 忽略 *name* 属性。

int PyObject_AsFileDescriptor(PyObject *p)

   将与 *p* 关联的文件描述器返回为 "int" 。 如果对象是整数，则返回其值
   。 如果没有，则调用对象的 "fileno()" 方法（如果存在）; 该方法必须返
   回一个整数，该整数作为文件描述器值返回。 设置异常并在失败时返回
   "-1"。

PyObject* PyFile_GetLine(PyObject *p, int n)
    *Return value: New reference.*

   等价于 "p.readline([n])" ，这个函数从对象 *p* 中读取一行。 *p* 可以
   是文件对象或具有 "readline()" 方法的任何对象。 如果 *n* 是 "0" ，则
   无论该行的长度如何，都会读取一行。 如果 *n* 大于``0``，则从文件中读
   取不超过 *n* 个字节；可以返回行的一部分。 在这两种情况下，如果立即
   到达文件末尾，则返回空字符串。 但是，如果 *n* 小于 "0" ，则无论长度
   如何都会读取一行，但是如果立即到达文件末尾，则引发 "EOFError"。

int PyFile_WriteObject(PyObject *obj, PyObject *p, int flags)

   将对象 *obj* 写入文件对象 *p* 。 *flags* 唯一支持的标志是
   "Py_PRINT_RAW"；如果给定，则写入对象的 "str()" 而不是 "repr()"。成
   功时返回 "0"，失败时返回 "-1"。 将设置适当的例外。

int PyFile_WriteString(const char *s, PyObject *p)

   将字符串 *s* 写入文件对象 *p*。 成功返回 "0" 失败返回 "-1"；将设定
   相应的异常。
