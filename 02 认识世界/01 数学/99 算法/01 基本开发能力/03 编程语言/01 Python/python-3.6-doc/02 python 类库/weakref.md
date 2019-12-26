---
title: weakref
toc: true
date: 2019-06-30
---
8.8. "weakref" --- 弱引用
*************************

**源码：** Lib/weakref.py

======================================================================

"weakref" 模块允许 Python 程序员创建对象的 *weak references* 。

在下文中，术语 *referent* 表示由弱引用引用的对象。

对对象的弱引用不能保证对象存活：当对像的引用只剩弱引用时， *garbage
collection* 可以销毁引用并将其内存重用于其他内容。但是，在实际销毁对象
之前，即使没有强引用，弱引用也一直能返回该对象。

弱引用的主要用途是实现保存大对象的高速缓存或映射，但又并希望大对象仅仅
因为它出现在高速缓存或映射中而保持存活。

例如，如果您有许多大型二进制图像对象，则可能希望将名称与每个对象关联起
来。如果您使用 Python 字典将名称映射到图像，或将图像映射到名称，则图像对
象将保持活动状态，因为它们在字典中显示为值或键。 "weakref" 模块提供的
"WeakKeyDictionary" 和 "WeakValueDictionary" 类可以替代 Python 字典，使
用弱引用来构造映射，这些映射不会仅仅因为它们出现在映射对象中而使对象保
持存活。例如，如果一个图像对象是 "WeakValueDictionary" 中的值，那么当
对该图像对象的剩余引用是弱映射对象所持有的弱引用时，垃圾回收可以回收该
对象并将其在弱映射对象中相应的条目删除。

