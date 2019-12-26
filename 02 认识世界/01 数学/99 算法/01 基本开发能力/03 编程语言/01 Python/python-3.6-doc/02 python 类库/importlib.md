---
title: importlib
toc: true
date: 2019-06-30
---
31.5. "importlib" --- The implementation of "import"
****************************************************

3.1 新版功能.

**源代码** Lib/importlib/__init__.py

======================================================================


31.5.1. 概述
============

The purpose of the "importlib" package is two-fold. One is to provide
the implementation of the "import" statement (and thus, by extension,
the "__import__()" function) in Python source code. This provides an
implementation of "import" which is portable to any Python
interpreter. This also provides an implementation which is easier to
comprehend than one implemented in a programming language other than
Python.

第二个目的是实现 "import" 的部分被公开在这个包中，使得用户更容易创建他
们自己的自定义对象 (通常被称为 *importer*) 来参与到导入过程中。

参见:

  The import statement
     "import" 语句的语言参考

  包规格说明
     包的初始规范。自从编写这个文档开始，一些语义已经发生改变了（比如
     基于 "sys.modules" 中 "None" 的重定向）。

  "__import__()" 函数
     "import" 语句是这个函数的语法糖。

  **PEP 235**
     在忽略大小写的平台上进行导入

  **PEP 263**
     定义 Python 源代码编码

  **PEP 302**
     新导入钩子

  **PEP 328**
     导入：多行和绝对/相对

  **PEP 366**
     主模块显式相对导入

  **PEP 420**
     隐式命名空间包

  **PEP 451**
     导入系统的一个模块规范类型

  **PEP 488**
     消除 PYO 文件

  **PEP 489**
     多阶段扩展模块初始化

  **PEP 3120**
     使用 UTF-8 作为默认的源编码

  **PEP 3147**
     PYC 仓库目录


31.5.2. 函数
============

importlib.__import__(name, globals=None, locals=None, fromlist=(), level=0)

   内置 "__import__()" 函数的实现。

   注解: 程序式地导入模块应该使用 "import_module()" 而不是这个函数。

importlib.import_module(name, package=None)

   导入一个模块。参数 *name* 指定了以绝对或相对导入方式导入什么模块 (
   比如要么像这样 "pkg.mod" 或者这样 "..mod")。如果参数 name 使用相对
   导入的方式来指定，那么那个参数 *packages* 必须设置为那个包名，这个
   包名作为解析这个包名的锚点 (比如  "import_module('..mod',
   'pkg.subpkg')" 将会导入 "pkg.mod")。

   "import_module()" 函数是一个对 "importlib.__import__()" 进行简化的
   包装器。 这意味着该函数的所有主义都来自于 "importlib.__import__()"
   。 这两个函数之间最重要的不同点在于 "import_module()" 返回指定的包
   或模块 (例如 "pkg.mod")，而 "__import__()" 返回最高层级的包或模块 (
   例如 "pkg")。

   如果动态导入一个自从解释器开始执行以来被创建的模块（即创建了一个
   Python 源代码文件），为了让导入系统知道这个新模块，可能需要调用
   "invalidate_caches()"。

   在 3.3 版更改: 父包会被自动导入。

