---
title: bytes
toc: true
date: 2019-06-30
---
字节对象
********

当期望带一个字节串形参但却带一个非字节串形参被调用时，这些函数会引发
"TypeError"。

PyBytesObject

   这种 "PyObject" 的子类型表示一个 Python 字节对象。

PyTypeObject PyBytes_Type

   "PyTypeObject" 的实例代表一个 Python 字节类型，在 Python 层面它与
   "bytes" 是相同的对象。

int PyBytes_Check(PyObject *o)

   Return true if the object *o* is a bytes object or an instance of a
   subtype of the bytes type.

int PyBytes_CheckExact(PyObject *o)

   Return true if the object *o* is a bytes object, but not an
   instance of a subtype of the bytes type.

PyObject* PyBytes_FromString(const char *v)

   返回一个新字节对象，成功时返回字符串*v*的副本作为值，失败时返回
   *NULL*。形参*v*不能为*NULL*；它不会被检查。

PyObject* PyBytes_FromStringAndSize(const char *v, Py_ssize_t len)

   返回一个新字节对象，成功时将字符串*v*的副本作为值和长度*len*，失败
   时返回*NULL*。如果*v*为*NULL*，则字节对象的内容未初始化。

PyObject* PyBytes_FromFormat(const char *format, ...)

   Take a C "printf()"-style *format* string and a variable number of
   arguments, calculate the size of the resulting Python bytes object
   and return a bytes object with the values formatted into it.  The
   variable arguments must be C types and must correspond exactly to
   the format characters in the *format* string.  The following format
   characters are allowed:

   +---------------------+-----------------+----------------------------------+
   | 格式字符            | 类型            | 注释                             |
   |=====================|=================|==================================|
   | "%%"                | *不适用*        | 文字%字符。                      |
   +---------------------+-----------------+----------------------------------+
   | "%c"                | int             | 一个字节，被表示为一个 C 语言的  |
   |                     |                 | 整型                             |
   +---------------------+-----------------+----------------------------------+
   | "%d"                | int             | Exactly equivalent to            |
   |                     |                 | "printf("%d")".                  |
   +---------------------+-----------------+----------------------------------+
   | "%u"                | 无符号整型      | Exactly equivalent to            |
   |                     |                 | "printf("%u")".                  |
   +---------------------+-----------------+----------------------------------+
   | "%ld"               | 长整型          | Exactly equivalent to            |
   |                     |                 | "printf("%ld")".                 |
   +---------------------+-----------------+----------------------------------+
   | "%lu"               | 无符号长整型    | Exactly equivalent to            |
   |                     |                 | "printf("%lu")".                 |
   +---------------------+-----------------+----------------------------------+
   | "%zd"               | Py_ssize_t      | Exactly equivalent to            |
   |                     |                 | "printf("%zd")".                 |
   +---------------------+-----------------+----------------------------------+
   | "%zu"               | size_t          | Exactly equivalent to            |
   |                     |                 | "printf("%zu")".                 |
   +---------------------+-----------------+----------------------------------+
   | "%i"                | int             | Exactly equivalent to            |
   |                     |                 | "printf("%i")".                  |
   +---------------------+-----------------+----------------------------------+
   | "%x"                | int             | Exactly equivalent to            |
   |                     |                 | "printf("%x")".                  |
   +---------------------+-----------------+----------------------------------+
   | "%s"                | char*           | A null-terminated C character    |
   |                     |                 | array.                           |
   +---------------------+-----------------+----------------------------------+
   | "%p"                | void*           | The hex representation of a C    |
   |                     |                 | pointer. Mostly equivalent to    |
   |                     |                 | "printf("%p")" except that it is |
   |                     |                 | guaranteed to start with the     |
   |                     |                 | literal "0x" regardless of what  |
   |                     |                 | the platform's "printf" yields.  |
   +---------------------+-----------------+----------------------------------+

   An unrecognized format character causes all the rest of the format
   string to be copied as-is to the result object, and any extra
   arguments discarded.

PyObject* PyBytes_FromFormatV(const char *format, va_list vargs)

   Identical to "PyBytes_FromFormat()" except that it takes exactly
   two arguments.

PyObject* PyBytes_FromObject(PyObject *o)

   Return the bytes representation of object *o* that implements the
   buffer protocol.

Py_ssize_t PyBytes_Size(PyObject *o)

   Return the length of the bytes in bytes object *o*.

Py_ssize_t PyBytes_GET_SIZE(PyObject *o)

   Macro form of "PyBytes_Size()" but without error checking.

char* PyBytes_AsString(PyObject *o)

   Return a pointer to the contents of *o*.  The pointer refers to the
   internal buffer of *o*, which consists of "len(o) + 1" bytes.  The
   last byte in the buffer is always null, regardless of whether there
   are any other null bytes.  The data must not be modified in any
   way, unless the object was just created using
   "PyBytes_FromStringAndSize(NULL, size)". It must not be
   deallocated.  If *o* is not a bytes object at all,
   "PyBytes_AsString()" returns *NULL* and raises "TypeError".

char* PyBytes_AS_STRING(PyObject *string)

   Macro form of "PyBytes_AsString()" but without error checking.

int PyBytes_AsStringAndSize(PyObject *obj, char **buffer, Py_ssize_t *length)

   Return the null-terminated contents of the object *obj* through the
   output variables *buffer* and *length*.

   If *length* is *NULL*, the bytes object may not contain embedded
   null bytes; if it does, the function returns "-1" and a
   "ValueError" is raised.

   The buffer refers to an internal buffer of *obj*, which includes an
   additional null byte at the end (not counted in *length*).  The
   data must not be modified in any way, unless the object was just
   created using "PyBytes_FromStringAndSize(NULL, size)".  It must not
   be deallocated.  If *obj* is not a bytes object at all,
   "PyBytes_AsStringAndSize()" returns "-1" and raises "TypeError".

   在 3.5 版更改: Previously, "TypeError" was raised when embedded
   null bytes were encountered in the bytes object.

void PyBytes_Concat(PyObject **bytes, PyObject *newpart)

   Create a new bytes object in **bytes* containing the contents of
   *newpart* appended to *bytes*; the caller will own the new
   reference.  The reference to the old value of *bytes* will be
   stolen.  If the new object cannot be created, the old reference to
   *bytes* will still be discarded and the value of **bytes* will be
   set to *NULL*; the appropriate exception will be set.

void PyBytes_ConcatAndDel(PyObject **bytes, PyObject *newpart)

   Create a new bytes object in **bytes* containing the contents of
   *newpart* appended to *bytes*.  This version decrements the
   reference count of *newpart*.

int _PyBytes_Resize(PyObject **bytes, Py_ssize_t newsize)

   A way to resize a bytes object even though it is "immutable". Only
   use this to build up a brand new bytes object; don't use this if
   the bytes may already be known in other parts of the code.  It is
   an error to call this function if the refcount on the input bytes
   object is not one. Pass the address of an existing bytes object as
   an lvalue (it may be written into), and the new size desired.  On
   success, **bytes* holds the resized bytes object and "0" is
   returned; the address in **bytes* may differ from its input value.
   If the reallocation fails, the original bytes object at **bytes* is
   deallocated, **bytes* is set to *NULL*, "MemoryError" is set, and
   "-1" is returned.
