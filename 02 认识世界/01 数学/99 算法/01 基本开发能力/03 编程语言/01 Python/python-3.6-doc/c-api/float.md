---
title: float
toc: true
date: 2019-06-30
---
浮点数对象
**********

PyFloatObject

   这个 C 类型 "PyObject" 的子类型代表一个 Python 浮点数对象。

PyTypeObject PyFloat_Type

   这是个属于 C 类型 "PyTypeObject" 的代表 Python 浮点类型的实例。在 Python
   层面的类型 "float" 是同一个对象。

int PyFloat_Check(PyObject *p)

   当他的参数是一个 C 类型 "PyFloatObject" 或者是 C 类型 "PyFloatObject"
   的子类型时，返回真。

int PyFloat_CheckExact(PyObject *p)

   当他的参数是一个 C 类型 "PyFloatObject" 但不是 C 类型 "PyFloatObject"
   的子类型时，返回真。

PyObject* PyFloat_FromString(PyObject *str)
    *Return value: New reference.*

   根据字符串 *str* 的值，创建一个 C 类型 "PyFloatObject" 对象，失败时返
   回 *NULL* 。

PyObject* PyFloat_FromDouble(double v)
    *Return value: New reference.*

   根据 *v* 创建一个 C 类型 "PyFloatObject" 对象，失败时返回 *NULL* 。

double PyFloat_AsDouble(PyObject *pyfloat)

   返回一个代表 *pyfloat* 内容的 C 类型 "double"。如果 *float* 不是一个
   Python浮点数对象，但是包含 "__float__()" 方法，这个方法会首先被调用
   ，将 *pyfloat* 转换成一个浮点数。失败时这个方法返回 "-1.0"，所以应
   该调用 C 函数 "PyErr_Occurred()" 检查错误。

double PyFloat_AS_DOUBLE(PyObject *pyfloat)

   返回一个 *pyfloat* 内容的 C "double" 表示，但没有错误检查。

PyObject* PyFloat_GetInfo(void)

   返回一个 structseq 实例，其中包含有关 float 的精度、最小值和最大值
   的信息。 它是头文件 "float.h" 的一个简单包装。

double PyFloat_GetMax()

   返回最大可表示的有限浮点数 *DBL_MAX* 为 C "double" 。

double PyFloat_GetMin()

   返回最小可表示归一化正浮点数 *DBL_MIN* 为 C "double" 。

int PyFloat_ClearFreeList()

   清空浮点数释放列表。 返回无法释放的项目数。
