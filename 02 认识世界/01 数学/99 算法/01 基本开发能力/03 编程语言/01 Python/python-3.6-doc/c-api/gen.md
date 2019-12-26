---
title: gen
toc: true
date: 2019-06-30
---
生成器对象
**********

生成器对象是 Python 用来实现生成器迭代器的对象。它们通常通过迭代产生值的
函数来创建，而不是显式调用 "PyGen_New()" 或 "PyGen_NewWithQualName()"
。

PyGenObject

   用于生成器对象的 C 结构体。

PyTypeObject PyGen_Type

   与生成器对象对应的类型对​​象。

int PyGen_Check(PyObject *ob)

   如果 *ob* 是一个生成器对象，则返回 true; *ob* 不得为 *NULL*。

int PyGen_CheckExact(PyObject *ob)

   如果 *ob* 的类型是 *PyGen_Type*，则返回真; *ob* 不得为 *NULL*。

PyObject* PyGen_New(PyFrameObject *frame)
    *Return value: New reference.*

   基于 *frame* 对象创建并返回一个新的生成器对象。 此函数获取 *frame*
   的引用。 参数不能是 *NULL*。

PyObject* PyGen_NewWithQualName(PyFrameObject *frame, PyObject *name, PyObject *qualname)
    *Return value: New reference.*

   基于 *frame* 对象创建并返回一个新的生成器对象，其中 "__name__" 和
   ``__qualname__`` 设置为 *name* 和 *qualname* 。 此函数获取 *frame*
   的引用。 *frame* 参数不能是 *NULL*。
