---
title: functools
toc: true
date: 2019-06-30
---
10.2. "functools" --- 高阶函数和可调用对象上的操作
**************************************************

**源代码:** Lib/functools.py

======================================================================

"functools" 模块应用于高阶函数，即——参数或（和）返回值为其他函数的函数
。通常来说，此模块的功能适用于所有可调用对象。

"functools" 模块定义了以下函数:

functools.cmp_to_key(func)

   将(旧式的)比较函数转换为新式的 *key function* .  在类似于
   "sorted()" ， "min()" ， "max()" ， "heapq.nlargest()" ，
   "heapq.nsmallest()" ， "itertools.groupby()" 等函数的 *key* 参数中
   使用。此函数主要用作将 Python 2 程序转换至新版的转换工具，以保持对
   比较函数的兼容。

   比较函数意为一个可调用对象，该对象接受两个参数并比较它们，结果为小
   于则返回一个负数，相等则返回零，大于则返回一个正数。key function则
   是一个接受一个参数，并返回另一个用以排序的值的可调用对象。

   示例:

      sorted(iterable, key=cmp_to_key(locale.strcoll))  # locale-aware sort order

   有关排序示例和简要排序教程，请参阅 排序指南 。

   3.2 新版功能.

@functools.lru_cache(maxsize=128, typed=False)

   一个为函数提供缓存功能的装饰器，缓存 *maxsize* 组传入参数，在下次以
   相同参数调用时直接返回上一次的结果。用以节约高开销或 I/O函数的调用时
   间。

   由于使用了字典存储缓存，所以该函数的固定参数和关键字参数必须是可哈
   希的。

   如果 *maxsize* 设置为 "None" ，LRU功能将被禁用且缓存数量无上限。
   *maxsize* 设置为 2 的幂时可获得最佳性能。

   如果 *typed* 设置为 true，不同类型的函数参数将被分别缓存。例如，
   "f(3)" 和 "f(3.0)" 将被视为不同而分别缓存。

   为了衡量缓存的有效性以便调整 *maxsize* 形参，被装饰的函数带有一个
   "cache_info()" 函数。当调用 "cache_info()" 函数时，返回一个具名元组
   ，包含命中次数 *hits*，未命中次数 *misses* ，最大缓存数量 *maxsize*
   和 当前缓存大小 *currsize*。在多线程环境中，命中数与未命中数是不完
   全准确的。

   该装饰器也提供了一个用于清理/使缓存失效的函数 "cache_clear()" 。

   原始的未经装饰的函数可以通过 "__wrapped__" 属性访问。它可以用于检查
   、绕过缓存，或使用不同的缓存再次装饰原始函数。

   “最久未使用算法”（LRU）缓存 在“最近的调用是即将到来的调用的最佳预测
   因子”时性能最好（比如，新闻服务器上最受欢迎的文章倾向于每天更改）。
   “缓存大小限制”参数保证缓存不会在长时间运行的进程比如说网站服务器上
   无限制的增加自身的大小。

   一般来说，LRU缓存只在当你想要重用之前计算的结果时使用。因此，用它缓
   存具有副作用的函数、需要在每次调用时创建不同、易变的对象的函数或者
   诸如 time（）或 random（）之类的不纯函数是没有意义的。

   静态 Web 内容的 LRU 缓存示例:

      @lru_cache(maxsize=32)
      def get_pep(num):
          'Retrieve text of a Python Enhancement Proposal'
          resource = 'http://www.Python.org/dev/peps/pep-%04d/' % num
          try:
              with urllib.request.urlopen(resource) as s:
                  return s.read()
          except urllib.error.HTTPError:
              return 'Not Found'

      >>> for n in 8, 290, 308, 320, 8, 218, 320, 279, 289, 320, 9991:
      ...     pep = get_pep(n)
      ...     print(n, len(pep))

      >>> get_pep.cache_info()
      CacheInfo(hits=3, misses=8, maxsize=32, currsize=8)

   以下是使用缓存通过 动态规划  计算 斐波那契数列  的例子。

      @lru_cache(maxsize=None)
      def fib(n):
          if n < 2:
              return n
          return fib(n-1) + fib(n-2)

      >>> [fib(n) for n in range(16)]
      [0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, 233, 377, 610]

      >>> fib.cache_info()
      CacheInfo(hits=28, misses=16, maxsize=None, currsize=16)

   3.2 新版功能.

   在 3.3 版更改: 添加 *typed* 选项。

