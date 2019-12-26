---
title: extending
toc: true
date: 2019-06-30
---
1. 使用 C 或 C++ 扩展 Python
****************************

如果你会用 C，添加新的 Python 内置模块会很简单。以下两件不能用 Python
直接做的事，可以通过 *extension modules* 来实现：实现新的内置对象类型
；调用 C 的库函数和系统调用。

为了支持扩展，Python API（应用程序编程接口）定义了一系列函数、宏和变量
，可以访问 Python 运行时系统的大部分内容。Python 的 API 可以通过在一个
C 源文件中引用 ""Python.h"" 头文件来使用。

扩展模块的编写方式取决与你的目的以及系统设置；下面章节会详细介绍。

注解: The C extension interface is specific to CPython, and
  extension modules do not work on other Python implementations.  In
  many cases, it is possible to avoid writing C extensions and
  preserve portability to other implementations. For example, if your
  use case is calling C library functions or system calls, you should
  consider using the "ctypes" module or the cffi library rather than
  writing custom C code. These modules let you write Python code to
  interface with C code and are more portable between implementations
  of Python than writing and compiling a C extension module.


1.1. 一个简单的例子
===================

让我们创建一个扩展模块 "spam" (Monty Python 粉丝最喜欢的食物...) 并且
想要创建对应 C 库函数 "system()" [1] 的 Python 接口。 这个函数接受一个
以 null 结尾的字符串参数并返回一个整数。 我们希望可以在 Python 中以如
下方式调用此函数:

   >>> import spam
   >>> status = spam.system("ls -l")

首先创建一个 "spammodule.c" 文件。（传统上，如果一个模块叫 "spam"，则
对应实现它的 C 文件叫 "spammodule.c"；如果这个模块名字非常长，比如
"spammify"，则这个模块的文件可以直接叫 "spammify.c"。）

The first line of our file can be:

   #include <Python.h>

这会导入 Python API（如果你喜欢，你可以在这里添加描述模块目标和版权信
息的注释)。

注解: 由于 Python 可能会定义一些影响某些系统上标准头文件的预处理器定义
  ，因此 在包含任何标准头文件之前，您*必须* include 这个文件：
  *Python.h*。

所有用户可见的符号都定义自 "Python.h" 中，并拥有前缀 "Py" 或 "PY" ，除
了那些已经定义在标准头文件的。 为了方便，以及由于其在 Python 解释器中
广泛应用，""Python.h"" 也包含了少量标准头文件: "<stdio.h>"，
"<string.h>"，"<errno.h>" 和 "<stdlib.h>"。 如果后面的头文件在你的系统
上不存在，还会直接声明函数 "malloc()"，"free()" 和 "realloc()" 。

下面要做的事是将 C 函数添加到我们的扩展模块，当 Python 表达式
"spam.system(string)" 被求值时函数将被调用（我们很快就会看到它最终是如
何被调用的）:

   static PyObject *
   spam_system(PyObject *self, PyObject *args)
   {
       const char *command;
       int sts;

       if (!PyArg_ParseTuple(args, "s", &command))
           return NULL;
       sts = system(command);
       return PyLong_FromLong(sts);
   }

