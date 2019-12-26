---
title: cmdline
toc: true
date: 2019-06-30
---
1. 命令行与环境
***************

CPython 解析器会扫描命令行与环境用于获取各种设置信息。

**CPython implementation detail:** 其他实现的命令行方案可能有所不同。
更多相关资源请参阅 其他实现。


1.1. 命令行
===========

对 Python 发起调用时，你可以指定以下的任意选项:

   Python [-bBdEhiIOqsSuvVWx?] [-c command | -m module-name | script | - ] [args]

当然最常见的用例就是简单地启动执行一个脚本:

   Python myscript.py


1.1.1. 接口选项
---------------

解释器接口类似于 UNIX shell，但提供了一些额外的发起调用方法:

* 当调用时附带连接到某个 tty 设备的标准输入时，它会提示输入命令并执
  行 它们，直到读入一个 EOF（文件结束字符，其产生方式是在 UNIX 中按
  "Ctrl-D" 或在 Windows 中按 "Ctrl-Z, Enter"。）

* 当调用时附带一个文件名参数或以一个文件作为标准输入时，它会从该文件
  读 取并执行脚本程序。

* 当调用时附带一个目录名参数时，它会从该目录读取并执行具有适当名称的
  脚 本程序。

* 当调用时附带 "-c command" 时，它会执行 *command* 所给出的 Python
  语 句。 在这里 *command* 可以包含以换行符分隔的多条语句。 请注意前导
  空 格在 Python 语句中是有重要作用的！

* 当调用时附带 "-m module-name" 时，会在 Python 模块路径中查找指定的
  模 块，并将其作为脚本程序执行。

在非交互模式下，会对全部输入先解析再执行。

一个接口选项会终结解释器所读入的选项列表，后续的所有参数将被放入
"sys.argv" -- 请注意其中首个元素即第零项 ("sys.argv[0]") 会是一个表示
程序源的字符串。

-c <command>

   执行 *command* 中的 Python 代码。 *command* 可以为一条或以换行符分
   隔的多条语句，其中前导空格像在普通模块代码中一样具有作用。

   如果给出此选项，"sys.argv" 的首个元素将为 ""-c"" 并且当前目录将被加
   入 "sys.path" 的开头（以允许该目录中的模块作为最高层级模块被导入）
   。

-m <module-name>

   在 "sys.path" 中搜索指定名称的模块并将其内容作为 "__main__" 模块来
   执行。

   由于该参数为 *module* 名称，你不应给出文件扩展名 (".py")。 模块名称
   应为绝对有效的 Python 模块名称，但具体实现可能并不总是强制要求这一
   点（例如它可能允许你使用包含连字符的名称）。

   包名称（包括命名空间包）也允许使用。 当所提供的是包名称而非普通模块
   名称时，解释器将把 "<pkg>.__main__" 作为主模块来执行。 此行为特意被
   设计为与作为脚本参数传递给解释器的目录和 zip 文件的处理方式类似。

   注解: 此选项不适用于内置模块和以 C 编写的扩展模块，因为它们并没有
     对应的 Python 模块文件。 但是它仍然适用于预编译的模块，即使没有可
     用的初 始源文件。

   如果给出此选项，"sys.argv" 的首个元素将为模块文件的完整路径 (在定位
   模块文件期间，首个元素将设为 ""-m"")。 与 "-c" 选项一样，当前目录将
   被加入 "sys.path" 的开头。

   许多标准库模块都包含作为脚本执行时会被发起调用的代码。 其中的一个例
   子是 "timeit" 模块:

      Python -mtimeit -s 'setup here' 'benchmarked code here'
      Python -mtimeit -h # for details

   参见:

     "runpy.run_module()"
        Python 代码可以直接使用的等效功能

     **PEP 338** -- 将模块作为脚本执行

   在 3.1 版更改: 提供包名称来运行 "__main__" 子模块。

   在 3.4 版更改: 同样支持命名空间包

-

   从标准输入 ("sys.stdin") 读取命令。 如果标准输入为一个终端，则使用
   "-i"。

   如果给出此选项，"sys.argv" 的首个元素将为 ""-"" 并且当前目录将被加
   入 "sys.path" 的开头。

<script>

   执行 *script* 中的 Python 代码，该参数应为一个（绝对或相对）文件系
   统路径，指向某个 Python 文件、包含 "__main__.py" 文件的目录，或包含
   "__main__.py" 文件的 zip 文件。

   如果给出此选项，"sys.argv" 的首个元素将为在命令行中指定的脚本名称。

   如果脚本名称直接指向一个 Python 文件，则包含该文件的目录将被加入
   "sys.path" 的开头，并且该文件会被作为 "__main__" 模块来执行。

   如果脚本名称指向一个目录或 zip 文件，则脚本名称将被加入 "sys.path"
   的开头，并且该位置中的 "__main__.py" 文件会被作为 "__main__" 模块来
   执行。

   参见:

     "runpy.run_path()"
        Python 代码可以直接使用的等效功能

