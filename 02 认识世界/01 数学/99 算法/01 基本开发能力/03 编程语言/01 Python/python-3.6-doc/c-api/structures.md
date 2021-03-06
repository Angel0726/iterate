---
title: structures
toc: true
date: 2019-06-30
---
Common Object Structures
************************

There are a large number of structures which are used in the
definition of object types for Python.  This section describes these
structures and how they are used.

All Python objects ultimately share a small number of fields at the
beginning of the object's representation in memory.  These are
represented by the "PyObject" and "PyVarObject" types, which are
defined, in turn, by the expansions of some macros also used, whether
directly or indirectly, in the definition of all other Python objects.

PyObject

   All object types are extensions of this type.  This is a type which
   contains the information Python needs to treat a pointer to an
   object as an object.  In a normal "release" build, it contains only
   the object's reference count and a pointer to the corresponding
   type object. Nothing is actually declared to be a "PyObject", but
   every pointer to a Python object can be cast to a "PyObject*".
   Access to the members must be done by using the macros "Py_REFCNT"
   and "Py_TYPE".

PyVarObject

   This is an extension of "PyObject" that adds the "ob_size" field.
   This is only used for objects that have some notion of *length*.
   This type does not often appear in the Python/C API. Access to the
   members must be done by using the macros "Py_REFCNT", "Py_TYPE",
   and "Py_SIZE".

PyObject_HEAD

   This is a macro used when declaring new types which represent
   objects without a varying length.  The PyObject_HEAD macro expands
   to:

      PyObject ob_base;

   See documentation of "PyObject" above.

PyObject_VAR_HEAD

   This is a macro used when declaring new types which represent
   objects with a length that varies from instance to instance. The
   PyObject_VAR_HEAD macro expands to:

      PyVarObject ob_base;

   See documentation of "PyVarObject" above.

Py_TYPE(o)

   This macro is used to access the "ob_type" member of a Python
   object. It expands to:

      (((PyObject*)(o))->ob_type)

Py_REFCNT(o)

   This macro is used to access the "ob_refcnt" member of a Python
   object. It expands to:

      (((PyObject*)(o))->ob_refcnt)

Py_SIZE(o)

   This macro is used to access the "ob_size" member of a Python
   object. It expands to:

      (((PyVarObject*)(o))->ob_size)

PyObject_HEAD_INIT(type)

   This is a macro which expands to initialization values for a new
   "PyObject" type.  This macro expands to:

      _PyObject_EXTRA_INIT
      1, type,

PyVarObject_HEAD_INIT(type, size)

   This is a macro which expands to initialization values for a new
   "PyVarObject" type, including the "ob_size" field. This macro
   expands to:

      _PyObject_EXTRA_INIT
      1, type, size,

PyCFunction

   Type of the functions used to implement most Python callables in C.
   Functions of this type take two "PyObject*" parameters and return
   one such value.  If the return value is *NULL*, an exception shall
   have been set.  If not *NULL*, the return value is interpreted as
   the return value of the function as exposed in Python.  The
   function must return a new reference.

PyCFunctionWithKeywords

   Type of the functions used to implement Python callables in C that
   take keyword arguments: they take three "PyObject*" parameters and
   return one such value.  See "PyCFunction" above for the meaning of
   the return value.

PyMethodDef

   Structure used to describe a method of an extension type.  This
   structure has four fields:

   +--------------------+---------------+---------------------------------+
   | Field              | C 类型        | 意义                            |
   |====================|===============|=================================|
   | "ml_name"          | char *        | name of the method              |
   +--------------------+---------------+---------------------------------+
   | "ml_meth"          | PyCFunction   | pointer to the C implementation |
   +--------------------+---------------+---------------------------------+
   | "ml_flags"         | int           | flag bits indicating how the    |
   |                    |               | call should be constructed      |
   +--------------------+---------------+---------------------------------+
   | "ml_doc"           | char *        | points to the contents of the   |
   |                    |               | docstring                       |
   +--------------------+---------------+---------------------------------+

