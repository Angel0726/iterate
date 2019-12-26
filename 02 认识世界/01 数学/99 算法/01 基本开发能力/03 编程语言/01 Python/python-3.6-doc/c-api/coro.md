---
title: coro
toc: true
date: 2019-06-30
---
协程对象
********

3.5 新版功能.

协程对象是使用 "async" 关键字声明的函数返回的。

PyCoroObject

   用于协程对象的 C 结构体。

PyTypeObject PyCoro_Type

   与协程对象对应的类型对​​象。

int PyCoro_CheckExact(PyObject *ob)

   如果 *ob* 的类型是 *PyCoro_Type*，则返回 true; *ob* 不得为 *NULL*。

PyObject* PyCoro_New(PyFrameObject *frame, PyObject *name, PyObject *qualname)
    *Return value: New reference.*

   基于 *frame* 对象创建并返回一个新的协程对象，其中 "__name__" 和
   ``__qualname__`` 设置为 *name* 和 *qualname* 。 此函数获取 *frame*
   的引用。 *frame* 参数不能是 *NULL*。