如果没有给出接口选项，则使用 "-i"，"sys.argv[0]" 将为空字符串 ("""")，
并且当前目录会被加入 "sys.path" 的开头。 此外，tab 补全和历史编辑会自
动启用，如果你的系统平台支持此功能的话 (参见 Readline configuration)。

参见: 调用解释器

在 3.4 版更改: 自动启用 tab 补全和历史编辑。


1.1.2. 通用选项
---------------

-?
-h
--help

   打印全部命令行选项的简短描述。

-V
--version

   打印 Python 版本号并退出。 示例输出信息如下:

      Python 3.6.0b2+

   如果重复给出，则打印有关构建的更多信息，例如:

      Python 3.6.0b2+ (3.6:84a3c5003510+, Oct 26 2016, 02:33:55)
      [GCC 6.2.0 20161005]

   3.6 新版功能: "-VV" 选项。


1.1.3. 其他选项
---------------

-b

   在将 "bytes" 或 "bytearray" 与 "str" 或是将 "bytes" 与 "int" 比较时
   发出警告。 如果重复给出该选项 ("-bb") 则会引发错误。

   在 3.5 版更改: 影响 "bytes" 与 "int" 的比较。

-B

   如果给出此选项，Python 将不会试图在导入源模块时写入 ".pyc" 文件。
   另请参阅 "PythonDONTWRITEBYTECODE"。

-d

   Turn on parser debugging output (for wizards only, depending on
   compilation options).  See also "PythonDEBUG".

-E

   忽略所有 "Python*" 环境变量，例如可能已设置的 "PythonPATH" 和
   "PythonHOME"。

-i

   当有脚本被作为首个参数传入或使用了 "-c" 选项时，在执行脚本或命令之
   后进入交互模式，即使是在 "sys.stdin" 并不是一个终端的时候。
   "PythonSTARTUP" 文件不会被读取。

   这一选项的用处是在脚本引发异常时检查全局变量或者栈跟踪。 另请参阅
   "PythonINSPECT"。

-I

   在隔离模式下运行 Python。 这将同时应用 -E 和 -s。 在隔离模式下
   "sys.path" 既不包含脚本所在目录也不包含用户的 site-packages 目录。
   所有 "Python*" 环境变量也会被忽略。 还可以施加更进一步的限制以防止
   用户注入恶意代码。

   3.4 新版功能.

-O

   移除 assert 语句以及任何以 "__debug__" 的值作为条件的代码。 通过在
   ".pyc" 扩展名之前添加 ".opt-1" 来扩充已编译文件 (*bytecode*) 的文件
   名  (参见 **PEP 488**)。 另请参阅 "PythonOPTIMIZE"。

   在 3.5 版更改: 依据 **PEP 488** 修改 ".pyc" 文件名。

-OO

   在启用 "-O" 的同时丢弃文档字符串。 通过在 ".pyc" 扩展名之前添加
   ".opt-2" 来扩展已编译文件 (*bytecode*) 的文件名 (参见 **PEP 488**)
   。

   在 3.5 版更改: 依据 **PEP 488** 修改 ".pyc" 文件名。

-q

   即使在交互模式下也不显示版权和版本信息。

   3.2 新版功能.

-R

   Kept for compatibility.  On Python 3.3 and greater, hash
   randomization is turned on by default.

   在先前的 Python 版本中，此选项会开启哈希随机化，这样 str, bytes 和
   datetime 的 "__hash__()" 值将会使用一个不可预测的随机值来“加盐”。
   虽然它们在单个 Python 进程中会保持不变，但在重复发起的 Python 调用
   之间则是不可预测的。

   哈希随机化旨在针对由精心选择的输入引起的拒绝服务攻击提供防护，这种
   输入利用了构造 dict 在最坏情况下的性能即 O(n^2) 复杂度。 详情请参阅
   http://www.ocert.org/advisories/ocert-2011-003.html。

   "PythonHASHSEED" 允许你为哈希种子密码设置一个固定值。

   3.2.3 新版功能.

-s

   不要将 "用户 site-packages 目录" 添加到 "sys.path"。

   参见: **PEP 370** -- 分用户的 site-packages 目录

-S

   禁用 "site" 的导入及其所附带的基于站点对 "sys.path" 的操作。 如果
   "site" 会在稍后被显式地导入也会禁用这些操作 (如果你希望触发它们则应
   调用 "site.main()")。

-u

   Force the binary layer of the stdout and stderr streams (which is
   available as their "buffer" attribute) to be unbuffered. The text
   I/O layer will still be line-buffered if writing to the console, or
   block-buffered if redirected to a non-interactive file.

   另请参阅 "PythonUNBUFFERED"。

-v

   每当一个模块被初始化时打印一条信息，显示其加载位置（文件名或内置模
   块）。 当重复给出时 ("-vv")，为搜索模块时所检查的每个文件都打印一条
   消息。 此外还提供退出时有关模块清理的信息 A。 另请参阅
   "PythonVERBOSE"。

-W arg

   警告控制。 Python 的警告机制在默认情况下会向 "sys.stderr" 打印警告
   消息。 典型的警告消息具有如下形式：

      file:line: category: message

   默认情况下，每个警告都对于其发生所在的每个源行都会打印一次。 此选项
   可控制警告打印的频繁程度。

   可以给出多个 "-W" 选项；当某个警告能与多个选项匹配时，将执行最后一
   个匹配选项的操作。 无效的 "-W" 选项将被忽略（但是，在发出第一个警告
   时会打印有关无效选项的警告消息）。

   Warnings can also be controlled from within a Python program using
   the "warnings" module.

   The simplest form of argument is one of the following action
   strings (or a unique abbreviation):

   "ignore"
      Ignore all warnings.

   "default"
      Explicitly request the default behavior (printing each warning
      once per source line).

   "all"
      Print a warning each time it occurs (this may generate many
      messages if a warning is triggered repeatedly for the same
      source line, such as inside a loop).

   "module"
      Print each warning only the first time it occurs in each module.

   "once"
      Print each warning only the first time it occurs in the program.

   "error"
      Raise an exception instead of printing a warning message.

   The full form of argument is:

      action:message:category:module:line

   Here, *action* is as explained above but only applies to messages
   that match the remaining fields.  Empty fields match all values;
   trailing empty fields may be omitted.  The *message* field matches
   the start of the warning message printed; this match is case-
   insensitive.  The *category* field matches the warning category.
   This must be a class name; the match tests whether the actual
   warning category of the message is a subclass of the specified
   warning category.  The full class name must be given.  The *module*
   field matches the (fully-qualified) module name; this match is
   case-sensitive.  The *line* field matches the line number, where
   zero matches all line numbers and is thus equivalent to an omitted
   line number.

   参见: "warnings" -- the warnings module

     **PEP 230** -- Warning framework

     "PythonWARNINGS"

-x

   跳过源中第一行，以允许使用非 Unix 形式的 "#!cmd"。 这适用于 DOS 专
   属的破解操作。

-X

   保留用于各种具体实现专属的选项。 CPython 目前定义了下列可用的值：

   * "-X faulthandler" 启用 "faulthandler"；

   * "-X showrefcount" 当程序结束或在交互解释器中的每条语句之后输出
     总 引用计数和已使用内存块计数。 此选项仅在调试版本中有效。

   * "-X tracemalloc" 使用 "tracemalloc" 模块启动对 Python 内存分配
     的 跟踪。 默认情况下，只有最近的帧会保存在跟踪的回溯信息中。 使用
     "-X tracemalloc=NFRAME" 以启动限定回溯 *NFRAME* 帧的跟踪。 请参阅
     "tracemalloc.start()" 了解详情。

   * "-X showalloccount" 当程序结束时输出每种类型的已分配对象的总数
     。 此选项仅当 Python 在定义了 "COUNT_ALLOCS" 后构建时才会生效。

   它还允许传入任意值并通过 "sys._xoptions" 字典来提取这些值。

   在 3.2 版更改: 增加了 "-X" 选项。

   3.3 新版功能: "-X faulthandler" 选项。

   3.4 新版功能: "-X showrefcount" 与 "-X tracemalloc" 选项。

   3.6 新版功能: "-X showalloccount" 选项。


1.1.4. 不应当使用的选项
-----------------------

-J

   保留给 Jython 使用。


1.2. 环境变量
=============

这些环境变量会影响 Python 的行为，它们是在命令行开关之前被处理的，但
-E 或 -I 除外。 根据约定，当存在冲突时命令行开关会覆盖环境变量的设置。

PythonHOME

   更改标准 Python 库的位置。 默认情况下库是在
   "*prefix*/lib/Python*version*" 和
   "*exec_prefix*/lib/Python*version*" 中搜索，其中 "*prefix*" 和
   "*exec_prefix*" 是由安装位置确定的目录，默认都位于 "/usr/local"。

   当 "PythonHOME" 被设为单个目录时，它的值会同时替代 "*prefix*" 和
   "*exec_prefix*"。 要为两者指定不同的值，请将 "PythonHOME" 设为
   "*prefix*:*exec_prefix*"。

PythonPATH

   增加模块文件默认搜索路径。 所用格式与终端的 "PATH" 相同：一个或多个
   由 "os.pathsep" 分隔的目录路径名称（例如 Unix 上用冒号而在 Windows
   上用分号）。 默认忽略不存在的目录。

   除了普通目录之外，单个 "PythonPATH" 条目可以引用包含纯 Python 模块的
   zip文件（源代码或编译形式）。无法从 zip 文件导入扩展模块。

   默认索引路径依赖于安装路径，但通常都是以
   "*prefix*/lib/Python*version*" 开始 (参见上文中的 "PythonHOME")。
   它 *总是* 会被添加到 "PythonPATH"。

   有一个附加目录将被插入到索引路径的 "PythonPATH" 之前，正如上文中 接
   口选项 所描述的。 搜索路径可以在 Python 程序内作为变量 "sys.path"
   来进行操作。

PythonSTARTUP

   这如果是一个可读文件的名称，该文件中的 Python 命令会在交互模式的首
   个提示符显示之前被执行。 该文件会在与交互式命令执行所在的同一命名空
   间中被执行，因此其中所定义或导入的对象可以在交互式会话中无限制地使
   用。 你还可以在这个文件中修改提示符 "sys.ps1" 和 "sys.ps2" 以及钩子
   "sys.__interactivehook__"。

PythonOPTIMIZE

   这如果被设为一个非空字符串，它就相当于指定 "-O" 选项。 如果设为一个
   整数，则它就相当于多次指定 "-O"。

PythonDEBUG

   此变量如果被设为一个非空字符串，它就相当于指定 "-d" 选项。 如果设为
   一个整数，则它就相当于多次指定 "-d"。

PythonINSPECT

   此变量如果被设为一个非空字符串，它就相当于指定 "-i" 选项。

   此变量也可由 Python 代码使用 "os.environ" 来修改以在程序终结时强制
   检查模式。

PythonUNBUFFERED

   此变量如果被设为一个非空字符串，它就相当于指定 "-u" 选项。

PythonVERBOSE

   此变量如果被设为一个非空字符串，它就相当于指定 "-v" 选项。 如果设为
   一个整数，则它就相当于多次指定 "-v"。

PythonCASEOK

   如果设置此变量，Python 将忽略 "import" 语句中的大小写。 这仅在
   Windows 和 OS X 上有效。

PythonDONTWRITEBYTECODE

   此变量如果被设为一个非空字符串，Python 将不会尝试在导入源模块时写入
   ".pyc" 文件。 这相当于指定 "-B" 选项。

PythonHASHSEED

   如果此变量未设置或设为 "random"，将使用一个随机值作为 str, bytes 和
   datetime 对象哈希运算的种子。

   如果 "PythonHASHSEED" 被设为一个整数值，它将被作为固定的种子数用来
   生成哈希随机化所涵盖的类型的 hash() 结果。

   它的目的是允许可复现的哈希运算，例如用于解释器本身的自我检测，或允
   许一组 Python 进程共享哈希值。

   该整数必须为一个 [0,4294967295] 范围内的十进制数。 指定数值 0 将禁
   用哈希随机化。

   3.2.3 新版功能.

PythonIOENCODING

   如果此变量在运行解释器之前被设置，它会覆盖通过
   "encodingname:errorhandler" 语法设置的 stdin/stdout/stderr 所用编码
   。 "encodingname" 和 ":errorhandler" 部分都是可选项，与在
   "str.encode()" 中的含义相同。

   对于 stderr，":errorhandler" 部分会被忽略；处理程序将总是为
   "'backslashreplace'"。

   在 3.4 版更改: “encodingname” 部分现在是可选的。

   在 3.6 版更改: 在 Windows 上，对于交互式控制台缓冲区会忽略此变量所
   指定的编码，除非还指定了 "PythonLEGACYWINDOWSSTDIO"。 通过标准流重
   定向的文件和管道则不受其影响。

PythonNOUSERSITE

   如果设置了此变量，Python 将不会把 "用户 site-packages 目录" 添加到
   "sys.path"。

   参见: **PEP 370** -- 分用户的 site-packages 目录

PythonUSERBASE

   定义 "用户基准目录"，它会在执行 "Python setup.py install --user" 时
   被用来计算 "用户 site-packages 目录" 的路径以及 Distutils 安装路径
   。

   参见: **PEP 370** -- 分用户的 site-packages 目录

PythonEXECUTABLE

   如果设置了此环境变量，则 "sys.argv[0]" 将被设为此变量的值而不是通过
   C 运行时所获得的值。 仅在 Mac OS X 上起作用。

PythonWARNINGS

   This is equivalent to the "-W" option. If set to a comma separated
   string, it is equivalent to specifying "-W" multiple times.

PythonFAULTHANDLER

   如果此环境变量被设为一个非空字符串，"faulthandler.enable()" 会在启
   动时被调用：为 "SIGSEGV", "SIGFPE", "SIGABRT", "SIGBUS" 和 "SIGILL"
   等信号安装一个处理句柄以转储 Python 回溯信息。 此变量等价于 "-X"
   "faulthandler" 选项。

   3.3 新版功能.

PythonTRACEMALLOC

   如果此环境变量被设为一个非空字符串，则会使用 "tracemalloc" 模块启动
   对 Python 内存分配的跟踪。 该变量的值是保存于跟踪的回溯信息中的最大
   帧数。 例如，"PythonTRACEMALLOC=1" 只保存最近的帧。 请参阅
   "tracemalloc.start()" 了解详情。

   3.4 新版功能.

PythonASYNCIODEBUG

   如果此变量被设为一个非空字符串，则会启用 "asyncio" 模块的 调试模式
   。

   3.4 新版功能.

PythonMALLOC

   设置 Python 内存分配器和/或安装调试钩子。

   设置 Python 所使用的内存分配器族群：

   * "malloc": 对所有域 ("PYMEM_DOMAIN_RAW", "PYMEM_DOMAIN_MEM",
     "PYMEM_DOMAIN_OBJ") 使用 C 库的 "malloc()" 函数。

   * "pymalloc": 对 "PYMEM_DOMAIN_MEM" 和 "PYMEM_DOMAIN_OBJ" 域使用
     pymalloc 分配器 而对 "PYMEM_DOMAIN_RAW" 域使用 "malloc()" 函数。

   安装调试钩子：

   * "debug": install debug hooks on top of the default memory
     allocator

   * "malloc_debug": 与 "malloc" 相同但也安装调试钩子

   * "pymalloc_debug": 与 "pymalloc" 相同但也安装调试钩子

   When Python is compiled in release mode, the default is "pymalloc".
   When compiled in debug mode, the default is "pymalloc_debug" and
   the debug hooks are used automatically.

   If Python is configured without "pymalloc" support, "pymalloc" and
   "pymalloc_debug" are not available, the default is "malloc" in
   release mode and "malloc_debug" in debug mode.

   See the "PyMem_SetupDebugHooks()" function for debug hooks on
   Python memory allocators.

   3.6 新版功能.

PythonMALLOCSTATS

   如果设为一个非空字符串，Python 将在每次创建新的 pymalloc 对象区域以
   及在关闭时打印 pymalloc 内存分配器 的统计信息。

   如果 "PythonMALLOC" 环境变量被用来强制开启 C 库的 "malloc()" 分配器
   ，或者如果 Python 的配置不支持 "pymalloc"，则此变量将被忽略。

   在 3.6 版更改: 此变量现在也可以被用于在发布模式下编译的 Python。 如
   果它被设置为一个空字符串则将没有任何效果。

PythonLEGACYWINDOWSFSENCODING

   如果设为一个非空字符串，则默认的文件系统编码和错误模式将分别恢复为
   它们在 3.6 版之前的值 'mbcs' 和 'replace'。 否则会使用新的默认值
   'utf-8' 和 'surrogatepass'。

   这也可以在运行时通过 "sys._enablelegacywindowsfsencoding()" 来启用
   。

   Availability: Windows

   3.6 新版功能: 有关更多详细信息，请参阅 **PEP 529**。

PythonLEGACYWINDOWSSTDIO

   如果设为一个非空字符串，则不使用新的控制台读取器和写入器。 这意味着
   Unicode 字符将根据活动控制台的代码页进行编码，而不是使用 utf-8。

   如果标准流被重定向（到文件或管道）而不是指向控制台缓冲区则该变量会
   被忽略。

   Availability: Windows

   3.6 新版功能.


1.2.1. 调试模式变量
-------------------

设置这些变量只会在 Python 的调试版本中产生影响，也就是说，如果 Python 配置
了 "--with-pydebug" 构建选项。

PythonTHREADDEBUG

   如果设置，Python将打印线程调试信息。

PythonDUMPREFS

   如果设置，Python在关闭解释器，及转储对象和引用计数后仍将保持活动。