@functools.total_ordering

   给定一个声明一个或多个全比较排序方法的类，这个类装饰器实现剩余的方
   法。这减轻了指定所有可能的全比较操作的工作。

   此类必须包含以下方法之一："__lt__()" 、"__le__()"、"__gt__()" 或
   "__ge__()"。另外，此类必须支持 "__eq__()" 方法。

   例如:

      @total_ordering
      class Student:
          def _is_valid_operand(self, other):
              return (hasattr(other, "lastname") and
                      hasattr(other, "firstname"))
          def __eq__(self, other):
              if not self._is_valid_operand(other):
                  return NotImplemented
              return ((self.lastname.lower(), self.firstname.lower()) ==
                      (other.lastname.lower(), other.firstname.lower()))
          def __lt__(self, other):
              if not self._is_valid_operand(other):
                  return NotImplemented
              return ((self.lastname.lower(), self.firstname.lower()) <
                      (other.lastname.lower(), other.firstname.lower()))

   注解: While this decorator makes it easy to create well behaved
     totally ordered types, it *does* come at the cost of slower
     execution and more complex stack traces for the derived
     comparison methods. If performance benchmarking indicates this is
     a bottleneck for a given application, implementing all six rich
     comparison methods instead is likely to provide an easy speed
     boost.

   3.2 新版功能.

   在 3.4 版更改: Returning NotImplemented from the underlying
   comparison function for unrecognised types is now supported.

functools.partial(func, *args, **keywords)

   Return a new partial object which when called will behave like
   *func* called with the positional arguments *args* and keyword
   arguments *keywords*. If more arguments are supplied to the call,
   they are appended to *args*. If additional keyword arguments are
   supplied, they extend and override *keywords*. Roughly equivalent
   to:

      def partial(func, *args, **keywords):
          def newfunc(*fargs, **fkeywords):
              newkeywords = keywords.copy()
              newkeywords.update(fkeywords)
              return func(*args, *fargs, **newkeywords)
          newfunc.func = func
          newfunc.args = args
          newfunc.keywords = keywords
          return newfunc

   The "partial()" is used for partial function application which
   "freezes" some portion of a function's arguments and/or keywords
   resulting in a new object with a simplified signature.  For
   example, "partial()" can be used to create a callable that behaves
   like the "int()" function where the *base* argument defaults to
   two:

   >>> from functools import partial
   >>> basetwo = partial(int, base=2)
   >>> basetwo.__doc__ = 'Convert base 2 string to an int.'
   >>> basetwo('10010')
   18

class functools.partialmethod(func, *args, **keywords)

   Return a new "partialmethod" descriptor which behaves like
   "partial" except that it is designed to be used as a method
   definition rather than being directly callable.

   *func* must be a *descriptor* or a callable (objects which are
   both, like normal functions, are handled as descriptors).

   When *func* is a descriptor (such as a normal Python function,
   "classmethod()", "staticmethod()", "abstractmethod()" or another
   instance of "partialmethod"), calls to "__get__" are delegated to
   the underlying descriptor, and an appropriate partial object
   returned as the result.

   When *func* is a non-descriptor callable, an appropriate bound
   method is created dynamically. This behaves like a normal Python
   function when used as a method: the *self* argument will be
   inserted as the first positional argument, even before the *args*
   and *keywords* supplied to the "partialmethod" constructor.

   示例:

      >>> class Cell(object):
      ...     def __init__(self):
      ...         self._alive = False
      ...     @property
      ...     def alive(self):
      ...         return self._alive
      ...     def set_state(self, state):
      ...         self._alive = bool(state)
      ...     set_alive = partialmethod(set_state, True)
      ...     set_dead = partialmethod(set_state, False)
      ...
      >>> c = Cell()
      >>> c.alive
      False
      >>> c.set_alive()
      >>> c.alive
      True

   3.4 新版功能.

