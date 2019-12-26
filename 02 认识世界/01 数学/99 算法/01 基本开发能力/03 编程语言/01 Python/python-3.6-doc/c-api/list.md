---
title: list
toc: true
date: 2019-06-30
---
列表对象
********

PyListObject

   这个 C 类型 "PyObject" 的子类型代表一个 Python 列表对象。

PyTypeObject PyList_Type

   这是个属于 "PyTypeObject" 的代表 Python 列表类型的实例。在 Python 层面
   和类型 "list" 是同一个对象。

int PyList_Check(PyObject *p)

   如果 *p* 是一个列表对象或者是一个列表类型的子类型实例时，返回真。

int PyList_CheckExact(PyObject *p)

   当 *p* 是一个列表对象，但是不是列表类型的子类型实例时，返回真。

PyObject* PyList_New(Py_ssize_t len)
    *Return value: New reference.*

   成功时返回长度为 *len* 的新列表，失败时返回 *NULL*。

   注解: 当 *len* 大于零时，被返回的列表对象项目被设成 "NULL"。因此
     你不能 用类似 C 函数 "PySequence_SetItem()" 的抽象 API 或者用 C 函数
     "PyList_SetItem()" 将所有项目设置成真实对象前对 Python 代码公开这个
     对象。

Py_ssize_t PyList_Size(PyObject *list)

   返回 *list* 中列表对象的长度；这等于在列表对象调用 "len(list)" 。

Py_ssize_t PyList_GET_SIZE(PyObject *list)

   宏版本的 C 函数 "PyList_Size()" ，没有错误检测。

PyObject* PyList_GetItem(PyObject *list, Py_ssize_t index)
    *Return value: Borrowed reference.*

   返回 *list* 指向的列表中位置 *index* 的对象。这个位置必需是正数，不
   支持倒叙索引。如果 *index* 超出边界，返回 *NULL* 并设置
   "IndexError" 异常。

PyObject* PyList_GET_ITEM(PyObject *list, Py_ssize_t i)
    *Return value: Borrowed reference.*

   宏版本的 C 函数 "PyList_GetItem()" ，没有错误检测。

int PyList_SetItem(PyObject *list, Py_ssize_t index, PyObject *item)

   将列表中索引为 *index* 的对象设为 *item*。成功时返回 "0" ，失败时返
   回 "-1" 。

   注解: This function "steals" a reference to *item* and discards a
     reference to an item already in the list at the affected
     position.

void PyList_SET_ITEM(PyObject *list, Py_ssize_t i, PyObject *o)

   Macro form of "PyList_SetItem()" without error checking. This is
   normally only used to fill in new lists where there is no previous
   content.

   注解: This macro "steals" a reference to *item*, and, unlike
     "PyList_SetItem()", does *not* discard a reference to any item
     that is being replaced; any reference in *list* at position *i*
     will be leaked.

int PyList_Insert(PyObject *list, Py_ssize_t index, PyObject *item)

   Insert the item *item* into list *list* in front of index *index*.
   Return "0" if successful; return "-1" and set an exception if
   unsuccessful. Analogous to "list.insert(index, item)".

int PyList_Append(PyObject *list, PyObject *item)

   Append the object *item* at the end of list *list*. Return "0" if
   successful; return "-1" and set an exception if unsuccessful.
   Analogous to "list.append(item)".

PyObject* PyList_GetSlice(PyObject *list, Py_ssize_t low, Py_ssize_t high)
    *Return value: New reference.*

   Return a list of the objects in *list* containing the objects
   *between* *low* and *high*.  Return *NULL* and set an exception if
   unsuccessful.  Analogous to "list[low:high]".  Negative indices, as
   when slicing from Python, are not supported.

int PyList_SetSlice(PyObject *list, Py_ssize_t low, Py_ssize_t high, PyObject *itemlist)

   Set the slice of *list* between *low* and *high* to the contents of
   *itemlist*.  Analogous to "list[low:high] = itemlist". The
   *itemlist* may be *NULL*, indicating the assignment of an empty
   list (slice deletion). Return "0" on success, "-1" on failure.
   Negative indices, as when slicing from Python, are not supported.

int PyList_Sort(PyObject *list)

   Sort the items of *list* in place.  Return "0" on success, "-1" on
   failure.  This is equivalent to "list.sort()".

int PyList_Reverse(PyObject *list)

   Reverse the items of *list* in place.  Return "0" on success, "-1"
   on failure.  This is the equivalent of "list.reverse()".

PyObject* PyList_AsTuple(PyObject *list)
    *Return value: New reference.*

   Return a new tuple object containing the contents of *list*;
   equivalent to "tuple(list)".

int PyList_ClearFreeList()

   Clear the free list. Return the total number of freed items.

   3.3 新版功能.
