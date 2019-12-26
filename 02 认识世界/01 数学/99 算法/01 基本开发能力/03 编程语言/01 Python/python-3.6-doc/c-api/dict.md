---
title: dict
toc: true
date: 2019-06-30
---
字典对象
********

PyDictObject

   这个 "PyObject" 的子类型代表一个 Python 字典对象。

PyTypeObject PyDict_Type

   Python字典类型表示为 "PyTypeObject" 的实例。这与 Python 层面的 "dict"
   是相同的对象。

int PyDict_Check(PyObject *p)

   如果 *p* 是字典对象或者字典类型的子类型的实例，则返回真。

int PyDict_CheckExact(PyObject *p)

   如果 *p* 是字典对象但不是字典类型的子类型的实例，则返回真。

PyObject* PyDict_New()
    *Return value: New reference.*

   返回一个新的空字典或者返回 *NULL* 表示失败。

PyObject* PyDictProxy_New(PyObject *mapping)
    *Return value: New reference.*

   返回 "types.MappingProxyType" 对象，用于强制执行只读行为的映射。这
   通常用于创建视图以防止修改非动态类类型的字典。

void PyDict_Clear(PyObject *p)

   清空现有字典的所有键值对。

int PyDict_Contains(PyObject *p, PyObject *key)

   确定 *key* 是否包含在字典 *p* 中。如果 *key* 匹配上 *p* 的某一项，
   则返回 "1" ，否则返回 "0" 。返回 "-1" 表示出错。这等同于 Python 表达
   式 "key in p" 。

PyObject* PyDict_Copy(PyObject *p)
    *Return value: New reference.*

   返回与 *p* 包含相同键值对的新字典。

int PyDict_SetItem(PyObject *p, PyObject *key, PyObject *val)

   使用 *key* 作为键将 *value* 插入字典 *p* 。 *key* 必须为 *hashable*
   ；如果不是，会抛出 "TypeError" 异常。成功返回 "0" ，失败返回 "-1"
   。

int PyDict_SetItemString(PyObject *p, const char *key, PyObject *val)

   Insert *value* into the dictionary *p* using *key* as a key. *key*
   should be a "char*".  The key object is created using
   "PyUnicode_FromString(key)".  Return "0" on success or "-1" on
   failure.

int PyDict_DelItem(PyObject *p, PyObject *key)

   使用键 *key* 删除字典 *p* 中的条目。 *key* 必须是可哈希的；如果不是
   ，则抛出 "TypeError" 异常。成功时返回 "0" ，失败时返回 "-1" 。

int PyDict_DelItemString(PyObject *p, const char *key)

   删除字典 *p* 中的条目，其中包含由字符串 *key* 指定的键。成功时返回
   “0，失败时返回“-1”。

PyObject* PyDict_GetItem(PyObject *p, PyObject *key)
    *Return value: Borrowed reference.*

   返回字典 *p* 中 *key* 作为键的对象。如果键 *key* 不存在则返回
   *NULL* ，但可以使用 *without* 设置例外。

   需要注意的是，调用 "__hash__()" 和 "__eq__()" 方法产生的异常不会被
   抛出。改用 "PyDict_GetItemWithError()" 获得错误报告。

PyObject* PyDict_GetItemWithError(PyObject *p, PyObject *key)

   变种的 "PyDict_GetItem()" 会抛出异常。当异常发生，返回 *NULL* **并
   ** 设置异常。当键不存在，返回 *NULL* **但不会** 设置异常。

PyObject* PyDict_GetItemString(PyObject *p, const char *key)
    *Return value: Borrowed reference.*

   This is the same as "PyDict_GetItem()", but *key* is specified as a
   "char*", rather than a "PyObject*".

   需要注意的是，调用 "__hash__()" 、 "__eq__()" 方法和创建一个临时的
   字符串对象时产生的异常不会被抛出。改用 "PyDict_GetItemWithError()"
   获得错误报告。