functools.reduce(function, iterable[, initializer])

   Apply *function* of two arguments cumulatively to the items of
   *sequence*, from left to right, so as to reduce the sequence to a
   single value.  For example, "reduce(lambda x, y: x+y, [1, 2, 3, 4,
   5])" calculates "((((1+2)+3)+4)+5)". The left argument, *x*, is the
   accumulated value and the right argument, *y*, is the update value
   from the *sequence*.  If the optional *initializer* is present, it
   is placed before the items of the sequence in the calculation, and
   serves as a default when the sequence is empty.  If *initializer*
   is not given and *sequence* contains only one item, the first item
   is returned.

   大致相当于：

      def reduce(function, iterable, initializer=None):
          it = iter(iterable)
          if initializer is None:
              value = next(it)
          else:
              value = initializer
          for element in it:
              value = function(value, element)
          return value

@functools.singledispatch

   Transform a function into a *single-dispatch* *generic function*.

   To define a generic function, decorate it with the
   "@singledispatch" decorator. Note that the dispatch happens on the
   type of the first argument, create your function accordingly:

      >>> from functools import singledispatch
      >>> @singledispatch
      ... def fun(arg, verbose=False):
      ...     if verbose:
      ...         print("Let me just say,", end=" ")
      ...     print(arg)

   To add overloaded implementations to the function, use the
   "register()" attribute of the generic function.  It is a decorator,
   taking a type parameter and decorating a function implementing the
   operation for that type:

      >>> @fun.register(int)
      ... def _(arg, verbose=False):
      ...     if verbose:
      ...         print("Strength in numbers, eh?", end=" ")
      ...     print(arg)
      ...
      >>> @fun.register(list)
      ... def _(arg, verbose=False):
      ...     if verbose:
      ...         print("Enumerate this:")
      ...     for i, elem in enumerate(arg):
      ...         print(i, elem)

   To enable registering lambdas and pre-existing functions, the
   "register()" attribute can be used in a functional form:

      >>> def nothing(arg, verbose=False):
      ...     print("Nothing.")
      ...
      >>> fun.register(type(None), nothing)

   The "register()" attribute returns the undecorated function which
   enables decorator stacking, pickling, as well as creating unit
   tests for each variant independently:

      >>> @fun.register(float)
      ... @fun.register(Decimal)
      ... def fun_num(arg, verbose=False):
      ...     if verbose:
      ...         print("Half of your number:", end=" ")
      ...     print(arg / 2)
      ...
      >>> fun_num is fun
      False

   When called, the generic function dispatches on the type of the
   first argument:

      >>> fun("Hello, world.")
      Hello, world.
      >>> fun("test.", verbose=True)
      Let me just say, test.
      >>> fun(42, verbose=True)
      Strength in numbers, eh? 42
      >>> fun(['spam', 'spam', 'eggs', 'spam'], verbose=True)
      Enumerate this:
      0 spam
      1 spam
      2 eggs
      3 spam
      >>> fun(None)
      Nothing.
      >>> fun(1.23)
      0.615

   Where there is no registered implementation for a specific type,
   its method resolution order is used to find a more generic
   implementation. The original function decorated with
   "@singledispatch" is registered for the base "object" type, which
   means it is used if no better implementation is found.

   To check which implementation will the generic function choose for
   a given type, use the "dispatch()" attribute:

      >>> fun.dispatch(float)
      <function fun_num at 0x1035a2840>
      >>> fun.dispatch(dict)    # note: default implementation
      <function fun at 0x103fe0000>

   To access all registered implementations, use the read-only
   "registry" attribute:

      >>> fun.registry.keys()
      dict_keys([<class 'NoneType'>, <class 'int'>, <class 'object'>,
                <class 'decimal.Decimal'>, <class 'list'>,
                <class 'float'>])
      >>> fun.registry[float]
      <function fun_num at 0x1035a2840>
      >>> fun.registry[object]
      <function fun at 0x103fe0000>

   3.4 新版功能.

