---
title: bytearray
toc: true
date: 2019-06-30
---
字节数组对象
************

PyByteArrayObject

   这个 "PyObject" 的子类型表示一个 Python 字节数组对象。

PyTypeObject PyByteArray_Type

   Python bytearray 类型表示为 "PyTypeObject" 的实例；这与 Python 层面的
   "bytearray" 是相同的对象。


类型检查宏
==========

int PyByteArray_Check(PyObject *o)

   当对象 *o* 是一个字节数组对象而且是一个字节数组类型的子类型实例时，
   返回真。

int PyByteArray_CheckExact(PyObject *o)

   当对象 *o* 是一个字节数组对象，但不是一个字节数组类型的子类型实例时
   ，返回真。


直接 API 函数
=============

PyObject* PyByteArray_FromObject(PyObject *o)

   根据任何实现了 缓冲区协议 的对象 *o*，返回一个新的字节数组对象。

PyObject* PyByteArray_FromStringAndSize(const char *string, Py_ssize_t len)

   根据 *string* 和它的长度 *len* 生成一个字节数组对象。当失败时，返回
   *NULL*。

PyObject* PyByteArray_Concat(PyObject *a, PyObject *b)

   连接字节数组 *a* 和 *b* 并返回一个带有结果的新的字节数组。

Py_ssize_t PyByteArray_Size(PyObject *bytearray)

   在检查 *NULL* 指针后返回 *bytearray* 的大小。

char* PyByteArray_AsString(PyObject *bytearray)

   数组在检查 *NULL* 指针后以字符数组形式返回 *bytearray* 内容。返回的
   字符数组结尾总是加上额外的空字节。

int PyByteArray_Resize(PyObject *bytearray, Py_ssize_t len)

   将 *bytearray* 的内部缓冲区的大小调整为 *len*。


宏
==

这些宏减低安全性以换取性能，它们不检查指针。

char* PyByteArray_AS_STRING(PyObject *bytearray)

   C函数 "PyByteArray_AsString()" 的宏版本。

Py_ssize_t PyByteArray_GET_SIZE(PyObject *bytearray)

   C函数 "PyByteArray_Size()" 的宏版本。