The "ml_meth" is a C function pointer.  The functions may be of
different types, but they always return "PyObject*".  If the function
is not of the "PyCFunction", the compiler will require a cast in the
method table. Even though "PyCFunction" defines the first parameter as
"PyObject*", it is common that the method implementation uses the
specific C type of the *self* object.

The "ml_flags" field is a bitfield which can include the following
flags. The individual flags indicate either a calling convention or a
binding convention.  Of the calling convention flags, only
"METH_VARARGS" and "METH_KEYWORDS" can be combined. Any of the calling
convention flags can be combined with a binding flag.

METH_VARARGS

   This is the typical calling convention, where the methods have the
   type "PyCFunction". The function expects two "PyObject*" values.
   The first one is the *self* object for methods; for module
   functions, it is the module object.  The second parameter (often
   called *args*) is a tuple object representing all arguments. This
   parameter is typically processed using "PyArg_ParseTuple()" or
   "PyArg_UnpackTuple()".

METH_KEYWORDS

   Methods with these flags must be of type "PyCFunctionWithKeywords".
   The function expects three parameters: *self*, *args*, and a
   dictionary of all the keyword arguments.  The flag must be combined
   with "METH_VARARGS", and the parameters are typically processed
   using "PyArg_ParseTupleAndKeywords()".

METH_NOARGS

   Methods without parameters don't need to check whether arguments
   are given if they are listed with the "METH_NOARGS" flag.  They
   need to be of type "PyCFunction".  The first parameter is typically
   named *self* and will hold a reference to the module or object
   instance.  In all cases the second parameter will be *NULL*.

METH_O

   Methods with a single object argument can be listed with the
   "METH_O" flag, instead of invoking "PyArg_ParseTuple()" with a
   ""O"" argument. They have the type "PyCFunction", with the *self*
   parameter, and a "PyObject*" parameter representing the single
   argument.

These two constants are not used to indicate the calling convention
but the binding when use with methods of classes.  These may not be
used for functions defined for modules.  At most one of these flags
may be set for any given method.

METH_CLASS

   The method will be passed the type object as the first parameter
   rather than an instance of the type.  This is used to create *class
   methods*, similar to what is created when using the "classmethod()"
   built-in function.

METH_STATIC

   The method will be passed *NULL* as the first parameter rather than
   an instance of the type.  This is used to create *static methods*,
   similar to what is created when using the "staticmethod()" built-in
   function.

One other constant controls whether a method is loaded in place of
another definition with the same method name.

METH_COEXIST

   The method will be loaded in place of existing definitions.
   Without *METH_COEXIST*, the default is to skip repeated
   definitions.  Since slot wrappers are loaded before the method
   table, the existence of a *sq_contains* slot, for example, would
   generate a wrapped method named "__contains__()" and preclude the
   loading of a corresponding PyCFunction with the same name.  With
   the flag defined, the PyCFunction will be loaded in place of the
   wrapper object and will co-exist with the slot.  This is helpful
   because calls to PyCFunctions are optimized more than wrapper
   object calls.