"WeakKeyDictionary" 和 "WeakValueDictionary" 在它们的实现中使用弱引用
，在弱引用上设置回调函数，当键或值被垃圾回收回收时通知弱字典。
"WeakSet" 实现了 "set" 接口，但像 "WeakKeyDictionary" 一样，只持有其元
素的弱引用。`

"finalize" 提供了注册一个对象被垃圾收集时要调用的清理函数的方式。这比
在原始弱引用上设置回调函数更简单，因为模块会自动确保对象被回收前终结器
一直保持存活。

这些弱容器类型之一或者 "finalize" 就是大多数程序所需要的 - 通常不需要
直接创建自己的弱引用。"weakref" 模块暴露了低级机制，以便于高级用途。

Not all objects can be weakly referenced; those objects which can
include class instances, functions written in Python (but not in C),
instance methods, sets, frozensets, some *file objects*, *generator*s,
type objects, sockets, arrays, deques, regular expression pattern
objects, and code objects.

在 3.2 版更改: 添加了对 thread.lock，threading.Lock和代码对象的支持。

几个内建类型如 "list" 和 "dict" 不直接支持弱引用，但可以通过子类化添加
支持:

   class Dict(dict):
       pass

   obj = Dict(red=1, green=2, blue=3)   # this object is weak referenceable

Other built-in types such as "tuple" and "int" do not support weak
references even when subclassed (This is an implementation detail and
may be different across various Python implementations.).

Extension types can easily be made to support weak references; see
Weak Reference Support.

class weakref.ref(object[, callback])

   返回对 *对象* 的弱引用。如果原始对象仍然存活，则可以通过调用引用对
   象来检索原始对象；如果引用的原始对象不再存在，则调用引用对象将得到
   "None" 。如果提供了 *回调* 而且值不是 "None" ，并且返回的弱引用对象
   仍然存活，则在对象即将终结时将调用回调；弱引用对象将作为回调的唯一参
   数传递；指示物将不再可用。

   It is allowable for many weak references to be constructed for the
   same object. Callbacks registered for each weak reference will be
   called from the most recently registered callback to the oldest
   registered callback.

   Exceptions raised by the callback will be noted on the standard
   error output, but cannot be propagated; they are handled in exactly
   the same way as exceptions raised from an object's "__del__()"
   method.

   Weak references are *hashable* if the *object* is hashable.  They
   will maintain their hash value even after the *object* was deleted.
   If "hash()" is called the first time only after the *object* was
   deleted, the call will raise "TypeError".

   Weak references support tests for equality, but not ordering.  If
   the referents are still alive, two references have the same
   equality relationship as their referents (regardless of the
   *callback*).  If either referent has been deleted, the references
   are equal only if the reference objects are the same object.

   This is a subclassable type rather than a factory function.

   __callback__

      This read-only attribute returns the callback currently
      associated to the weakref.  If there is no callback or if the
      referent of the weakref is no longer alive then this attribute
      will have value "None".

   在 3.4 版更改: Added the "__callback__" attribute.

weakref.proxy(object[, callback])

   Return a proxy to *object* which uses a weak reference.  This
   supports use of the proxy in most contexts instead of requiring the
   explicit dereferencing used with weak reference objects.  The
   returned object will have a type of either "ProxyType" or
   "CallableProxyType", depending on whether *object* is callable.
   Proxy objects are not *hashable* regardless of the referent; this
   avoids a number of problems related to their fundamentally mutable
   nature, and prevent their use as dictionary keys.  *callback* is
   the same as the parameter of the same name to the "ref()" function.

weakref.getweakrefcount(object)

   Return the number of weak references and proxies which refer to
   *object*.

weakref.getweakrefs(object)

   Return a list of all weak reference and proxy objects which refer
   to *object*.

class weakref.WeakKeyDictionary([dict])

   Mapping class that references keys weakly.  Entries in the
   dictionary will be discarded when there is no longer a strong
   reference to the key.  This can be used to associate additional
   data with an object owned by other parts of an application without
   adding attributes to those objects.  This can be especially useful
   with objects that override attribute accesses.

   注解: Caution: Because a "WeakKeyDictionary" is built on top of a
     Python dictionary, it must not change size when iterating over
     it.  This can be difficult to ensure for a "WeakKeyDictionary"
     because actions performed by the program during iteration may
     cause items in the dictionary to vanish "by magic" (as a side
     effect of garbage collection).

"WeakKeyDictionary" objects have an additional method that exposes the
internal references directly.  The references are not guaranteed to be
"live" at the time they are used, so the result of calling the
references needs to be checked before being used.  This can be used to
avoid creating references that will cause the garbage collector to
keep the keys around longer than needed.

WeakKeyDictionary.keyrefs()

   Return an iterable of the weak references to the keys.

class weakref.WeakValueDictionary([dict])

   Mapping class that references values weakly.  Entries in the
   dictionary will be discarded when no strong reference to the value
   exists any more.

   注解: Caution:  Because a "WeakValueDictionary" is built on top
     of a Python dictionary, it must not change size when iterating
     over it.  This can be difficult to ensure for a
     "WeakValueDictionary" because actions performed by the program
     during iteration may cause items in the dictionary to vanish "by
     magic" (as a side effect of garbage collection).

"WeakValueDictionary" objects have an additional method that has the
same issues as the "keyrefs()" method of "WeakKeyDictionary" objects.

WeakValueDictionary.valuerefs()

   Return an iterable of the weak references to the values.

class weakref.WeakSet([elements])

   Set class that keeps weak references to its elements.  An element
   will be discarded when no strong reference to it exists any more.

class weakref.WeakMethod(method)

   A custom "ref" subclass which simulates a weak reference to a bound
   method (i.e., a method defined on a class and looked up on an
   instance). Since a bound method is ephemeral, a standard weak
   reference cannot keep hold of it.  "WeakMethod" has special code to
   recreate the bound method until either the object or the original
   function dies:

      >>> class C:
      ...     def method(self):
      ...         print("method called!")
      ...
      >>> c = C()
      >>> r = weakref.ref(c.method)
      >>> r()
      >>> r = weakref.WeakMethod(c.method)
      >>> r()
      <bound method C.method of <__main__.C object at 0x7fc859830220>>
      >>> r()()
      method called!
      >>> del c
      >>> gc.collect()
      0
      >>> r()
      >>>

   3.4 新版功能.

class weakref.finalize(obj, func, *args, **kwargs)

   Return a callable finalizer object which will be called when *obj*
   is garbage collected. Unlike an ordinary weak reference, a
   finalizer will always survive until the reference object is
   collected, greatly simplifying lifecycle management.

   A finalizer is considered *alive* until it is called (either
   explicitly or at garbage collection), and after that it is *dead*.
   Calling a live finalizer returns the result of evaluating
   "func(*arg, **kwargs)", whereas calling a dead finalizer returns
   "None".

   Exceptions raised by finalizer callbacks during garbage collection
   will be shown on the standard error output, but cannot be
   propagated.  They are handled in the same way as exceptions raised
   from an object's "__del__()" method or a weak reference's callback.

   When the program exits, each remaining live finalizer is called
   unless its "atexit" attribute has been set to false.  They are
   called in reverse order of creation.

   A finalizer will never invoke its callback during the later part of
   the *interpreter shutdown* when module globals are liable to have
   been replaced by "None".

   __call__()

      If *self* is alive then mark it as dead and return the result of
      calling "func(*args, **kwargs)".  If *self* is dead then return
      "None".

   detach()

      If *self* is alive then mark it as dead and return the tuple
      "(obj, func, args, kwargs)".  If *self* is dead then return
      "None".

   peek()

      If *self* is alive then return the tuple "(obj, func, args,
      kwargs)".  If *self* is dead then return "None".

   alive

      Property which is true if the finalizer is alive, false
      otherwise.

   atexit

      A writable boolean property which by default is true.  When the
      program exits, it calls all remaining live finalizers for which
      "atexit" is true.  They are called in reverse order of creation.

   注解: It is important to ensure that *func*, *args* and *kwargs*
     do not own any references to *obj*, either directly or
     indirectly, since otherwise *obj* will never be garbage
     collected.  In particular, *func* should not be a bound method of
     *obj*.

   3.4 新版功能.

weakref.ReferenceType

   The type object for weak references objects.

weakref.ProxyType

   The type object for proxies of objects which are not callable.

weakref.CallableProxyType

   The type object for proxies of callable objects.

weakref.ProxyTypes

   Sequence containing all the type objects for proxies.  This can
   make it simpler to test if an object is a proxy without being
   dependent on naming both proxy types.

exception weakref.ReferenceError

   Exception raised when a proxy object is used but the underlying
   object has been collected.  This is the same as the standard
   "ReferenceError" exception.

参见:

  **PEP 205** - Weak References
     The proposal and rationale for this feature, including links to
     earlier implementations and information about similar features in
     other languages.


8.8.1. 弱引用对象
=================

Weak reference objects have no methods and no attributes besides
"ref.__callback__". A weak reference object allows the referent to be
obtained, if it still exists, by calling it:

>>> import weakref
>>> class Object:
...     pass
...
>>> o = Object()
>>> r = weakref.ref(o)
>>> o2 = r()
>>> o is o2
True

If the referent no longer exists, calling the reference object returns
"None":

>>> del o, o2
>>> print(r())
None

Testing that a weak reference object is still live should be done
using the expression "ref() is not None".  Normally, application code
that needs to use a reference object should follow this pattern:

   # r is a weak reference object
   o = r()
   if o is None:
       # referent has been garbage collected
       print("Object has been deallocated; can't frobnicate.")
   else:
       print("Object is still live!")
       o.do_something_useful()

Using a separate test for "liveness" creates race conditions in
threaded applications; another thread can cause a weak reference to
become invalidated before the weak reference is called; the idiom
shown above is safe in threaded applications as well as single-
threaded applications.

Specialized versions of "ref" objects can be created through
subclassing. This is used in the implementation of the
"WeakValueDictionary" to reduce the memory overhead for each entry in
the mapping.  This may be most useful to associate additional
information with a reference, but could also be used to insert
additional processing on calls to retrieve the referent.

This example shows how a subclass of "ref" can be used to store
additional information about an object and affect the value that's
returned when the referent is accessed:

   import weakref

   class ExtendedRef(weakref.ref):
       def __init__(self, ob, callback=None, **annotations):
           super(ExtendedRef, self).__init__(ob, callback)
           self.__counter = 0
           for k, v in annotations.items():
               setattr(self, k, v)

       def __call__(self):
           """Return a pair containing the referent and the number of
           times the reference has been called.
           """
           ob = super(ExtendedRef, self).__call__()
           if ob is not None:
               self.__counter += 1
               ob = (ob, self.__counter)
           return ob


8.8.2. 示例
===========

This simple example shows how an application can use object IDs to
retrieve objects that it has seen before.  The IDs of the objects can
then be used in other data structures without forcing the objects to
remain alive, but the objects can still be retrieved by ID if they do.

   import weakref

   _id2obj_dict = weakref.WeakValueDictionary()

   def remember(obj):
       oid = id(obj)
       _id2obj_dict[oid] = obj
       return oid

   def id2obj(oid):
       return _id2obj_dict[oid]


8.8.3. Finalizer Objects
========================

The main benefit of using "finalize" is that it makes it simple to
register a callback without needing to preserve the returned finalizer
object.  For instance

>>> import weakref
>>> class Object:
...     pass
...
>>> kenny = Object()
>>> weakref.finalize(kenny, print, "You killed Kenny!")  #doctest:+ELLIPSIS
<finalize object at ...; for 'Object' at ...>
>>> del kenny
You killed Kenny!

The finalizer can be called directly as well.  However the finalizer
will invoke the callback at most once.

>>> def callback(x, y, z):
...     print("CALLBACK")
...     return x + y + z
...
>>> obj = Object()
>>> f = weakref.finalize(obj, callback, 1, 2, z=3)
>>> assert f.alive
>>> assert f() == 6
CALLBACK
>>> assert not f.alive
>>> f()                     # callback not called because finalizer dead
>>> del obj                 # callback not called because finalizer dead

You can unregister a finalizer using its "detach()" method.  This
kills the finalizer and returns the arguments passed to the
constructor when it was created.

>>> obj = Object()
>>> f = weakref.finalize(obj, callback, 1, 2, z=3)
>>> f.detach()                                           #doctest:+ELLIPSIS
(<__main__.Object object ...>, <function callback ...>, (1, 2), {'z': 3})
>>> newobj, func, args, kwargs = _
>>> assert not f.alive
>>> assert newobj is obj
>>> assert func(*args, **kwargs) == 6
CALLBACK

Unless you set the "atexit" attribute to "False", a finalizer will be
called when the program exits if it is still alive.  For instance

>>> obj = Object()
>>> weakref.finalize(obj, print, "obj dead or exiting")  #doctest:+ELLIPSIS
<finalize object at ...; for 'Object' at ...>
>>> exit()                                               #doctest:+SKIP
obj dead or exiting


8.8.4. Comparing finalizers with "__del__()" methods
====================================================

Suppose we want to create a class whose instances represent temporary
directories.  The directories should be deleted with their contents
when the first of the following events occurs:

* the object is garbage collected,

* the object's "remove()" method is called, or

* the program exits.

We might try to implement the class using a "__del__()" method as
follows:

   class TempDir:
       def __init__(self):
           self.name = tempfile.mkdtemp()

       def remove(self):
           if self.name is not None:
               shutil.rmtree(self.name)
               self.name = None

       @property
       def removed(self):
           return self.name is None

       def __del__(self):
           self.remove()

Starting with Python 3.4, "__del__()" methods no longer prevent
reference cycles from being garbage collected, and module globals are
no longer forced to "None" during *interpreter shutdown*. So this code
should work without any issues on CPython.

However, handling of "__del__()" methods is notoriously implementation
specific, since it depends on internal details of the interpreter's
garbage collector implementation.

A more robust alternative can be to define a finalizer which only
references the specific functions and objects that it needs, rather
than having access to the full state of the object:

   class TempDir:
       def __init__(self):
           self.name = tempfile.mkdtemp()
           self._finalizer = weakref.finalize(self, shutil.rmtree, self.name)

       def remove(self):
           self._finalizer()

       @property
       def removed(self):
           return not self._finalizer.alive

Defined like this, our finalizer only receives a reference to the
details it needs to clean up the directory appropriately. If the
object never gets garbage collected the finalizer will still be called
at exit.

The other advantage of weakref based finalizers is that they can be
used to register finalizers for classes where the definition is
controlled by a third party, such as running code when a module is
unloaded:

   import weakref, sys
   def unloading_module():
       # implicit reference to the module globals from the function body
   weakref.finalize(sys.modules[__name__], unloading_module)

注解: If you create a finalizer object in a daemonic thread just as
  the program exits then there is the possibility that the finalizer
  does not get called at exit.  However, in a daemonic thread
  "atexit.register()", "try: ... finally: ..." and "with: ..." do not
  guarantee that cleanup occurs either.