有个直接翻译参数列表的方法(例如单独的 "“ls-l"")到要传递给 C 函数的参数。
C函数总是有两个参数，通常名字是 *self* 和 *args* 。

对模块级函数， *self* 参数指向模块对象；对于对象实例则指向方法。

*args* 参数是指向一个 Python 的 tuple 对象的指针，其中包含参数。 每个
tuple 项对应一个调用参数。 这些参数也全都是 Python 对象 --- 要在我们的
C 函数中使用它们就需要先将其转换为 C 值。 Python API 中的函数
"PyArg_ParseTuple()" 会检查参数类型并将其转换为 C 值。 它使用模板字符
串确定需要的参数类型以及存储被转换的值的 C 变量类型。 细节将稍后说明。

"PyArg_ParseTuple()" 正常返回非零，并已经按照提供的地址存入了各个变量
值。如果出错(零)则应该让函数返回 NULL 以通知解释器出错(有如例子中看到的)
。


1.2. 关于错误和异常
===================

一个 Python 解释器的常见惯例是，函数发生错误时，应该设置一个异常环境并返
回错误值(通常是 *NULL* 指针)。异常存储在解释器静态全局变量中，如果为
*NULL* ，则没有发生异常。异常的第一个参数也需要保存在静态全局变量中，
也就是 raise 的第二个参数(第二个参数到 "raise")。第三个变量包含栈回溯信
息。这三个变量等同于 Python 变量 "sys.exc_info()" (查看 Python 库参考的模
块 "sys" 的章节)。这对于理解到底发生了什么错误是很重要的。

Python API中定义了一些函数来设置这些变量。

最常用的就是 "PyErr_SetString()"。 其参数是异常对象和 C 字符串。 异常
对象一般是像 "PyExc_ZeroDivisionError" 这样的预定义对象。 C 字符串指明
异常原因，并被转换为一个 Python 字符串对象存储为异常的“关联值”。

另一个有用的函数是 "PyErr_SetFromErrno()" ，仅接受一个异常对象，异常描
述包含在全局变量 "errno" 中。最通用的函数还是 "PyErr_SetObject()" ，包
含两个参数，分别为异常对象和异常描述。你不需要使用 "Py_INCREF()" 来增
加传递到其他函数的参数对象的引用计数。

你可以通过 "PyErr_Occurred()" 获知当前异常，返回当前异常对象，如果确实
没有则为 *NULL* 。一般来说，你在调用函数时不需要调用
"PyErr_Occurred()" 检查是否发生了异常，你可以直接检查返回值。

当函数 *f* 调用另一个函数 *g* 时检测到后者出错了，*f* 自身将返回一个错
误值 (通常为 *NULL* 或 "-1")。 它 *不应* 调用某个 "PyErr_*()" 函数 ---
这种函数已经由 *g* 调用过了。 然后 *f* 的调用者也应该返回一个错误提示
*它的* 调用者，同样 *不应* 调用 "PyErr_*()" ，依此类推 --- 错误的最详
细原因已经由首先检测到它的函数报告了。 一旦这个错误到达了 Python 解释
器的主循环，它将中断当前执行的 Python 代码并尝试找到由 Python 程序员所
指定的异常处理。

（在某些情况下，当模块确实能够通过调用其它 "PyErr_*()" 函数给出更加详
细的错误消息，并且在这些情况是可以这样做的。 但是按照一般规则，这是不
必要的，并可能导致有关错误原因的信息丢失：大多数操作会由于种种原因而失
败。）

想要忽略由一个失败的函数调用所设置的异常，异常条件必须通过调用
"PyErr_Clear()" 显式地被清除。 C 代码应当调用 "PyErr_Clear()" 的唯一情
况是如果它不想将错误传给解释器而是想完全由自己来处理它（可能是尝试其他
方法，或是假装没有出错）。

每次失败的 "malloc()" 调用必须转换为一个异常。 "malloc()" (或
"realloc()" )的直接调用者必须调用 "PyErr_NoMemory()" 来返回错误来提示
。所有对象创建函数(例如 "PyLong_FromLong()" )已经这么做了，所以这个提
示仅用于直接调用 "malloc()" 的情况。

还要注意的是，除了 "PyArg_ParseTuple()" 等重要的例外，返回整数状态码的
函数通常都是返回正值或零来表示成功，而以 "-1" 表示失败，如同 Unix 系统
调用一样。

最后，当你返回一个错误指示器时要注意清理垃圾（通过为你已经创建的对象执
行 "Py_XDECREF()" 或 "Py_DECREF()" 调用）！

选择引发哪个异常完全取决于你的喜好。 所有内置的 Python 异常都有对应的
预声明 C 对象，例如 "PyExc_ZeroDivisionError"，你可以直接使用它们。 当
然，你应当明智地选择异常 --- 不要使用 "PyExc_TypeError" 来表示一个文件
无法被打开 (那大概应该用 "PyExc_IOError")。 如果参数列表有问题，
"PyArg_ParseTuple()" 函数通常会引发 "PyExc_TypeError"。 如果你想要一个
参数的值必须处于特定范围之内或必须满足其他条件，则适宜使用
"PyExc_ValueError"。

你也可以为你的模块定义一个唯一的新异常。需要在文件前部声明一个静态对象
变量，如:

   static PyObject *SpamError;

以及初始化你的模块的初始化函数 ("PyInit_spam()") 包含一个异常对象(先不
管错误检查)：

   PyMODINIT_FUNC
   PyInit_spam(void)
   {
       PyObject *m;

       m = PyModule_Create(&spammodule);
       if (m == NULL)
           return NULL;

       SpamError = PyErr_NewException("spam.error", NULL, NULL);
       Py_INCREF(SpamError);
       PyModule_AddObject(m, "error", SpamError);
       return m;
   }

注意实际的 Python 异常名字是 "spam.error" 。 "PyErr_NewException()" 函数
使用 "Exception" 为基类创建一个类(除非是使用另外一个类替代 *NULL* )。
描述参考 内置异常 。

同样注意的是创建类保存了 "SpamError" 的一个引用，这是有意的。为了防止
被垃圾回收掉，否则 "SpamError" 随时会成为野指针。

一会讨论 "PyMODINIT_FUNC" 作为函数返回类型的用法。

"spam.error" 异常可以在扩展模块中抛出，通过 "PyErr_SetString()" 函数调
用，如下：

   static PyObject *
   spam_system(PyObject *self, PyObject *args)
   {
       const char *command;
       int sts;

       if (!PyArg_ParseTuple(args, "s", &command))
           return NULL;
       sts = system(command);
       if (sts < 0) {
           PyErr_SetString(SpamError, "System command failed");
           return NULL;
       }
       return PyLong_FromLong(sts);
   }


1.3. 回到例子
=============

回到前面的例子，你应该明白下面的代码:

   if (!PyArg_ParseTuple(args, "s", &command))
       return NULL;

如果在参数列表中检测到错误，将会返回 *NULL* (返回对象指针的函数的错误
指示器) ， 依据 "PyArg_ParseTuple()" 所设置的异常。 在其他情况下参数的
字符串值会被拷贝到局部变量 "command"。 这是一个指针赋值，你不应该修改
它所指向的字符串 (所以在标准 C 中，变量 "command" 应当被正确地声明为
"const char *command")。

下一个语句使用 UNIX 系统函数 "system()" ，传递给他的参数是刚才从
"PyArg_ParseTuple()" 取出的:

   sts = system(command);

我们的 "spam.system()" 函数必须返回 "sts" 的值作为 Python 对象。这通过使
用函数 "PyLong_FromLong()" 来实现。

   return PyLong_FromLong(sts);

在这种情况下，会返回一个整数对象，(这个对象会在 Python 堆里面管理)。

如果你的 C 函数没有有用的返回值(返回 "void" 的函数)，则必须返回 "None"
。(你可以用  "Py_RETUN_NONE" 宏来完成):

   Py_INCREF(Py_None);
   return Py_None;

"Py_None" 是一个 C 名字指定 Python 对象 "None" 。这是一个真正的 PY 对象，而
不是 *NULL* 指针。


1.4. 模块方法表和初始化函数
===========================

为了展示 "spam_system()" 如何被 Python 程序调用。把函数声明为可以被
Python调用，需要先定义一个方法表 "method table" 。

   static PyMethodDef SpamMethods[] = {
       ...
       {"system",  spam_system, METH_VARARGS,
        "Execute a shell command."},
       ...
       {NULL, NULL, 0, NULL}        /* Sentinel */
   };

注意第三个参数 ( "METH_VARARGS" ) ，这个标志指定会使用 C 的调用惯例。可
选值有  "METH_VARARGS" 、 "METH_VARARGS | METH_KEYWORDS" 。值 "0" 代表
使用 "PyArg_ParseTuple()" 的陈旧变量。

如果单独使用 "METH_VARARGS" ，函数会等待 Python 传来 tuple 格式的参数，并
最终使用  "PyArg_ParseTuple()" 进行解析。

"METH_KEYWORDS" 值表示接受关键字参数。这种情况下 C 函数需要接受第三个
"PyObject *"  对象，表示字典参数，使用 "PyArg_ParseTupleAndKeywords()"
来解析出参数。

这个方法表必须被模块定义结构所引用。

   static struct PyModuleDef spammodule = {
       PyModuleDef_HEAD_INIT,
       "spam",   /* name of module */
       spam_doc, /* module documentation, may be NULL */
       -1,       /* size of per-interpreter state of the module,
                    or -1 if the module keeps state in global variables. */
       SpamMethods
   };

这个结构体必须传递给解释器的模块初始化函数。初始化函数必须命名为
"PyInit_name()" ，其中 *name* 是模块的名字，并应该定义为非 "static" ，
且在模块文件里：

   PyMODINIT_FUNC
   PyInit_spam(void)
   {
       return PyModule_Create(&spammodule);
   }

注意 PyMODINIT_FUNC 声明了函数作为 "PyObject *" 返回类型，声明任何平台
的链接生命，以及给 C++生命函数的 "extern "C"" 。

当 Python 程序首次导入模块 "spam" 时， "PyInit_spam()" 被调用。(查看后续
注释了解嵌入 Python)。他会调用 "PyModule_Create()" ，会返回模块对象，并
插入内置函数对象到新创建的模块里，基于表(一个 "PyMethodDef" 结构体的数
组类型)到模块定义。 "PyModule_Create()" 返回一个指向刚创建模块的指针。
也可能因为严重错误而中止，或返回 *NULL* 在模块无法初始化成功时。初始化
函数必须返回模块对象给调用者，所以之后会被插入 "sys.modules" 。

当嵌入 Python 时， "PyInit_spam()" 函数不会被自动调用，除非放在
"PyImport_Inittab" 表里。要添加模块到初始化表，使用
"PyImport_AppendInittab()" ，可选的跟着一个模块的导入。

   int
   main(int argc, char *argv[])
   {
       wchar_t *program = Py_DecodeLocale(argv[0], NULL);
       if (program == NULL) {
           fprintf(stderr, "Fatal error: cannot decode argv[0]\n");
           exit(1);
       }

       /* Add a built-in module, before Py_Initialize */
       PyImport_AppendInittab("spam", PyInit_spam);

       /* Pass argv[0] to the Python interpreter */
       Py_SetProgramName(program);

       /* Initialize the Python interpreter.  Required. */
       Py_Initialize();

       /* Optionally import the module; alternatively,
          import can be deferred until the embedded script
          imports it. */
       PyImport_ImportModule("spam");

       ...

       PyMem_RawFree(program);
       return 0;
   }

注解: 要从 "sys.modules" 删除实体或导入已编译模块到一个进程里的多个
  解释器( 或使用 "fork()" 而没用 "exec()" )会在一些扩展模块上产生错误
  。扩展模 块作者可以在初始化内部数据结构时给出警告。

更多关于模块的现实的例子包含在 Python 源码包的 "Modules/xxmodule.c" 中。
这些文件可以用作你的代码模板，或者学习。脚本 modulator.py 包含在源码发
行版或 Windows 安装中，提供了一个简单的 GUI，用来声明需要实现的函数和对象
，并且可以生成供填入的模板。脚本在 Tools/modulator/ 目录。查看 README 以
了解用法。

注解: 不像我们的 "spam" 例子， "xxmodule" 使用了 *多阶段初始化*
  (Python3.5 开始引入)， "PyInit_spam" 会返回一个 PyModuleDef 结构体，
  然后创建的 模块放到导入机制。细节参考 **PEP 489** 的多阶段初始化。


1.5. 编译和链接
===============

在你能使用你的新写的扩展之前，你还需要做两件事情：使用 Python 系统来编
译和链接。如果你使用动态加载，这取决于你使用的操作系统的动态加载机制；
更多信息请参考编译扩展模块的章节（ 构建 C/C++扩展 章节），以及在
Windows 上编译需要的额外信息（ 在 Windows 平台编译 C 和 C++扩展 章节）。

如果你不使用动态加载，或者想要让模块永久性的作为 Python 解释器的一部分，
就必须修改配置设置，并重新构建解释器。幸运的是在 Unix 上很简单，只需要把
你的文件 ( "spammodule.c" 为例) 放在解压缩源码发行包的 "Modules/" 目录
下，添加一行到 "Modules/Setup.local" 来描述你的文件：

   spam spammodule.o

然后在顶层目录运行 **make** 来重新构建解释器。你也可以在 "Modules/" 子
目录使用 **make**，但是你必须先重建 "Makefile" 文件，然后运行
'**make** Makefile' 命令。（你每次修改 "Setup" 文件都需要这样操作。）

如果你的模块需要额外的链接，这些内容可以列出在配置文件里，举个实例：

   spam spammodule.o -lX11


1.6. 在 C 中调用 Python 函数
========================

迄今为止，我们一直把注意力集中于让 Python 调用 C 函数，其实反过来也很有用
，就是用 C 调用 Python 函数。这在回调函数中尤其有用。如果一个 C 接口使用回调
，那么就要实现这个回调机制。

幸运的是，Python解释器是比较方便回调的，并给标准 Python 函数提供了标准接
口。(这里就不再详述解析 Python 代码作为输入的方式，如果有兴趣可以参考
"Python/Pythonmain.c" 中的 "-c" 命令代码。)

调用 Python 函数，首先 Python 程序要传递 Python 函数对象。应该提供个函数(或
其他接口)来实现。当调用这个函数时，用全局变量保存 Python 函数对象的指针
，还要调用 ("Py_INCREF()") 来增加引用计数，当然不用全局变量也没什么关
系。例如如下:

   static PyObject *my_callback = NULL;

   static PyObject *
   my_set_callback(PyObject *dummy, PyObject *args)
   {
       PyObject *result = NULL;
       PyObject *temp;

       if (PyArg_ParseTuple(args, "O:set_callback", &temp)) {
           if (!PyCallable_Check(temp)) {
               PyErr_SetString(PyExc_TypeError, "parameter must be callable");
               return NULL;
           }
           Py_XINCREF(temp);         /* Add a reference to new callback */
           Py_XDECREF(my_callback);  /* Dispose of previous callback */
           my_callback = temp;       /* Remember new callback */
           /* Boilerplate to return "None" */
           Py_INCREF(Py_None);
           result = Py_None;
       }
       return result;
   }

这个函数必须使用 "METH_VARARGS" 标志注册到解释器，这在 模块方法表和初
始化函数 章节会描述。 "PyArg_ParseTuple()" 函数及其参数的文档在 提取扩
展函数的参数 。

"Py_XINCREF()" 和 "Py_XDECREF()" 这两个宏可以用来增加或减少对象的引用
计数，即使参数是 *NULL* 指针，操作也是安全的（但在这个例子中 *temp* 永
远不会为 *NULL*）。更多内容请参考 引用计数 段落。

"PyEval_CallObject()" 返回一个 Python 对象指针表示返回值。该函数有 2 个参
数，都是指向 Python 对象的指针：Python函数，和参数列表。参数列表必须是
tuple对象，其长度是参数数量。要调用无参数的 Python 函数，可以传递 NULL 或
空元组。要用唯一参数调用，传递单一元组。 "Py_BuildValue()" 返回元组，
当其格式为字符串或多个编码时，例如：

   int arg;
   PyObject *arglist;
   PyObject *result;
   ...
   arg = 123;
   ...
   /* Time to call the callback */
   arglist = Py_BuildValue("(i)", arg);
   result = PyObject_CallObject(my_callback, arglist);
   Py_DECREF(arglist);

"PyObject_CallObject()" 返回 Python 对象指针，这也是 Python 函数的返回值。
"PyObject_CallObject()" 是一个对其参数 "引用计数无关" 的函数。例子中新
的元组创建用于参数列表，并且在  "PyObject_CallObject()" 之后立即使用了
"Py_DECREF()" 。

"PyEval_CallObject()" 的返回值总是“新”的：要么是一个新建的对象；要么是
已有对象，但增加了引用计数。所以除非你想把结果保存在全局变量中，你需要
对这个值使用 "Py_DECREF()"，即使你对里面的内容（特别！）不感兴趣。

在你这么做之前，需要先检查返回值是否是 *NULL* 。如果是，Python函数会终
止并抛出异常。如果 C 代码调用了从 Python 传入的函数
"PyObject_CallObject()" ，因该立即返回错误来告知 Python 调用者，然后解释
器会打印栈回溯，或者调用 Python 代码来处理这个异常。如果无法处理，异常会
被 "PyErr_Clear()" 清除，例如：

   if (result == NULL)
       return NULL; /* Pass error back */
   ...use result...
   Py_DECREF(result);

依赖于具体的回调函数，你还要提供一个参数列表到 "PyEval_CallObject()"
。在某些情况下参数列表是由 Python 程序提供的，通过接口再传到回调函数。这
样就可以不改变形式直接传递。另外一些时候你要构造一个新的 tuple 来传递参
数。最简单的方法就是 "Py_BuildValue()" 函数构造 tuple。例如，你要传递一
个事件对象时可以用:

   PyObject *arglist;
   ...
   arglist = Py_BuildValue("(l)", eventcode);
   result = PyObject_CallObject(my_callback, arglist);
   Py_DECREF(arglist);
   if (result == NULL)
       return NULL; /* Pass error back */
   /* Here maybe use the result */
   Py_DECREF(result);

注意 "Py_DECREF(arglist)" 所在处会立即调用，在错误检查之前。当然还要注
意一些常规的错误，比如 "Py_BuildValue()" 可能会遭遇内存不足等等。

你还需要注意，用关键字参数调用 "PyObject_Call()" ，需要支持普通参数和
关键字参数。有如如上例子中，我们使用 "Py_BuildValue()" 来构造字典。

   PyObject *dict;
   ...
   dict = Py_BuildValue("{s:i}", "name", val);
   result = PyObject_Call(my_callback, NULL, dict);
   Py_DECREF(dict);
   if (result == NULL)
       return NULL; /* Pass error back */
   /* Here maybe use the result */
   Py_DECREF(result);


1.7. 提取扩展函数的参数
=======================

函数 "PyArg_ParseTuple()" 的声明如下：

   int PyArg_ParseTuple(PyObject *arg, const char *format, ...);

参数 *arg* 必须是一个元组对象，包含从 Python 传递给 C 函数的参数列表。
*format* 参数必须是一个格式字符串，语法请参考 Python C/API 手册中的 语
句解释及变量编译。剩余参数是各个变量的地址，类型要与格式字符串对应。

注意 "PyArg_ParseTuple()" 会检测他需要的 Python 参数类型，却无法检测传递
给他的 C 变量地址，如果这里出错了，可能会在内存中随机写入东西，小心。

注意任何由调用者提供的 Python 对象引用是 *借来的* 引用；不要递减它们的引
用计数！

一些调用的例子：

   #define PY_SSIZE_T_CLEAN  /* Make "s#" use Py_ssize_t rather than int. */
   #include <Python.h>

   int ok;
   int i, j;
   long k, l;
   const char *s;
   Py_ssize_t size;

   ok = PyArg_ParseTuple(args, ""); /* No arguments */
       /* Python call: f() */

   ok = PyArg_ParseTuple(args, "s", &s); /* A string */
       /* Possible Python call: f('whoops!') */

   ok = PyArg_ParseTuple(args, "lls", &k, &l, &s); /* Two longs and a string */
       /* Possible Python call: f(1, 2, 'three') */

   ok = PyArg_ParseTuple(args, "(ii)s#", &i, &j, &s, &size);
       /* A pair of ints and a string, whose size is also returned */
       /* Possible Python call: f((1, 2), 'three') */

   {
       const char *file;
       const char *mode = "r";
       int bufsize = 0;
       ok = PyArg_ParseTuple(args, "s|si", &file, &mode, &bufsize);
       /* A string, and optionally another string and an integer */
       /* Possible Python calls:
          f('spam')
          f('spam', 'w')
          f('spam', 'wb', 100000) */
   }

   {
       int left, top, right, bottom, h, v;
       ok = PyArg_ParseTuple(args, "((ii)(ii))(ii)",
                &left, &top, &right, &bottom, &h, &v);
       /* A rectangle and a point */
       /* Possible Python call:
          f(((0, 0), (400, 300)), (10, 10)) */
   }

   {
       Py_complex c;
       ok = PyArg_ParseTuple(args, "D:myfunction", &c);
       /* a complex, also providing a function name for errors */
       /* Possible Python call: myfunction(1+2j) */
   }


1.8. 给扩展函数的关键字参数
===========================

函数 "PyArg_ParseTupleAndKeywords()" 声明如下：

   int PyArg_ParseTupleAndKeywords(PyObject *arg, PyObject *kwdict,
                                   const char *format, char *kwlist[], ...);

参数 *arg* 和 *format* 定义同 "PyArg_ParseTuple()" 。参数 *kwdict* 是
关键字字典，用于接受运行时传来的关键字参数。参数 *kwlist* 是一个
*NULL* 结尾的字符串，定义了可以接受的参数名，并从左到右与 *format* 中
各个变量对应。如果执行成功 "PyArg_ParseTupleAndKeywords()" 会返回 true
，否则返回 false 并抛出异常。

注解: 嵌套的元组在使用关键字参数时无法生效，不在 *kwlist* 中的关键字
  参数会 导致 "TypeError" 异常。

如下是使用关键字参数的例子模块，作者是 Geoff Philbrick
(phibrick@hks.com):

   #include "Python.h"

   static PyObject *
   keywdarg_parrot(PyObject *self, PyObject *args, PyObject *keywds)
   {
       int voltage;
       char *state = "a stiff";
       char *action = "voom";
       char *type = "Norwegian Blue";

       static char *kwlist[] = {"voltage", "state", "action", "type", NULL};

       if (!PyArg_ParseTupleAndKeywords(args, keywds, "i|sss", kwlist,
                                        &voltage, &state, &action, &type))
           return NULL;

       printf("-- This parrot wouldn't %s if you put %i Volts through it.\n",
              action, voltage);
       printf("-- Lovely plumage, the %s -- It's %s!\n", type, state);

       Py_RETURN_NONE;
   }

   static PyMethodDef keywdarg_methods[] = {
       /* The cast of the function is necessary since PyCFunction values
        * only take two PyObject* parameters, and keywdarg_parrot() takes
        * three.
        */
       {"parrot", (PyCFunction)keywdarg_parrot, METH_VARARGS | METH_KEYWORDS,
        "Print a lovely skit to standard output."},
       {NULL, NULL, 0, NULL}   /* sentinel */
   };

   static struct PyModuleDef keywdargmodule = {
       PyModuleDef_HEAD_INIT,
       "keywdarg",
       NULL,
       -1,
       keywdarg_methods
   };

   PyMODINIT_FUNC
   PyInit_keywdarg(void)
   {
       return PyModule_Create(&keywdargmodule);
   }


1.9. 构造任意值
===============

这个函数与 "PyArg_ParseTuple()" 很相似，声明如下：

   PyObject *Py_BuildValue(const char *format, ...);

接受一个格式字符串，与 "PyArg_ParseTuple()" 相同，但是参数必须是原变量
的地址指针(输入给函数，而非输出)。最终返回一个 Python 对象适合于返回 C 函
数调用给 Python 代码。

一个与 "PyArg_ParseTuple()" 的不同是，后面可能需要的要求返回一个元组
(Python参数里诶包总是在内部描述为元组)，比如用于传递给其他 Python 函数以
参数。 "Py_BuildValue()" 并不总是生成元组，在多于 1 个参数时会生成元组，
而如果没有参数则返回 "None" ，一个参数则直接返回该参数的对象。如果要求
强制生成一个长度为空的元组，或包含一个元素的元组，需要在格式字符串中加
上括号。

例子(左侧是调用，右侧是 Python 值结果)：

   Py_BuildValue("")                        None
   Py_BuildValue("i", 123)                  123
   Py_BuildValue("iii", 123, 456, 789)      (123, 456, 789)
   Py_BuildValue("s", "hello")              'hello'
   Py_BuildValue("y", "hello")              b'hello'
   Py_BuildValue("ss", "hello", "world")    ('hello', 'world')
   Py_BuildValue("s#", "hello", 4)          'hell'
   Py_BuildValue("y#", "hello", 4)          b'hell'
   Py_BuildValue("()")                      ()
   Py_BuildValue("(i)", 123)                (123,)
   Py_BuildValue("(ii)", 123, 456)          (123, 456)
   Py_BuildValue("(i,i)", 123, 456)         (123, 456)
   Py_BuildValue("[i,i]", 123, 456)         [123, 456]
   Py_BuildValue("{s:i,s:i}",
                 "abc", 123, "def", 456)    {'abc': 123, 'def': 456}
   Py_BuildValue("((ii)(ii)) (ii)",
                 1, 2, 3, 4, 5, 6)          (((1, 2), (3, 4)), (5, 6))


1.10. 引用计数
==============

在 C/C++语言中，程序员负责动态分配和回收堆(heap)当中的内存。在 C 里，通过
函数 "malloc()" 和 "free()" 来完成。在 C++里是操作 "new" 和 "delete" 来
实现相同的功能。

每个由 "malloc()" 分配的内存块，最终都要由 "free()" 退回到可用内存池里
面去。而调用 "free()" 的时机非常重要，如果一个内存块忘了 "free()" 则会
导致内存泄漏，这块内存在程序结束前将无法重新使用。这叫做 *内存泄漏* 。
而如果对同一内存块 "free()" 了以后，另外一个指针再次访问，则再次使用
"malloc()" 复用这块内存会导致冲突。这叫做 *野指针* 。等同于使用未初始
化的数据，core dump，错误结果，神秘的崩溃等。

内存泄露往往发生在一些并不常见的代码流程上面。比如一个函数申请了内存以
后，做了些计算，然后释放内存块。现在一些对函数的修改可能增加对计算的测
试并检测错误条件，然后过早的从函数返回了。这很容易忘记在退出前释放内存
，特别是后期修改的代码。这种内存泄漏，一旦引入，通常很长时间都难以检测
到，错误退出被调用的频度较低，而现代电脑又有非常巨大的虚拟内存，所以泄
漏仅在长期运行或频繁调用泄漏函数时才会变得明显。因此，有必要避免内存泄
漏，通过代码规范会策略来最小化此类错误。

Python通过 "malloc()" 和 "free()" 包含大量的内存分配和释放，同样需要避
免内存泄漏和野指针。他选择的方法就是 *引用计数* 。其原理比较简单：每个
对象都包含一个计数器，计数器的增减与对象引用的增减直接相关，当引用计数
为 0 时，表示对象已经没有存在的意义了，对象就可以删除了。

另一个叫法是 *自动垃圾回收* 。(有时引用计数也被看作是垃圾回收策略，于
是这里的"自动"用以区分两者)。自动垃圾回收的优点是用户不需要明确的调用
"free()" 。(另一个优点是改善速度或内存使用，然而这并不难)。缺点是对 C，
没有可移植的自动垃圾回收器，而引用计数则可以可移植的实现(只要
"malloc()" 和 "free()" 函数是可用的，这也是 C 标准担保的)。也许以后有一
天会出现可移植的自动垃圾回收器，但在此前我们必须与引用计数一起工作。

Python使用传统的引用计数实现，也提供了循环监测器，用以检测引用循环。这
使得应用无需担心直接或间接的创建了循环引用，这是引用计数垃圾收集的一个
弱点。引用循环是对象(可能直接)的引用了本身，所以循环中的每个对象的引用
计数都不是 0。典型的引用计数实现无法回收处于引用循环中的对象，或者被循
环所引用的对象，哪怕没有循环以外的引用了。

循环探测器可以检测垃圾循环并回收。 "gc" 模块提供了方法运行探测器 (
"collect()" 函数) ，而且可以在运行时配置禁用探测器。循环探测器被当作可
选组件，默认是包含的，也可以在构建时禁用，在 Unix 平台(包括 Mac OS X)使用
"--without-cycle-gc" 选项到 **configure** 脚本。如果循环探测器被禁用，
"gc" 模块就不可用了。


1.10.1. Python中的引用计数
--------------------------

有两个宏 "Py_INCREF(x)" 和 "Py_DECREF(x)" ，会处理引用计数的增减。
"Py_DECREF()" 也会在引用计数到达 0 时释放对象。为了灵活，并不会直接调用
"free()" ，而是通过对象的 *类型对象* 的函数指针来调用。为了这个目的(或
其他的)，每个对象同时包含一个指向自身类型对象的指针。

最大的问题依旧：何时使用 "Py_INCREF(x)" 和 "Py_DECREF(x)" ？我们首先引
入一些概念。没有人"拥有"一个对象，你可以 *拥有一个引用* 到一个对象。一
个对象的引用计数定义为拥有引用的数量。引用的拥有者有责任调用
"Py_DECREF()" ，在引用不再需要时。引用的拥有关系可以被传递。有三种办法
来处置拥有的引用：传递、存储、调用 "Py_DECREF()" 。忘记处置一个拥有的
引用会导致内存泄漏。

还可以 *借用* [2] 一个对象的引用。借用的引用不应该调用 "Py_DECREF()"
。借用者必须确保不能持有对象超过拥有者借出的时间。在拥有者处置对象后使
用借用的引用是有风险的，应该完全避免 [3] 。

借用相对于引用的优点是你无需担心整条路径上代码的引用，或者说，通过借用
你无需担心内存泄漏的风险。借用的缺点是一些看起来正确代码上的借用可能会
在拥有者处置后使用对象。

借用可以变为拥有引用，通过调用 "Py_INCREF()" 。这不会影响已经借出的拥
有者的状态。这回创建一个新的拥有引用，并给予完全的拥有者责任(新的拥有
者必须恰当的处置引用，就像之前的拥有者那样)。


1.10.2. 拥有规则
----------------

当一个对象引用传递进出一个函数时，函数的接口应该指定拥有关系的传递是否
包含引用。

大多数函数返回一个对象的引用，并传递引用拥有关系。通常，所有创建对象的
函数，例如 "PyLong_FromLong()" 和 "Py_BuildValue()" ，会传递拥有关系给
接收者。即便是对象不是真正新的，你仍然可以获得对象的新引用。一个实例是
"PyLong_FromLong()" 维护了一个流行值的缓存，并可以返回已缓存项目的新引
用。

很多另一个对象提取对象的函数，也会传递引用关系，例如
"PyObject_GetAttrString()" 。这里的情况不够清晰，一些不太常用的例程是
例外的 "PyTuple_GetItem()" ， "PyList_GetItem()" ， "PyDict_GetItem()"
， "PyDict_GetItemString()" 都是返回从元组、列表、字典里借用的引用。

函数 "PyImport_AddModule()" 也会返回借用的引用，哪怕可能会返回创建的对
象：这个可能因为一个拥有的引用对象是存储在 "sys.modules" 里。

当你传递一个对象引用到另一个函数时，通常函数是借用出去的。如果需要存储
，就使用 "Py_INCREF()" 来变成独立的拥有者。这个规则有两个重要的例外：
"PyTuple_SetItem()" 和 "PyList_SetItem()" 。这些函数接受传递来的引用关
系，哪怕会失败！(注意 "PyDict_SetItem()" 及其同类不会接受引用关系，他
们是"正常的")。

当一个 C 函数被 Python 调用时，会从调用方传来的参数借用引用。调用者拥有对
象的引用，所以借用的引用生命周期可以保证到函数返回。只要当借用的引用需
要存储或传递时，就必须转换为拥有的引用，通过调用 "Py_INCREF()" 。

Python调用从 C 函数返回的对象引用时必须是拥有的引用---拥有关系被从函数传
递给调用者。


1.10.3. 危险的薄冰
------------------

有少数情况下，借用的引用看起来无害，但却可能导致问题。这通常是因为解释
器的隐式调用，并可能导致引用拥有者处置这个引用。

首先需要特别注意的情况是使用 "Py_DECREF()" 到一个无关对象，而这个对象
的引用是借用自一个列表的元素。举个实例：

   void
   bug(PyObject *list)
   {
       PyObject *item = PyList_GetItem(list, 0);

       PyList_SetItem(list, 1, PyLong_FromLong(0L));
       PyObject_Print(item, stdout, 0); /* BUG! */
   }

这个函数首先借用一个引用 "list[0]" ，然后替换 "list[1]" 为值 "0" ，最
后打印借用的引用。看起来无害是吧，但却不是。

我们跟着控制流进入 "PyList_SetItem()" 。列表拥有者引用了其所有成员，所
以当成员 1 被替换时，就必须处置原来的成员 1。现在假设原来的成员 1 是用户定
义类的实例，且假设这个类定义了 "__del__()" 方法。如果这个类实例的引用
计数是 1，那么处置动作就会调用 "__del__()" 方法。

既然是 Python 写的， "__del__()" 方法可以执行任意 Python 代码。是否可能在
"bug()" 的 "item" 废止引用呢，是的。假设列表传递到 "bug()" 会被
"__del__()" 方法所访问，就可以执行一个语句来实现 "del list[0]" ，然后
假设这是最后一个对对象的引用，就需要释放内存，从而使得 "item" 无效化。

解决方法是，当你知道了问题的根源，就容易了：临时增加引用计数。正确版本
的函数代码如下：

   void
   no_bug(PyObject *list)
   {
       PyObject *item = PyList_GetItem(list, 0);

       Py_INCREF(item);
       PyList_SetItem(list, 1, PyLong_FromLong(0L));
       PyObject_Print(item, stdout, 0);
       Py_DECREF(item);
   }

这是个真实的故事。一个旧版本的 Python 包含了这个 bug 的变种，而一些人花费
了大量时间在 C 调试器上去寻找为什么 "__del__()" 方法会失败。

这个问题的第二种情况是借用的引用涉及线程的变种。通常，Python解释器里多
个线程无法进入对方的路径，因为有个全局锁保护着 Python 整个对象空间。但可
以使用宏 "Py_BEGIN_ALLOW_THREADS" 来临时释放这个锁，重新获取锁用
"Py_END_ALLOW_THREADS" 。这通常围绕在阻塞 I/O调用外，使得其他线程可以在
等待 I/O期间使用处理器。显然，如下函数会跟之前那个有一样的问题：

   void
   bug(PyObject *list)
   {
       PyObject *item = PyList_GetItem(list, 0);
       Py_BEGIN_ALLOW_THREADS
       ...some blocking I/O call...
       Py_END_ALLOW_THREADS
       PyObject_Print(item, stdout, 0); /* BUG! */
   }


1.10.4. NULL指针
----------------

通常，函数接受对象引用作为参数，而非期待你传入 *NULL* 指针，你非这么干
会导致 dump core (或者之后导致 core dumps) 。函数返回对象引用时，返回的
*NULL* 用以指示发生了异常。 *NULL* 参数的理由在从其他函数接收时并未测
试，如果每个函数都测试 *NULL* ，就会导致大量的冗余测试，并使得代码运行
更慢。

好的方法是仅在 "源头" 测试 *NULL* ，当一个指针可能是 *NULL* 时，例如
"malloc()" 或者从一个可能抛出异常的函数。

宏 "Py_INCREF()" 和 "Py_DECREF()" 不会检查 *NULL* 指针。但他们的变种
"Py_XINCREF()" 和 "Py_XDECREF()" 会检查。

用以检查对象类型的宏( "Pytype_Check()" )不会检查 *NULL* 指针，有很多代
码会多次测试一个对象是否是预期的类型，这可能产生冗余的测试。而 *NULL*
检查没有冗余。

C函数调用机制会确保传递到 C 函数的参数列表 (例如 "args" )不会是 *NULL*
，实际上会确保总是元组 [4] 。

把 *NULL* 指针转义给 Python 用户是个严重的错误。


1.11. 在 C++中编写扩展
=====================

还可以在 C++中编写扩展模块，只是有些限制。如果主程序(Python解释器)是使
用 C 编译器来编译和链接的，全局或静态对象的构造器就不能使用。而如果是 C++
编译器来链接的就没有这个问题。函数会被 Python 解释器调用(通常就是模块初
始化函数)必须声明为 "extern "C"" 。而是否在 "extern "C" {...}" 里包含
Python头文件则不是那么重要，因为如果定义了符号 "__cplusplus" 则已经是
这么声明的了(所有现代 C++编译器都会定义这个符号)。


1.12. 给扩展模块提供 C API
=========================

很多扩展模块提供了新的函数和类型供 Python 使用，但有时扩展模块里的代码也
可以被其他扩展模块使用。例如，一个扩展模块可以实现一个类型
"collection" 看起来是没有顺序的。就像是 Python 列表类型，拥有 C API允许扩
展模块来创建和维护列表，这个新的集合类型可以有一堆 C 函数用于给其他扩展
模块直接使用。

开始看起来很简单：只需要编写函数(无需声明为 "static" )，提供恰当的头文
件，以及 C API的文档。实际上在所有扩展模块都是静态链接到 Python 解释器时
也是可以正常工作的。当模块以共享库链接时，一个模块中的符号定义对另一个
模块不可见。可见的细节依赖于操作系统，一些系统的 Python 解释器使用全局命
名空间(例如 Windows)，有些则在链接时需要一个严格的已导入符号列表(一个例
子是 AIX)，或者提供可选的不同策略(如 Unix 系列)。即便是符号是全局可见的，
你要调用的模块也可能尚未加载。

可移植性需要不能对符号可见性做任何假设。这意味着扩展模块里的所有符号都
应该声明为 "static" ，除了模块的初始化函数，来避免与其他扩展模块的命名
冲突(在段落 模块方法表和初始化函数 中讨论) 。这意味着符号应该 *必须*
通过其他导出方式来供其他扩展模块访问。

Python提供了一个特别的机制来传递 C 级别信息(指针)，从一个扩展模块到另一
个：Capsules。一个 Capsule 是一个 Python 数据类型，会保存指针( "void *" )
。Capsule只能通过其 C API来创建和访问，但可以像其他 Python 对象一样的传递
。通常，我们可以指定一个扩展模块命名空间的名字。其他扩展模块可以导入这
个模块，获取这个名字的值，然后从 Capsule 获取指针。

Capsule可以用多种方式导出 C API给扩展模块。每个函数可以用自己的 Capsule
，或者所有 C API指针可以存储在一个数组里，数组地址再发布给 Capsule。存储
和获取指针也可以用多种方式，供客户端模块使用。

使用的方法，对 Capsule 的名字很重要。函数 "PyCapsule_New()" 会接受一个名
字参数( "const char *" )，你可以传入 *NULL* 给名字，但强烈建议指定个名
字。恰当的命名 Capsule 提供了一定程度的运行时类型安全；而却没有可行的方
法来告知我们一个未命名的 Capsule。

通常来说，Capsule用于暴露 C API，其名字应该遵循如下规范：

   modulename.attributename

便利函数 "PyCapsule_Import()" 可以方便的载入通过 Capsule 提供的 C API，仅
在 Capsule 的名字匹配时。这个行为为 C API用户提供了高度的确定性来载入正确
的 C API。

如下例子展示了将大部分负担交由导出模块作者的方法，适用于常用的库模块。
其会存储所有 C API指针(例子里只有一个)在 "void" 指针的数组里，并使其值
变为 Capsule。对应的模块头文件提供了宏来管理导入模块和获取 C API指针；客
户端模块只需要在访问 C API前调用这个宏即可。

导出的模块修改自 "spam" 模块，来自 一个简单的例子 段落。函数
"spam.system()" 不会直接调用 C 库函数 "system()" ，但一个函数
"PySpam_System()" 会负责调用，当然现实中会更复杂些(例如添加 "spam" 到
每个命令)。函数 "PySpam_System()" 也会导出给其他扩展模块。

函数 "PySpam_System()" 是个纯 C 函数，声明 "static" 就像其他地方那样:

   static int
   PySpam_System(const char *command)
   {
       return system(command);
   }

函数 "spam_system()" 按照如下方式修改:

   static PyObject *
   spam_system(PyObject *self, PyObject *args)
   {
       const char *command;
       int sts;

       if (!PyArg_ParseTuple(args, "s", &command))
           return NULL;
       sts = PySpam_System(command);
       return PyLong_FromLong(sts);
   }

在模块开头，在此行后:

   #include "Python.h"

添加另外两行:

   #define SPAM_MODULE
   #include "spammodule.h"

"#define" 用于告知头文件需要包含给导出的模块，而不是客户端模块。最终，
模块的初始化函数必须负责初始化 C API指针数组:

   PyMODINIT_FUNC
   PyInit_spam(void)
   {
       PyObject *m;
       static void *PySpam_API[PySpam_API_pointers];
       PyObject *c_api_object;

       m = PyModule_Create(&spammodule);
       if (m == NULL)
           return NULL;

       /* Initialize the C API pointer array */
       PySpam_API[PySpam_System_NUM] = (void *)PySpam_System;

       /* Create a Capsule containing the API pointer array's address */
       c_api_object = PyCapsule_New((void *)PySpam_API, "spam._C_API", NULL);

       if (c_api_object != NULL)
           PyModule_AddObject(m, "_C_API", c_api_object);
       return m;
   }

注意 "PySpam_API" 声明为 "static" ；此外指针数组会在 "PyInit_spam()"
结束后消失!

头文件 "spammodule.h" 里的一堆工作，看起来如下所示:

   #ifndef Py_SPAMMODULE_H
   #define Py_SPAMMODULE_H
   #ifdef __cplusplus
   extern "C" {
   #endif

   /* Header file for spammodule */

   /* C API functions */
   #define PySpam_System_NUM 0
   #define PySpam_System_RETURN int
   #define PySpam_System_PROTO (const char *command)

   /* Total number of C API pointers */
   #define PySpam_API_pointers 1


   #ifdef SPAM_MODULE
   /* This section is used when compiling spammodule.c */

   static PySpam_System_RETURN PySpam_System PySpam_System_PROTO;

   #else
   /* This section is used in modules that use spammodule's API */

   static void **PySpam_API;

   #define PySpam_System \
    (*(PySpam_System_RETURN (*)PySpam_System_PROTO) PySpam_API[PySpam_System_NUM])

   /* Return -1 on error, 0 on success.
    * PyCapsule_Import will set an exception if there's an error.
    */
   static int
   import_spam(void)
   {
       PySpam_API = (void **)PyCapsule_Import("spam._C_API", 0);
       return (PySpam_API != NULL) ? 0 : -1;
   }

   #endif

   #ifdef __cplusplus
   }
   #endif

   #endif /* !defined(Py_SPAMMODULE_H) */

客户端模块必须在其初始化函数里按顺序调用函数 "import_spam()" (或其他宏
)才能访问函数 "PySpam_System()" 。

   PyMODINIT_FUNC
   PyInit_client(void)
   {
       PyObject *m;

       m = PyModule_Create(&clientmodule);
       if (m == NULL)
           return NULL;
       if (import_spam() < 0)
           return NULL;
       /* additional initialization can happen here */
       return m;
   }

这种方法的主要缺点是，文件 "spammodule.h" 过于复杂。当然，对每个要导出
的函数，基本结构是相似的，所以只需要学习一次。

最后需要提醒的是 Capsule 提供了额外的功能，用于存储在 Capsule 里的指针的内
存分配和释放。细节参考 Python/C API参考手册的章节 胶囊 和 Capsule 的实现
(在 Python 源码发行包的 "Include/pycapsule.h" 和 "Objects/pycapsule.c" )
。

-[ 脚注 ]-

[1] 这个函数的接口已经在标准模块 "os" 里了，这里作为一个简单而直接
    的例 子。

[2] 术语"借用"一个引用是不完全正确的：拥有者仍然有引用的拷贝。

[3] 检查引用计数至少为 1 **没有用** ，引用计数本身可以在已经释放的
    内存 里，并有可能被其他对象所用。

[4] 当你使用 "旧式" 风格调用约定时，这些保证不成立，尽管这依旧存在
    于很 多旧代码中。