PyMemberDef

   Structure which describes an attribute of a type which corresponds
   to a C struct member.  Its fields are:

   +--------------------+---------------+---------------------------------+
   | Field              | C 类型        | 意义                            |
   |====================|===============|=================================|
   | "name"             | char *        | name of the member              |
   +--------------------+---------------+---------------------------------+
   | "type"             | int           | the type of the member in the C |
   |                    |               | struct                          |
   +--------------------+---------------+---------------------------------+
   | "offset"           | Py_ssize_t    | the offset in bytes that the    |
   |                    |               | member is located on the type's |
   |                    |               | object struct                   |
   +--------------------+---------------+---------------------------------+
   | "flags"            | int           | flag bits indicating if the     |
   |                    |               | field should be read-only or    |
   |                    |               | writable                        |
   +--------------------+---------------+---------------------------------+
   | "doc"              | char *        | points to the contents of the   |
   |                    |               | docstring                       |
   +--------------------+---------------+---------------------------------+

   "type" can be one of many "T_" macros corresponding to various C
   types.  When the member is accessed in Python, it will be converted
   to the equivalent Python type.

   +-----------------+--------------------+
   | Macro name      | C数据类型          |
   |=================|====================|
   | T_SHORT         | short              |
   +-----------------+--------------------+
   | T_INT           | int                |
   +-----------------+--------------------+
   | T_LONG          | 长整型             |
   +-----------------+--------------------+
   | T_FLOAT         | float              |
   +-----------------+--------------------+
   | T_DOUBLE        | double             |
   +-----------------+--------------------+
   | T_STRING        | char *             |
   +-----------------+--------------------+
   | T_OBJECT        | PyObject *         |
   +-----------------+--------------------+
   | T_OBJECT_EX     | PyObject *         |
   +-----------------+--------------------+
   | T_CHAR          | char               |
   +-----------------+--------------------+
   | T_BYTE          | char               |
   +-----------------+--------------------+
   | T_UBYTE         | unsigned char      |
   +-----------------+--------------------+
   | T_UINT          | 无符号整型         |
   +-----------------+--------------------+
   | T_USHORT        | unsigned short     |
   +-----------------+--------------------+
   | T_ULONG         | 无符号长整型       |
   +-----------------+--------------------+
   | T_BOOL          | char               |
   +-----------------+--------------------+
   | T_LONGLONG      | long long          |
   +-----------------+--------------------+
   | T_ULONGLONG     | 无符号 long long   |
   +-----------------+--------------------+
   | T_PYSSIZET      | Py_ssize_t         |
   +-----------------+--------------------+

   "T_OBJECT" and "T_OBJECT_EX" differ in that "T_OBJECT" returns
   "None" if the member is *NULL* and "T_OBJECT_EX" raises an
   "AttributeError".  Try to use "T_OBJECT_EX" over "T_OBJECT" because
   "T_OBJECT_EX" handles use of the "del" statement on that attribute
   more correctly than "T_OBJECT".

   "flags" can be "0" for write and read access or "READONLY" for
   read-only access.  Using "T_STRING" for "type" implies "READONLY".
   "T_STRING" data is interpreted as UTF-8. Only "T_OBJECT" and
   "T_OBJECT_EX" members can be deleted.  (They are set to *NULL*).

PyGetSetDef

   Structure to define property-like access for a type. See also
   description of the "PyTypeObject.tp_getset" slot.

   +---------------+--------------------+-------------------------------------+
   | Field         | C 类型             | 意义                                |
   |===============|====================|=====================================|
   | name          | char *             | attribute name                      |
   +---------------+--------------------+-------------------------------------+
   | get           | getter             | C Function to get the attribute     |
   +---------------+--------------------+-------------------------------------+
   | set           | setter             | optional C function to set or       |
   |               |                    | delete the attribute, if omitted    |
   |               |                    | the attribute is readonly           |
   +---------------+--------------------+-------------------------------------+
   | doc           | char *             | optional docstring                  |
   +---------------+--------------------+-------------------------------------+
   | closure       | void *             | optional function pointer,          |
   |               |                    | providing additional data for       |
   |               |                    | getter and setter                   |
   +---------------+--------------------+-------------------------------------+

   The "get" function takes one "PyObject*" parameter (the instance)
   and a function pointer (the associated "closure"):

      typedef PyObject *(*getter)(PyObject *, void *);

   It should return a new reference on success or *NULL* with a set
   exception on failure.

   "set" functions take two "PyObject*" parameters (the instance and
   the value to be set) and a function pointer (the associated
   "closure"):

      typedef int (*setter)(PyObject *, PyObject *, void *);

   In case the attribute should be deleted the second parameter is
   *NULL*. Should return "0" on success or "-1" with a set exception
   on failure.
