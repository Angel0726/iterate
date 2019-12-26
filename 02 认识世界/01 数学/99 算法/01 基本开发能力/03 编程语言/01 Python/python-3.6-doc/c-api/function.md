---
title: function
toc: true
date: 2019-06-30
---
函数对象
********

有一些特定于 Python 函数的函数。

PyFunctionObject

   用于函数的 C 结构体。

PyTypeObject PyFunction_Type

   这是一个 "PyTypeObject" 实例并表示 Python 函数类型。 它作为
   "types.FunctionType" 向 Python 程序员公开。

int PyFunction_Check(PyObject *o)

   如果 *o* 是函数对象（具有类型 "PyFunction_Type" ），则返回 true 。
   参数不能为 *NULL* 。

PyObject* PyFunction_New(PyObject *code, PyObject *globals)
    *Return value: New reference.*

   返回与代码对象 *code* 关联的新函数对象。 *globals* 必须是一个字典，
   该函数可以访问全局变量。

   从代码对象中检索函数的 docstring 和 name 。 *__module__* 从
   *globals* 中检索。 参数 defaults 、 annotations 和 closure 设置为
   *NULL* 。 *__qualname__* 设置为与函数名称相同的值。

PyObject* PyFunction_NewWithQualName(PyObject *code, PyObject *globals, PyObject *qualname)
    *Return value: New reference.*

   如 "PyFunction_New()" ，但也允许设置函数对象的 "__qualname__" 属性
   。 *qualname* 应该是 unicode 对象或 NULL ；如果为 NULL ，则
   "__qualname__" 属性设置为与 "__name__" 属性相同的值。

   3.3 新版功能.

PyObject* PyFunction_GetCode(PyObject *op)
    *Return value: Borrowed reference.*

   返回与函数对象 *op* 关联的代码对象。

PyObject* PyFunction_GetGlobals(PyObject *op)
    *Return value: Borrowed reference.*

   返回与函数对象*op*相关联的全局字典。

PyObject* PyFunction_GetModule(PyObject *op)
    *Return value: Borrowed reference.*

   返回函数对象 *op* 的 *__module__* 属性，通常为一个包含了模块名称的
   字符串，但可以通过 Python 代码设为返回其他任意对象。

PyObject* PyFunction_GetDefaults(PyObject *op)
    *Return value: Borrowed reference.*

   返回函数对象 *op* 的参数缺省值 (argument default values) ，该返回值
   为一个参数元组或者 *NULL*。

int PyFunction_SetDefaults(PyObject *op, PyObject *defaults)

   为函数对象 *op* 设置参数缺省值。 *defaults* 必须为 *Py_None* 或一个
   元组。

   失败时引发 "SystemError" 异常并返回 "-1" 。

PyObject* PyFunction_GetClosure(PyObject *op)
    *Return value: Borrowed reference.*

   返回与函数对象 *op* 相关联的闭包。它可以是 *NULL* 或者是一个由 cell
   对象组成的元组。

int PyFunction_SetClosure(PyObject *op, PyObject *closure)

   设置与函数对象 *op* 相关联的闭包。这个*闭包*必须是 *Py_None* 或者是
   一个由 cell 对象组成的元组。

   失败时引发 "SystemError" 异常并返回 "-1" 。

PyObject *PyFunction_GetAnnotations(PyObject *op)

   返回函数对象 *op* 的注解。它可以是一个可变字典或 *NULL*。

int PyFunction_SetAnnotations(PyObject *op, PyObject *annotations)

   设置函数对象 *op* 的注解。这个*注解*必须是一个字典或者 *Py_None*。

   失败时引发 "SystemError" 异常并返回 "-1" 。