PyObject* PyDict_SetDefault(PyObject *p, PyObject *key, PyObject *default)
    *Return value: Borrowed reference.*

   这跟 Python 层面的 "dict.setdefault()" 一样。如果键 *key* 存在，它返
   回在字典 *p* 里面对应的值。如果键不存在，它会和值 *defaultobj* 一起
   插入并返回 *defaultobj* 。这个函数只计算 *key* 的哈希函数一次，而不
   是在查找和插入时分别计算它。

   3.4 新版功能.

PyObject* PyDict_Items(PyObject *p)
    *Return value: New reference.*

   返回一个包含字典中所有键值项的 "PyListObject"。

PyObject* PyDict_Keys(PyObject *p)
    *Return value: New reference.*

   返回一个包含字典中所有键(keys)的 "PyListObject"。

PyObject* PyDict_Values(PyObject *p)
    *Return value: New reference.*

   返回一个包含字典中所有值(values)的 "PyListObject"。

Py_ssize_t PyDict_Size(PyObject *p)

   返回字典中项目数，等价于对字典 *p* 使用 "len(p)"。

int PyDict_Next(PyObject *p, Py_ssize_t *ppos, PyObject **pkey, PyObject **pvalue)

   Iterate over all key-value pairs in the dictionary *p*.  The
   "Py_ssize_t" referred to by *ppos* must be initialized to "0" prior
   to the first call to this function to start the iteration; the
   function returns true for each pair in the dictionary, and false
   once all pairs have been reported.  The parameters *pkey* and
   *pvalue* should either point to "PyObject*" variables that will be
   filled in with each key and value, respectively, or may be *NULL*.
   Any references returned through them are borrowed.  *ppos* should
   not be altered during iteration. Its value represents offsets
   within the internal dictionary structure, and since the structure
   is sparse, the offsets are not consecutive.

   例如:

      PyObject *key, *value;
      Py_ssize_t pos = 0;

      while (PyDict_Next(self->dict, &pos, &key, &value)) {
          /* do something interesting with the values... */
          ...
      }

   字典 *p* 不应该在遍历期间发生改变。在遍历字典时，改变键中的值是安全
   的，但仅限于键的集合不发生改变。例如:

      PyObject *key, *value;
      Py_ssize_t pos = 0;

      while (PyDict_Next(self->dict, &pos, &key, &value)) {
          long i = PyLong_AsLong(value);
          if (i == -1 && PyErr_Occurred()) {
              return -1;
          }
          PyObject *o = PyLong_FromLong(i + 1);
          if (o == NULL)
              return -1;
          if (PyDict_SetItem(self->dict, key, o) < 0) {
              Py_DECREF(o);
              return -1;
          }
          Py_DECREF(o);
      }

int PyDict_Merge(PyObject *a, PyObject *b, int override)

   Iterate over mapping object *b* adding key-value pairs to
   dictionary *a*. *b* may be a dictionary, or any object supporting
   "PyMapping_Keys()" and "PyObject_GetItem()". If *override* is true,
   existing pairs in *a* will be replaced if a matching key is found
   in *b*, otherwise pairs will only be added if there is not a
   matching key in *a*. Return "0" on success or "-1" if an exception
   was raised.

int PyDict_Update(PyObject *a, PyObject *b)

   This is the same as "PyDict_Merge(a, b, 1)" in C, and is similar to
   "a.update(b)" in Python except that "PyDict_Update()" doesn't fall
   back to the iterating over a sequence of key value pairs if the
   second argument has no "keys" attribute.  Return "0" on success or
   "-1" if an exception was raised.

int PyDict_MergeFromSeq2(PyObject *a, PyObject *seq2, int override)

   Update or merge into dictionary *a*, from the key-value pairs in
   *seq2*. *seq2* must be an iterable object producing iterable
   objects of length 2, viewed as key-value pairs.  In case of
   duplicate keys, the last wins if *override* is true, else the first
   wins. Return "0" on success or "-1" if an exception was raised.
   Equivalent Python (except for the return value):

      def PyDict_MergeFromSeq2(a, seq2, override):
          for key, value in seq2:
              if override or key not in a:
                  a[key] = value

int PyDict_ClearFreeList()

   Clear the free list. Return the total number of freed items.

   3.3 新版功能.