importlib.find_loader(name, path=None)

   查找一个模块的加载器，可选择地在指定的 *path* 里面。如果这个模块是
   在 "sys.modules"，那么返回 "sys.modules[name].__loader__" (除非这个
   加载器是 "None" 或者是没有被设置， 在这样的情况下，会引起
   "ValueError" 异常）。 否则使用 "sys.meta_path" 的一次搜索就结束。如
   果未发现加载器，则返回 "None"。

   点状的名称没有使得它父包或模块隐式地导入，因为它需要加载它们并且可
   能不需要。为了适当地导入一个子模块，需要导入子模块的所有父包并且使
   用正确的参数提供给 *path*。

   3.3 新版功能.

   在 3.4 版更改: 如果没有设置 "__loader__"，会引起 "ValueError" 异常
   ，就像属性设置为 "None" 的时候一样。

   3.4 版后已移除: 使用 "importlib.util.find_spec()" 来代替。

importlib.invalidate_caches()

   使查找器存储在 "sys.meta_path" 中的内部缓存无效。如果一个查找器实现
   了 "invalidate_caches()"，那么它会被调用来执行那个无效过程。 如果创
   建/安装任何模块，同时正在运行的程序是为了保证所有的查找器知道新模块
   的存在，那么应该调用这个函数。

   3.3 新版功能.

importlib.reload(module)

   重新加载之前导入的 *module*。那个参数必须是一个模块对象，所以它之前
   必须已经成功导入了。这样做是有用的，如果使用外部编辑器编已经辑过了
   那个模块的源代码文件并且想在退出 Python 解释器之前试验这个新版本的
   模块。函数的返回值是那个模块对象（如果重新导入导致一个不同的对象放
   置在 "sys.modules" 中，那么那个模块对象是有可能会不同）。

   当执行 "reload()" 的时候：

   * Python 模块的代码会被重新编译并且那个模块级的代码被重新执行，通
     过 重新使用一开始加载那个模块的 *loader*，定义一个新的绑定在那个
     模块 字典中的名称的对象集合。扩展模块的``init``函数不会被调用第二
     次。

   * 与 Python 中的所有的其它对象一样，旧的对象只有在它们的引用计数为 0
     之 后才会被回收。

   * 模块命名空间中的名称重新指向任何新的或更改后的对象。

   * 其他旧对象的引用（例如那个模块的外部名称）不会被重新绑定到引用
     的 新对象的，并且如果有需要，必须在出现的每个命名空间中进行更新。

   有一些其他注意事项：

   当一个模块被重新加载的时候，它的字典（包含了那个模块的全区变量）会
   被保留。名称的重新定义会覆盖旧的定义，所以通常来说这不是问题。如果
   一个新模块没有定义在旧版本模块中定义的名称，则将保留旧版本中的定义
   。这一特性可用于作为那个模块的优点，如果它维护一个全局表或者对象的
   缓存 —— 使用 "try" 语句，就可以测试表的存在并且跳过它的初始化，如果
   有需要的话:

      try:
          cache
      except NameError:
          cache = {}

   重新加载内置的或者动态加载模块，通常来说不是很有用处。不推荐重新加
   载""sys"，"__main__"，"builtins" 和其它关键模块。在很多例子中，扩展
   模块并不是设计为不止一次的初始化，并且当重新加载时，可能会以任意方
   式失败。

   If a module imports objects from another module using "from" ...
   "import" ..., calling "reload()" for the other module does not
   redefine the objects imported from it --- one way around this is to
   re-execute the "from" statement, another is to use "import" and
   qualified names (*module.name*) instead.

   如果一个模块创建一个类的实例，重新加载定义那个类的模块不影响那些实
   例的方法定义———它们继续使用旧类中的定义。对于子类来说同样是正确的。

   3.4 新版功能.


31.5.3. "importlib.abc" —— 关于导入的抽象基类
=============================================

**源代码：** Lib/importlib/abc.py

======================================================================

The "importlib.abc" module contains all of the core abstract base
classes used by "import". Some subclasses of the core abstract base
classes are also provided to help in implementing the core ABCs.

ABC 类的层次结构：

   object
    +-- Finder (deprecated)
    |    +-- MetaPathFinder
    |    +-- PathEntryFinder
    +-- Loader
         +-- ResourceLoader --------+
         +-- InspectLoader          |
              +-- ExecutionLoader --+
                                    +-- FileLoader
                                    +-- SourceLoader

class importlib.abc.Finder

   代表 *finder* 的一个抽象基类

   3.3 版后已移除: 使用 "MetaPathFinder" 或 "PathEntryFinder" 来代替。

   abstractmethod find_module(fullname, path=None)

      为指定的模块查找 *loader* 定义的抽象方法。本来是在 **PEP 302**
      指定的，这个方法是在 "sys.meta_path" 和基于路径的导入子系统中使
      用。

      在 3.4 版更改: 当被调用的时候，返回 "None" 而不是引发
      "NotImplementedError"。

class importlib.abc.MetaPathFinder

   代表 *meta path finder* 的一个抽象基类。 为了保持兼容性，这是
   "Finder" 的一个子类。

   3.3 新版功能.

   find_spec(fullname, path, target=None)

      一个为了在指定的模块中查找模块 *规格* 而定义的抽象方法。如果这是
      最高层级导入，*path* 的值将会是 "None"。否则，这是一个查找子包或
      者模块的的方法并且 *path* 将会是来自父包的 "__path__" 的值。如果
      未能查找到一个规格，返回 "None"。当向这个函数传参时，"target" 是
      查找器用于做出对返回什么规格更具有根据的猜测的一个模块对象。

      3.4 新版功能.

   find_module(fullname, path)

      一个用于查找指定的模块中 *loader* 的遗留方法。如果这是最高层级的
      导入，*path* 的值将会是 "None"。否则，这是一个查找子包或者模块的
      方法，并且 *path* 的值将会是来自父包的 "__path__" 的值。如果未发
      现加载器，返回 "None"。

      如果定义了 "find_spec()" 方法，则提供了向后兼容的功能。

      在 3.4 版更改: 当调用这个方法的时候返回 "None" 而不是引发
      "NotImplementedError"。 可以使用 "find_spec()" 来提供功能。

      3.4 版后已移除: 使用 "find_spec()" 来代替。

   invalidate_caches()

      当被调用的时候，一个可选的方法应该将查找器使用的任何内部缓存进行
      无效。将在 "sys.meta_path" 上的所有查找器的缓存进行无效的时候，
      这个函数被 "importlib.invalidate_caches()" 所使用。

      在 3.4 版更改: 当方法被调用的时候，方法返回是 "None" 而不是
      "NotImplemented" 。

class importlib.abc.PathEntryFinder

   *path entry finder* 的一个抽象基类。尽管这个基类和 "MetaPathFinder"
   有一些相似之处，但是 "PathEntryFinder" 只在由 "PathFinder" 提供的基
   于路径导入子系统中使用。这个抽象类是 *Finder* 的一个子类，仅仅是因
   为兼容性的原因。

   3.3 新版功能.

   find_spec(fullname, target=None)

      一个为了在指定的模块中查找模块 *规格* 而定义的抽象方法。 查找器
      只会在赋值给 *path entry* 里面查找那个模块。如果未能找到一个规格
      ，返回 "None"。 当向这个函数传参时，"target" 是这个查找器用于做
      出对于返回什么规格更具有根据的猜测的一个模块对象。

      3.4 新版功能.

   find_loader(fullname)

      一个用于在模块中查找一个 *loader* 的遗留方法。 返回一个
      "(loader, portion)" 的 2 元组，"portion" 是一个贡献给命名空间包部
      分的文件系统位置的序列。 加载器可能是 "None"，同时正在指定的
      "portion" 表示的是贡献给命名空间包的文件系统位置。"portion" 可以
      使用一个空列表来表示加载器不是命名空间包的一部分。 如果 "loader"
      是 "None" 并且 "portion" 是一个空列表，那么命名空间包中无加载器
      或者文件系统位置可查找到（即在那个模块中未能找到任何东西）。

      如果定义了 "find_spec()" ，则提供了向后兼容的功能。

      在 3.4 版更改: 返回 "(None, [])" 而不是引发
      "NotImplementedError"。 当可于提供相应的功能的时候，使用
      "find_spec()"。

      3.4 版后已移除: 使用 "find_spec()" 来代替。

   find_module(fullname)

      "Finder.find_module`的具体实现，该方法等价于
      ``self.find_loader(fullname)[0]`()"。

      3.4 版后已移除: 使用 "find_spec()" 来代替。

   invalidate_caches()

      当被调用的时候，一个可选的方法应该将查找器使用的任何内部缓存进行
      无效。当将所有缓存的查找器的缓存进行无效的时候，该函数被
      "PathFinder.invalidate_caches()" 使用。

class importlib.abc.Loader

   *loader* 的抽象基类。 关于一个加载器的实际定义请查看 **PEP 302**。

   create_module(spec)

      当导入一个模块的时候，一个返回将要使用的那个模块对象的方法。这个
      方法可能返回 "None" ，这暗示着应该发生默认的模块创建语义。"

      3.4 新版功能.

      在 3.5 版更改: 从 Python 3.6 开始，当定义了 "exec_module()" 的时
      候，这个方法将不会是可选的。

   exec_module(module)

      当一个模块被导入或重新加载时，一个抽象方法在它自己的命名空间中执
      行那个模块。当调用 "exec_module()" 的时候，那个模块应该已经被初
      始化 了。当这个方法存在时，必须定义 "create_module()" 。

      3.4 新版功能.

      在 3.6 版更改: "create_module()" 也必须被定义。

   load_module(fullname)

      用于加载一个模块的传统方法。如果这个模块不能被导入，将引起
      "ImportError" 异常，否则返回那个被加载的模块。

      如果请求的模块已经存在 "sys.modules"，应该使用并且重新加载那个模
      块。 否则加载器应该是创建一个新的模块并且在任何家过程开始之前将
      这个新模块插入到 "sys.modules" 中，来阻止递归导入。 如果加载器插
      入了一个模块并且加载失败了，加载器必须从 "sys.modules" 中将这个
      模块移除。在加载器开始执行之前，已经在 "sys.modules" 中的模块应
      该被忽略 (查看 "importlib.util.module_for_loader()")。

      加载器应该在模块上面设置几个属性。（要知道当重新加载一个模块的时
      候，那些属性某部分可以改变）：

      * "__name__"

           模块的名字

      * "__file__"

           模块数据存储的路径(不是为了内置的模块而设置)

      * "__cached__"

           被存储或应该被存储的模块的编译版本的路径（当这个属性不恰当
           的时候不设置）。

      * "__path__"

           指定在一个包中搜索路径的一个字符串列表。这个属性不在模块上
           面进行设置。

      * "__package__"

           模块/包的父包。如果这个模块是最上层的，那么它是一个为空字符
           串的值。 "importlib.util.module_for_loader()" 装饰器可以处
           理 "__package__" 的细节。

      * "__loader__"

           用来加载那个模块的加载器。
           "importlib.util.module_for_loader()" 装饰器可以处理
           "__package__" 的细节。

      当 "exec_module()" 可用的时候，那么则提供了向后兼容的功能。

      在 3.4 版更改: 当这个方法被调用的时候，触发 "ImportError" 异常而
      不是 "NotImplementedError"。当 "exec_module()" 可用的时候，使用
      它的功能。

      3.4 版后已移除: 加载模块推荐的使用的 API 是 "exec_module()" (和
      "create_module()")。 加载器应该实现它而不是 load_module()。 当
      exec_module() 被实现的时候，导入机制关心的是 load_module() 所有
      其他的责任。

   module_repr(module)

      一个遗留方法，在实现时计算并返回给定模块的 repr，作为字符串。 模
      块类型的默认 repr() 将根据需要使用此方法的结果。

      3.3 新版功能.

      在 3.4 版更改: 是可选的方法而不是一个抽象方法。

      3.4 版后已移除: 现在导入机制会自动地关注这个方法。

class importlib.abc.ResourceLoader

   An abstract base class for a *loader* which implements the optional
   **PEP 302** protocol for loading arbitrary resources from the
   storage back-end.

   abstractmethod get_data(path)

      An abstract method to return the bytes for the data located at
      *path*. Loaders that have a file-like storage back-end that
      allows storing arbitrary data can implement this abstract method
      to give direct access to the data stored. "OSError" is to be
      raised if the *path* cannot be found. The *path* is expected to
      be constructed using a module's "__file__" attribute or an item
      from a package's "__path__".

      在 3.4 版更改: Raises "OSError" instead of
      "NotImplementedError".

class importlib.abc.InspectLoader

   An abstract base class for a *loader* which implements the optional
   **PEP 302** protocol for loaders that inspect modules.

   get_code(fullname)

      Return the code object for a module, or "None" if the module
      does not have a code object (as would be the case, for example,
      for a built-in module).  Raise an "ImportError" if loader cannot
      find the requested module.

      注解: While the method has a default implementation, it is
        suggested that it be overridden if possible for performance.

      在 3.4 版更改: No longer abstract and a concrete implementation
      is provided.

   abstractmethod get_source(fullname)

      An abstract method to return the source of a module. It is
      returned as a text string using *universal newlines*,
      translating all recognized line separators into "'\n'"
      characters.  Returns "None" if no source is available (e.g. a
      built-in module). Raises "ImportError" if the loader cannot find
      the module specified.

      在 3.4 版更改: Raises "ImportError" instead of
      "NotImplementedError".

   is_package(fullname)

      An abstract method to return a true value if the module is a
      package, a false value otherwise. "ImportError" is raised if the
      *loader* cannot find the module.

      在 3.4 版更改: Raises "ImportError" instead of
      "NotImplementedError".

   static source_to_code(data, path='<string>')

      Create a code object from Python source.

      The *data* argument can be whatever the "compile()" function
      supports (i.e. string or bytes). The *path* argument should be
      the "path" to where the source code originated from, which can
      be an abstract concept (e.g. location in a zip file).

      With the subsequent code object one can execute it in a module
      by running "exec(code, module.__dict__)".

      3.4 新版功能.

      在 3.5 版更改: Made the method static.

   exec_module(module)

      Implementation of "Loader.exec_module()".

      3.4 新版功能.

   load_module(fullname)

      Implementation of "Loader.load_module()".

      3.4 版后已移除: use "exec_module()" instead.

class importlib.abc.ExecutionLoader

   An abstract base class which inherits from "InspectLoader" that,
   when implemented, helps a module to be executed as a script. The
   ABC represents an optional **PEP 302** protocol.

   abstractmethod get_filename(fullname)

      An abstract method that is to return the value of "__file__" for
      the specified module. If no path is available, "ImportError" is
      raised.

      If source code is available, then the method should return the
      path to the source file, regardless of whether a bytecode was
      used to load the module.

      在 3.4 版更改: Raises "ImportError" instead of
      "NotImplementedError".

class importlib.abc.FileLoader(fullname, path)

   An abstract base class which inherits from "ResourceLoader" and
   "ExecutionLoader", providing concrete implementations of
   "ResourceLoader.get_data()" and "ExecutionLoader.get_filename()".

   The *fullname* argument is a fully resolved name of the module the
   loader is to handle. The *path* argument is the path to the file
   for the module.

   3.3 新版功能.

   name

      The name of the module the loader can handle.

   path

      Path to the file of the module.

   load_module(fullname)

      Calls super's "load_module()".

      3.4 版后已移除: Use "Loader.exec_module()" instead.

   abstractmethod get_filename(fullname)

      Returns "path".

   abstractmethod get_data(path)

      Reads *path* as a binary file and returns the bytes from it.

class importlib.abc.SourceLoader

   An abstract base class for implementing source (and optionally
   bytecode) file loading. The class inherits from both
   "ResourceLoader" and "ExecutionLoader", requiring the
   implementation of:

   * "ResourceLoader.get_data()"

   * "ExecutionLoader.get_filename()"

        Should only return the path to the source file; sourceless
        loading is not supported.

   The abstract methods defined by this class are to add optional
   bytecode file support. Not implementing these optional methods (or
   causing them to raise "NotImplementedError") causes the loader to
   only work with source code. Implementing the methods allows the
   loader to work with source *and* bytecode files; it does not allow
   for *sourceless* loading where only bytecode is provided.  Bytecode
   files are an optimization to speed up loading by removing the
   parsing step of Python's compiler, and so no bytecode-specific API
   is exposed.

   path_stats(path)

      Optional abstract method which returns a "dict" containing
      metadata about the specified path.  Supported dictionary keys
      are:

      * "'mtime'" (mandatory): an integer or floating-point number
        representing the modification time of the source code;

      * "'size'" (optional): the size in bytes of the source code.

      Any other keys in the dictionary are ignored, to allow for
      future extensions. If the path cannot be handled, "OSError" is
      raised.

      3.3 新版功能.

      在 3.4 版更改: Raise "OSError" instead of "NotImplementedError".

   path_mtime(path)

      Optional abstract method which returns the modification time for
      the specified path.

      3.3 版后已移除: This method is deprecated in favour of
      "path_stats()".  You don't have to implement it, but it is still
      available for compatibility purposes. Raise "OSError" if the
      path cannot be handled.

      在 3.4 版更改: Raise "OSError" instead of "NotImplementedError".

   set_data(path, data)

      Optional abstract method which writes the specified bytes to a
      file path. Any intermediate directories which do not exist are
      to be created automatically.

      When writing to the path fails because the path is read-only
      ("errno.EACCES"/"PermissionError"), do not propagate the
      exception.

      在 3.4 版更改: No longer raises "NotImplementedError" when
      called.

   get_code(fullname)

      Concrete implementation of "InspectLoader.get_code()".

   exec_module(module)

      Concrete implementation of "Loader.exec_module()".

      3.4 新版功能.

   load_module(fullname)

      Concrete implementation of "Loader.load_module()".

      3.4 版后已移除: Use "exec_module()" instead.

   get_source(fullname)

      Concrete implementation of "InspectLoader.get_source()".

   is_package(fullname)

      Concrete implementation of "InspectLoader.is_package()". A
      module is determined to be a package if its file path (as
      provided by "ExecutionLoader.get_filename()") is a file named
      "__init__" when the file extension is removed **and** the module
      name itself does not end in "__init__".


31.5.4. "importlib.machinery" -- Importers and path hooks
=========================================================

**Source code:** Lib/importlib/machinery.py

======================================================================

This module contains the various objects that help "import" find and
load modules.

importlib.machinery.SOURCE_SUFFIXES

   A list of strings representing the recognized file suffixes for
   source modules.

   3.3 新版功能.

importlib.machinery.DEBUG_BYTECODE_SUFFIXES

   A list of strings representing the file suffixes for non-optimized
   bytecode modules.

   3.3 新版功能.

   3.5 版后已移除: Use "BYTECODE_SUFFIXES" instead.

importlib.machinery.OPTIMIZED_BYTECODE_SUFFIXES

   A list of strings representing the file suffixes for optimized
   bytecode modules.

   3.3 新版功能.

   3.5 版后已移除: Use "BYTECODE_SUFFIXES" instead.

importlib.machinery.BYTECODE_SUFFIXES

   A list of strings representing the recognized file suffixes for
   bytecode modules (including the leading dot).

   3.3 新版功能.

   在 3.5 版更改: The value is no longer dependent on "__debug__".

importlib.machinery.EXTENSION_SUFFIXES

   A list of strings representing the recognized file suffixes for
   extension modules.

   3.3 新版功能.

importlib.machinery.all_suffixes()

   Returns a combined list of strings representing all file suffixes
   for modules recognized by the standard import machinery. This is a
   helper for code which simply needs to know if a filesystem path
   potentially refers to a module without needing any details on the
   kind of module (for example, "inspect.getmodulename()").

   3.3 新版功能.

class importlib.machinery.BuiltinImporter

   An *importer* for built-in modules. All known built-in modules are
   listed in "sys.builtin_module_names". This class implements the
   "importlib.abc.MetaPathFinder" and "importlib.abc.InspectLoader"
   ABCs.

   Only class methods are defined by this class to alleviate the need
   for instantiation.

   在 3.5 版更改: As part of **PEP 489**, the builtin importer now
   implements "Loader.create_module()" and "Loader.exec_module()"

class importlib.machinery.FrozenImporter

   An *importer* for frozen modules. This class implements the
   "importlib.abc.MetaPathFinder" and "importlib.abc.InspectLoader"
   ABCs.

   Only class methods are defined by this class to alleviate the need
   for instantiation.

class importlib.machinery.WindowsRegistryFinder

   *Finder* for modules declared in the Windows registry.  This class
   implements the "importlib.abc.MetaPathFinder" ABC.

   Only class methods are defined by this class to alleviate the need
   for instantiation.

   3.3 新版功能.

   3.6 版后已移除: Use "site" configuration instead. Future versions
   of Python may not enable this finder by default.

class importlib.machinery.PathFinder

   A *Finder* for "sys.path" and package "__path__" attributes. This
   class implements the "importlib.abc.MetaPathFinder" ABC.

   Only class methods are defined by this class to alleviate the need
   for instantiation.

   classmethod find_spec(fullname, path=None, target=None)

      Class method that attempts to find a *spec* for the module
      specified by *fullname* on "sys.path" or, if defined, on *path*.
      For each path entry that is searched, "sys.path_importer_cache"
      is checked. If a non-false object is found then it is used as
      the *path entry finder* to look for the module being searched
      for. If no entry is found in "sys.path_importer_cache", then
      "sys.path_hooks" is searched for a finder for the path entry
      and, if found, is stored in "sys.path_importer_cache" along with
      being queried about the module. If no finder is ever found then
      "None" is both stored in the cache and returned.

      3.4 新版功能.

      在 3.5 版更改: If the current working directory -- represented
      by an empty string -- is no longer valid then "None" is returned
      but no value is cached in "sys.path_importer_cache".

   classmethod find_module(fullname, path=None)

      A legacy wrapper around "find_spec()".

      3.4 版后已移除: 使用 "find_spec()" 来代替。

   classmethod invalidate_caches()

      Calls "importlib.abc.PathEntryFinder.invalidate_caches()" on all
      finders stored in "sys.path_importer_cache".

   在 3.4 版更改: Calls objects in "sys.path_hooks" with the current
   working directory for "''" (i.e. the empty string).

class importlib.machinery.FileFinder(path, *loader_details)

   A concrete implementation of "importlib.abc.PathEntryFinder" which
   caches results from the file system.

   The *path* argument is the directory for which the finder is in
   charge of searching.

   The *loader_details* argument is a variable number of 2-item tuples
   each containing a loader and a sequence of file suffixes the loader
   recognizes. The loaders are expected to be callables which accept
   two arguments of the module's name and the path to the file found.

   The finder will cache the directory contents as necessary, making
   stat calls for each module search to verify the cache is not
   outdated. Because cache staleness relies upon the granularity of
   the operating system's state information of the file system, there
   is a potential race condition of searching for a module, creating a
   new file, and then searching for the module the new file
   represents. If the operations happen fast enough to fit within the
   granularity of stat calls, then the module search will fail. To
   prevent this from happening, when you create a module dynamically,
   make sure to call "importlib.invalidate_caches()".

   3.3 新版功能.

   path

      The path the finder will search in.

   find_spec(fullname, target=None)

      Attempt to find the spec to handle *fullname* within "path".

      3.4 新版功能.

   find_loader(fullname)

      Attempt to find the loader to handle *fullname* within "path".

   invalidate_caches()

      Clear out the internal cache.

   classmethod path_hook(*loader_details)

      A class method which returns a closure for use on
      "sys.path_hooks". An instance of "FileFinder" is returned by the
      closure using the path argument given to the closure directly
      and *loader_details* indirectly.

      If the argument to the closure is not an existing directory,
      "ImportError" is raised.

class importlib.machinery.SourceFileLoader(fullname, path)

   A concrete implementation of "importlib.abc.SourceLoader" by
   subclassing "importlib.abc.FileLoader" and providing some concrete
   implementations of other methods.

   3.3 新版功能.

   name

      The name of the module that this loader will handle.

   path

      The path to the source file.

   is_package(fullname)

      Return true if "path" appears to be for a package.

   path_stats(path)

      Concrete implementation of
      "importlib.abc.SourceLoader.path_stats()".

   set_data(path, data)

      Concrete implementation of
      "importlib.abc.SourceLoader.set_data()".

   load_module(name=None)

      Concrete implementation of "importlib.abc.Loader.load_module()"
      where specifying the name of the module to load is optional.

      3.6 版后已移除: Use "importlib.abc.Loader.exec_module()"
      instead.

class importlib.machinery.SourcelessFileLoader(fullname, path)

   A concrete implementation of "importlib.abc.FileLoader" which can
   import bytecode files (i.e. no source code files exist).

   Please note that direct use of bytecode files (and thus not source
   code files) inhibits your modules from being usable by all Python
   implementations or new versions of Python which change the bytecode
   format.

   3.3 新版功能.

   name

      The name of the module the loader will handle.

   path

      The path to the bytecode file.

   is_package(fullname)

      Determines if the module is a package based on "path".

   get_code(fullname)

      Returns the code object for "name" created from "path".

   get_source(fullname)

      Returns "None" as bytecode files have no source when this loader
      is used.

   load_module(name=None)

   Concrete implementation of "importlib.abc.Loader.load_module()"
   where specifying the name of the module to load is optional.

   3.6 版后已移除: Use "importlib.abc.Loader.exec_module()" instead.

class importlib.machinery.ExtensionFileLoader(fullname, path)

   A concrete implementation of "importlib.abc.ExecutionLoader" for
   extension modules.

   The *fullname* argument specifies the name of the module the loader
   is to support. The *path* argument is the path to the extension
   module's file.

   3.3 新版功能.

   name

      Name of the module the loader supports.

   path

      Path to the extension module.

   create_module(spec)

      Creates the module object from the given specification in
      accordance with **PEP 489**.

      3.5 新版功能.

   exec_module(module)

      Initializes the given module object in accordance with **PEP
      489**.

      3.5 新版功能.

   is_package(fullname)

      Returns "True" if the file path points to a package's "__init__"
      module based on "EXTENSION_SUFFIXES".

   get_code(fullname)

      Returns "None" as extension modules lack a code object.

   get_source(fullname)

      Returns "None" as extension modules do not have source code.

   get_filename(fullname)

      Returns "path".

      3.4 新版功能.

class importlib.machinery.ModuleSpec(name, loader, *, origin=None, loader_state=None, is_package=None)

   A specification for a module's import-system-related state.  This
   is typically exposed as the module's "__spec__" attribute.  In the
   descriptions below, the names in parentheses give the corresponding
   attribute available directly on the module object. E.g.
   "module.__spec__.origin == module.__file__".  Note however that
   while the *values* are usually equivalent, they can differ since
   there is no synchronization between the two objects.  Thus it is
   possible to update the module's "__path__" at runtime, and this
   will not be automatically reflected in
   "__spec__.submodule_search_locations".

   3.4 新版功能.

   name

   ("__name__")

   A string for the fully-qualified name of the module.

   loader

   ("__loader__")

   The loader to use for loading.  For namespace packages this should
   be set to "None".

   origin

   ("__file__")

   Name of the place from which the module is loaded, e.g. "builtin"
   for built-in modules and the filename for modules loaded from
   source. Normally "origin" should be set, but it may be "None" (the
   default) which indicates it is unspecified.

   submodule_search_locations

   ("__path__")

   List of strings for where to find submodules, if a package ("None"
   otherwise).

   loader_state

   Container of extra module-specific data for use during loading (or
   "None").

   cached

   ("__cached__")

   String for where the compiled module should be stored (or "None").

   parent

   ("__package__")

   (Read-only) Fully-qualified name of the package to which the module
   belongs as a submodule (or "None").

   has_location

   Boolean indicating whether or not the module's "origin" attribute
   refers to a loadable location.


31.5.5. "importlib.util" -- Utility code for importers
======================================================

**Source code:** Lib/importlib/util.py

======================================================================

This module contains the various objects that help in the construction
of an *importer*.

importlib.util.MAGIC_NUMBER

   The bytes which represent the bytecode version number. If you need
   help with loading/writing bytecode then consider
   "importlib.abc.SourceLoader".

   3.4 新版功能.

importlib.util.cache_from_source(path, debug_override=None, *, optimization=None)

   Return the **PEP 3147**/**PEP 488** path to the byte-compiled file
   associated with the source *path*.  For example, if *path* is
   "/foo/bar/baz.py" the return value would be
   "/foo/bar/__pycache__/baz.cPython-32.pyc" for Python 3.2. The
   "cPython-32" string comes from the current magic tag (see
   "get_tag()"; if "sys.implementation.cache_tag" is not defined then
   "NotImplementedError" will be raised).

   The *optimization* parameter is used to specify the optimization
   level of the bytecode file. An empty string represents no
   optimization, so "/foo/bar/baz.py" with an *optimization* of "''"
   will result in a bytecode path of
   "/foo/bar/__pycache__/baz.cPython-32.pyc". "None" causes the
   interpter's optimization level to be used. Any other value's string
   representation being used, so "/foo/bar/baz.py" with an
   *optimization* of "2" will lead to the bytecode path of
   "/foo/bar/__pycache__/baz.cPython-32.opt-2.pyc". The string
   representation of *optimization* can only be alphanumeric, else
   "ValueError" is raised.

   The *debug_override* parameter is deprecated and can be used to
   override the system's value for "__debug__". A "True" value is the
   equivalent of setting *optimization* to the empty string. A "False"
   value is the same as setting *optimization* to "1". If both
   *debug_override* an *optimization* are not "None" then "TypeError"
   is raised.

   3.4 新版功能.

   在 3.5 版更改: The *optimization* parameter was added and the
   *debug_override* parameter was deprecated.

   在 3.6 版更改: 接受一个 *类路径对象*。

importlib.util.source_from_cache(path)

   Given the *path* to a **PEP 3147** file name, return the associated
   source code file path.  For example, if *path* is
   "/foo/bar/__pycache__/baz.cPython-32.pyc" the returned path would
   be "/foo/bar/baz.py".  *path* need not exist, however if it does
   not conform to **PEP 3147** or **PEP 488** format, a "ValueError"
   is raised. If "sys.implementation.cache_tag" is not defined,
   "NotImplementedError" is raised.

   3.4 新版功能.

   在 3.6 版更改: 接受一个 *类路径对象*。

importlib.util.decode_source(source_bytes)

   Decode the given bytes representing source code and return it as a
   string with universal newlines (as required by
   "importlib.abc.InspectLoader.get_source()").

   3.4 新版功能.

importlib.util.resolve_name(name, package)

   Resolve a relative module name to an absolute one.

   If  **name** has no leading dots, then **name** is simply returned.
   This allows for usage such as "importlib.util.resolve_name('sys',
   __package__)" without doing a check to see if the **package**
   argument is needed.

   "ValueError" is raised if **name** is a relative module name but
   package is a false value (e.g. "None" or the empty string).
   "ValueError" is also raised a relative name would escape its
   containing package (e.g. requesting "..bacon" from within the
   "spam" package).

   3.3 新版功能.

importlib.util.find_spec(name, package=None)

   Find the *spec* for a module, optionally relative to the specified
   **package** name. If the module is in "sys.modules", then
   "sys.modules[name].__spec__" is returned (unless the spec would be
   "None" or is not set, in which case "ValueError" is raised).
   Otherwise a search using "sys.meta_path" is done. "None" is
   returned if no spec is found.

   If **name** is for a submodule (contains a dot), the parent module
   is automatically imported.

   **name** and **package** work the same as for "import_module()".

   3.4 新版功能.

importlib.util.module_from_spec(spec)

   Create a new module based on **spec** and
   "spec.loader.create_module".

   If "spec.loader.create_module" does not return "None", then any
   pre-existing attributes will not be reset. Also, no
   "AttributeError" will be raised if triggered while accessing
   **spec** or setting an attribute on the module.

   This function is preferred over using "types.ModuleType" to create
   a new module as **spec** is used to set as many import-controlled
   attributes on the module as possible.

   3.5 新版功能.

@importlib.util.module_for_loader

   A *decorator* for "importlib.abc.Loader.load_module()" to handle
   selecting the proper module object to load with. The decorated
   method is expected to have a call signature taking two positional
   arguments (e.g. "load_module(self, module)") for which the second
   argument will be the module **object** to be used by the loader.
   Note that the decorator will not work on static methods because of
   the assumption of two arguments.

   The decorated method will take in the **name** of the module to be
   loaded as expected for a *loader*. If the module is not found in
   "sys.modules" then a new one is constructed. Regardless of where
   the module came from, "__loader__" set to **self** and
   "__package__" is set based on what
   "importlib.abc.InspectLoader.is_package()" returns (if available).
   These attributes are set unconditionally to support reloading.

   If an exception is raised by the decorated method and a module was
   added to "sys.modules", then the module will be removed to prevent
   a partially initialized module from being in left in "sys.modules".
   If the module was already in "sys.modules" then it is left alone.

   在 3.3 版更改: "__loader__" and "__package__" are automatically set
   (when possible).

   在 3.4 版更改: Set "__name__", "__loader__" "__package__"
   unconditionally to support reloading.

   3.4 版后已移除: The import machinery now directly performs all the
   functionality provided by this function.

@importlib.util.set_loader

   A *decorator* for "importlib.abc.Loader.load_module()" to set the
   "__loader__" attribute on the returned module. If the attribute is
   already set the decorator does nothing. It is assumed that the
   first positional argument to the wrapped method (i.e. "self") is
   what "__loader__" should be set to.

   在 3.4 版更改: Set "__loader__" if set to "None", as if the
   attribute does not exist.

   3.4 版后已移除: The import machinery takes care of this
   automatically.

@importlib.util.set_package

   A *decorator* for "importlib.abc.Loader.load_module()" to set the
   "__package__" attribute on the returned module. If "__package__" is
   set and has a value other than "None" it will not be changed.

   3.4 版后已移除: The import machinery takes care of this
   automatically.

importlib.util.spec_from_loader(name, loader, *, origin=None, is_package=None)

   A factory function for creating a "ModuleSpec" instance based on a
   loader.  The parameters have the same meaning as they do for
   ModuleSpec.  The function uses available *loader* APIs, such as
   "InspectLoader.is_package()", to fill in any missing information on
   the spec.

   3.4 新版功能.

importlib.util.spec_from_file_location(name, location, *, loader=None, submodule_search_locations=None)

   A factory function for creating a "ModuleSpec" instance based on
   the path to a file.  Missing information will be filled in on the
   spec by making use of loader APIs and by the implication that the
   module will be file-based.

   3.4 新版功能.

   在 3.6 版更改: 接受一个 *类路径对象*。

class importlib.util.LazyLoader(loader)

   A class which postpones the execution of the loader of a module
   until the module has an attribute accessed.

   This class **only** works with loaders that define "exec_module()"
   as control over what module type is used for the module is
   required. For those same reasons, the loader's "create_module()"
   method must return "None" or a type for which its "__class__"
   attribute can be mutated along with not using *slots*. Finally,
   modules which substitute the object placed into "sys.modules" will
   not work as there is no way to properly replace the module
   references throughout the interpreter safely; "ValueError" is
   raised if such a substitution is detected.

   注解: For projects where startup time is critical, this class
     allows for potentially minimizing the cost of loading a module if
     it is never used. For projects where startup time is not
     essential then use of this class is **heavily** discouraged due
     to error messages created during loading being postponed and thus
     occurring out of context.

   3.5 新版功能.

   在 3.6 版更改: Began calling "create_module()", removing the
   compatibility warning for "importlib.machinery.BuiltinImporter" and
   "importlib.machinery.ExtensionFileLoader".

   classmethod factory(loader)

      A static method which returns a callable that creates a lazy
      loader. This is meant to be used in situations where the loader
      is passed by class instead of by instance.

         suffixes = importlib.machinery.SOURCE_SUFFIXES
         loader = importlib.machinery.SourceFileLoader
         lazy_loader = importlib.util.LazyLoader.factory(loader)
         finder = importlib.machinery.FileFinder(path, (lazy_loader, suffixes))


31.5.6. 示例
============


31.5.6.1. Importing programmatically
------------------------------------

To programmatically import a module, use "importlib.import_module()".

   import importlib

   itertools = importlib.import_module('itertools')


31.5.6.2. Checking if a module can be imported
----------------------------------------------

If you need to find out if a module can be imported without actually
doing the import, then you should use "importlib.util.find_spec()".

   import importlib.util
   import sys

   # For illustrative purposes.
   name = 'itertools'

   spec = importlib.util.find_spec(name)
   if spec is None:
       print("can't find the itertools module")
   else:
       # If you chose to perform the actual import ...
       module = importlib.util.module_from_spec(spec)
       spec.loader.exec_module(module)
       # Adding the module to sys.modules is optional.
       sys.modules[name] = module


31.5.6.3. Importing a source file directly
------------------------------------------

To import a Python source file directly, use the following recipe
(Python 3.5 and newer only):

   import importlib.util
   import sys

   # For illustrative purposes.
   import tokenize
   file_path = tokenize.__file__
   module_name = tokenize.__name__

   spec = importlib.util.spec_from_file_location(module_name, file_path)
   module = importlib.util.module_from_spec(spec)
   spec.loader.exec_module(module)
   # Optional; only necessary if you want to be able to import the module
   # by name later.
   sys.modules[module_name] = module


31.5.6.4. Setting up an importer
--------------------------------

For deep customizations of import, you typically want to implement an
*importer*. This means managing both the *finder* and *loader* side of
things. For finders there are two flavours to choose from depending on
your needs: a *meta path finder* or a *path entry finder*. The former
is what you would put on "sys.meta_path" while the latter is what you
create using a *path entry hook* on "sys.path_hooks" which works with
"sys.path" entries to potentially create a finder. This example will
show you how to register your own importers so that import will use
them (for creating an importer for yourself, read the documentation
for the appropriate classes defined within this package):

   import importlib.machinery
   import sys

   # For illustrative purposes only.
   SpamMetaPathFinder = importlib.machinery.PathFinder
   SpamPathEntryFinder = importlib.machinery.FileFinder
   loader_details = (importlib.machinery.SourceFileLoader,
                     importlib.machinery.SOURCE_SUFFIXES)

   # Setting up a meta path finder.
   # Make sure to put the finder in the proper location in the list in terms of
   # priority.
   sys.meta_path.append(SpamMetaPathFinder)

   # Setting up a path entry finder.
   # Make sure to put the path hook in the proper location in the list in terms
   # of priority.
   sys.path_hooks.append(SpamPathEntryFinder.path_hook(loader_details))


31.5.6.5. Approximating "importlib.import_module()"
---------------------------------------------------

Import itself is implemented in Python code, making it possible to
expose most of the import machinery through importlib. The following
helps illustrate the various APIs that importlib exposes by providing
an approximate implementation of "importlib.import_module()" (Python
3.4 and newer for the importlib usage, Python 3.6 and newer for other
parts of the code).

   import importlib.util
   import sys

   def import_module(name, package=None):
       """An approximate implementation of import."""
       absolute_name = importlib.util.resolve_name(name, package)
       try:
           return sys.modules[absolute_name]
       except KeyError:
           pass

       path = None
       if '.' in absolute_name:
           parent_name, _, child_name = absolute_name.rpartition('.')
           parent_module = import_module(parent_name)
           path = parent_module.__spec__.submodule_search_locations
       for finder in sys.meta_path:
           spec = finder.find_spec(absolute_name, path)
           if spec is not None:
               break
       else:
           msg = f'No module named {absolute_name!r}'
           raise ModuleNotFoundError(msg, name=absolute_name)
       module = importlib.util.module_from_spec(spec)
       spec.loader.exec_module(module)
       sys.modules[absolute_name] = module
       if path is not None:
           setattr(parent_module, child_name, module)
       return module