functools.update_wrapper(wrapper, wrapped, assigned=WRAPPER_ASSIGNMENTS, updated=WRAPPER_UPDATES)

   Update a *wrapper* function to look like the *wrapped* function.
   The optional arguments are tuples to specify which attributes of
   the original function are assigned directly to the matching
   attributes on the wrapper function and which attributes of the
   wrapper function are updated with the corresponding attributes from
   the original function. The default values for these arguments are
   the module level constants "WRAPPER_ASSIGNMENTS" (which assigns to
   the wrapper function's "__module__", "__name__", "__qualname__",
   "__annotations__" and "__doc__", the documentation string) and
   "WRAPPER_UPDATES" (which updates the wrapper function's "__dict__",
   i.e. the instance dictionary).

   To allow access to the original function for introspection and
   other purposes (e.g. bypassing a caching decorator such as
   "lru_cache()"), this function automatically adds a "__wrapped__"
   attribute to the wrapper that refers to the function being wrapped.

   The main intended use for this function is in *decorator* functions
   which wrap the decorated function and return the wrapper. If the
   wrapper function is not updated, the metadata of the returned
   function will reflect the wrapper definition rather than the
   original function definition, which is typically less than helpful.

   "update_wrapper()" may be used with callables other than functions.
   Any attributes named in *assigned* or *updated* that are missing
   from the object being wrapped are ignored (i.e. this function will
   not attempt to set them on the wrapper function). "AttributeError"
   is still raised if the wrapper function itself is missing any
   attributes named in *updated*.

   3.2 新版功能: Automatic addition of the "__wrapped__" attribute.

   3.2 新版功能: Copying of the "__annotations__" attribute by
   default.

   在 3.2 版更改: Missing attributes no longer trigger an
   "AttributeError".

   在 3.4 版更改: The "__wrapped__" attribute now always refers to the
   wrapped function, even if that function defined a "__wrapped__"
   attribute. (see bpo-17482)

@functools.wraps(wrapped, assigned=WRAPPER_ASSIGNMENTS, updated=WRAPPER_UPDATES)

   This is a convenience function for invoking "update_wrapper()" as a
   function decorator when defining a wrapper function.  It is
   equivalent to "partial(update_wrapper, wrapped=wrapped,
   assigned=assigned, updated=updated)". For example:

      >>> from functools import wraps
      >>> def my_decorator(f):
      ...     @wraps(f)
      ...     def wrapper(*args, **kwds):
      ...         print('Calling decorated function')
      ...         return f(*args, **kwds)
      ...     return wrapper
      ...
      >>> @my_decorator
      ... def example():
      ...     """Docstring"""
      ...     print('Called example function')
      ...
      >>> example()
      Calling decorated function
      Called example function
      >>> example.__name__
      'example'
      >>> example.__doc__
      'Docstring'

   Without the use of this decorator factory, the name of the example
   function would have been "'wrapper'", and the docstring of the
   original "example()" would have been lost.


10.2.1. "partial" Objects
=========================

"partial" objects are callable objects created by "partial()". They
have three read-only attributes:

partial.func

   A callable object or function.  Calls to the "partial" object will
   be forwarded to "func" with new arguments and keywords.

partial.args

   The leftmost positional arguments that will be prepended to the
   positional arguments provided to a "partial" object call.

partial.keywords

   The keyword arguments that will be supplied when the "partial"
   object is called.

"partial" objects are like "function" objects in that they are
callable, weak referencable, and can have attributes.  There are some
important differences.  For instance, the "__name__" and "__doc__"
attributes are not created automatically.  Also, "partial" objects
defined in classes behave like static methods and do not transform
into bound methods during instance attribute look-up.
