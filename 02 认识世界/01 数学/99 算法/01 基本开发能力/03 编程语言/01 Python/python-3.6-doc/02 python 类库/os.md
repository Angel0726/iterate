---
title: os
toc: true
date: 2019-06-30
---
16.1. "os" --- 操作系统接口模块
*******************************

**源代码：** Lib/os.py

======================================================================

该模块提供了一些方便使用操作系统相关功能的函数。 如果你是想读写一个文
件，请参阅 "open()"，如果你想操作路径，请参阅 "os.path" 模块，如果你想
在命令行上读取所有文件中的所有行请参阅 "fileinput" 模块。 有关创建临时
文件和目录的方法，请参阅 "tempfile" 模块，对于高级文件目录处理，请参阅
"shutil" 模块。

关于这些函数的可用性的说明：

* 所有 Python 内建的操作系统相关的模块的设计都是为了使得在同一功能可
  用 的情况下，保持接口的一致性；例如，函数 "os.stat(path)" 以相同的格
  式 返回关于 *path* 的统计信息（这个函数同时也是起源于 POSIX 接口）。

* 针对特定的操作的拓展同样在可用于 "os" 模块，但是使用它们必然会对可
  移 植性产生威胁。

* 所有接受路径或文件名的函数都同时支持字节串和字符串对象，并在返回路
  径 或文件名时使用相应类型的对象作为结果。

* An "Availability: Unix" note means that this function is commonly
  found on Unix systems.  It does not make any claims about its
  existence on a specific operating system.

* If not separately noted, all functions that claim "Availability:
  Unix" are supported on Mac OS X, which builds on a Unix core.

注解: 此模块的所有函数在遇到不可用或不可访问的文件名或路径，以及其他
  类型正 确，但不被操作系统接受的参数时，会引发 "OSError" 异常。

exception os.error

   内建的 "OSError" 异常的一个别名。

os.name

   导入的依赖特定操作系统的模块的名称。以下名称目前已注册: "'posix'",
   "'nt'", "'java'".

   参见: "sys.platform" 有更详细的描述. "os.uname()" 只给出系统提供
     的版本 信息。

     "platform" 模块对系统的标识有更详细的检查。


16.1.1. 文件名，命令行参数，以及环境变量。
==========================================

在 Python 中，使用字符串类型表示文件名、命令行参数和环境变量。 在某些
系统上，在将这些字符串传递给操作系统之前，必须将这些字符串解码为字节。
Python 使用文件系统编码来执行此转换（请参阅
"sys.getfilesystemencoding()" ）。

在 3.1 版更改: 在某些系统上，使用文件系统编码进行转换可能会失败。 在这
种情况下，Python 会使用 代理转义编码错误处理器，这意味着在解码时，不可
解码的字节被 Unicode 字符 U+DCxx 替换，并且这些字节在编码时再次转换为
原始字节。

文件系统编码必须保证成功解码小于 128 的所有字节。如果文件系统编码无法
提供此保证， API 函数可能会引发 UnicodeErrors 。


16.1.2. 进程参数
================

这些函数和数据项提供了操作当前进程和用户的信息。

os.ctermid()

   返回与进程控制终端对应的文件名。

   Availability: Unix.

os.environ

   一个表示字符串环境的 *mapping* 对象。 例如，"environ['HOME']" 是你
   的主目录（在某些平台上）的路径名，相当于 C 中的 "getenv("HOME")"。

   这个映射是在第一次导入 "os" 模块时捕获的，通常作为 Python 启动时处
   理 "site.py" 的一部分。除了通过直接修改 "os.environ" 之外，在此之后
   对环境所做的更改不会反映在 "os.environ" 中。

   如果平台支持 "putenv()" 函数，这个映射除了可以用于查询环境外还能用
   于修改环境。 当这个映射被修改时，"putenv()" 将被自动调用。

   在 Unix 系统上，键和值会使用 "sys.getfilesystemencoding()" 和
   "'surrogateescape'" 的错误处理。如果你想使用其他的编码，使用
   "environb"。

   注解: 直接调用 "putenv()" 并不会影响  "os.environ"，所以推荐直接
     修改 ``os.environ``。

   注解: 在某些平台上，包括 FreeBSD 和 Mac OS X，设置 "environ" 可能
     导致内 存泄露。参阅 "putenv()" 的系统文档。

   如果平台没有提供 "putenv()", 为了使启动的子进程使用修改后的环境，一
   份修改后的映射会被传给合适的进程创建函数。

   如果平台支持 "unsetenv()" 函数，你可以通过删除映射中元素的方式来删
   除对应的环境变量。当一个元素被从 "os.environ" 删除时，以及 "pop()"
   或 "clear()" 被调用时， "unsetenv()" 会被自动调用。

os.environb

   字节版本的 "environ": 一个以字节串表示环境的 *mapping* 对象。
   "environ" 和 "environb" 是同步的（修改 "environb" 会更新 "environ"
   ，反之亦然）。

   只有在 "supports_bytes_environ" 为 True 的时候 "environb" 才是可用
   的。

   3.2 新版功能.

os.chdir(path)
os.fchdir(fd)
os.getcwd()

   以上函数请参阅 Files and Directories 。

os.fsencode(filename)

   编码 *路径类* *文件名* 为文件系统接受的形式，使用
   "'surrogateescape'" 代理转义编码错误处理器，在 Windows 系统上会使用
   "'strict'" ；返回 "bytes" 字节类型不变。

   "fsdecode()" 是此函数的逆向函数。

   3.2 新版功能.

   在 3.6 版更改: 增加对实现了 "os.PathLike" 接口的对象的支持。

os.fsdecode(filename)

   从文件系统编码方式解码为 *路径类* 文件名，使用 "'surrogateescape'"
   代理转义编码错误处理器，在 Windows 系统上会使用 "'strict'" ；返回
   "str" 字符串不变。

   "fsencode()" 是此函数的逆向函数。

   3.2 新版功能.

   在 3.6 版更改: 增加对实现了 "os.PathLike" 接口的对象的支持。

os.fspath(path)

   返回路径的文件系统表示。

   如果传入的是 "str" 或 "bytes" 类型的字符串，将原样返回。否则
   "__fspath__()" 将被调用，如果得到的是一个 "str" 或 "bytes" 类型的对
   象，那就返回这个值。其他所有情况则会抛出 "TypeError"  异常。

   3.6 新版功能.

class os.PathLike

   描述表示一个文件系统路径的 *abstract base class* ，如
   "pathlib.PurePath" 。

   3.6 新版功能.

   abstractmethod __fspath__()

      返回当前对象的文件系统表示。

      这个方法只应该返回一个 "str" 字符串或 "bytes" 字节串，请优先选择
      "str" 字符串。

os.getenv(key, default=None)

   如果存在，返回环境变量 *key* 的值，否则返回 *default*。 *key* ，
   *default* 和返回值均为 str 字符串类型。

   在 Unix 系统上，键和值会使用 "sys.getfilesystemencoding()" 和
   ``'surrogateescape'`` 错误处理进行解码。如果你想使用其他的编码，使
   用 "os.getenvb()"。

   Availability: most flavors of Unix, Windows.

os.getenvb(key, default=None)

   如果存在环境变量 *key* 那么返回其值，否则返回 *default*。 *key* ，
   *default* 和返回值均为 bytes 字节串类型。

   "getenvb()" 仅在 "supports_bytes_environ" 为 True 时可用

   Availability: most flavors of Unix.

   3.2 新版功能.

os.get_exec_path(env=None)

   返回将用于搜索可执行文件的目录列表，与在外壳程序中启动一个进程时相
   似。指定的 *env* 应为用于搜索 PATH 的环境变量字典。默认情况下，当
   *env* 为 "None" 时，将会使用 "environ" 。

   3.2 新版功能.

os.getegid()

   返回当前进程的有效组 ID。对应当前进程执行文件的 "set id" 位。

   Availability: Unix.

os.geteuid()

   返回当前进程的有效用户 ID。

   Availability: Unix.

os.getgid()

   返回当前进程的实际组 ID。

   Availability: Unix.

os.getgrouplist(user, group)

   返回该用户所在的组 ID 列表。可能 *group* 参数没有在返回的列表中，实
   际上用户应该也是属于该 *group*。*group* 参数一般可以从储存账户信息
   的密码记录文件中找到。

   Availability: Unix.

   3.3 新版功能.

os.getgroups()

   返回当前进程对应的组 ID 列表

   Availability: Unix.

   注解: 在 Mac OS X系统中，"getgroups()" 会和其他 Unix 平台有些不同
     。如果 Python 解释器是在 "10.5" 或更早版本中部署，"getgroups()"
     返回当前 用户进程相关的有效组 ID 列表。 该列表长度由于系统预设的接
     口限制，最 长为 16。 而且在适当的权限下，返回结果还会因
     "getgroups()" 而发生 变化；如果 Python 解释器是在 "10.5" 以上版本
     中部署，"getgroups()" 返回进程所属有效用户 ID 所对应的用户的组 ID
     列表，组用户列表可能 因为进程的生存周期而发生变动，而且也不会因为
     "setgroups()" 的调用 而发生，返回的组用户列表长度也没有长度 16 的
     限制。在部署中， Python 解释器用到的变量
     "MACOSX_DEPLOYMENT_TARGET" 可以用 "sysconfig.get_config_var()"。

os.getlogin()

   返回通过控制终端进程进行登录的用户名。在多数情况下，使用
   "getpass.getuser()" 会更有效，因为后者会通过检查环境变量 "LOGNAME"
   或 "USERNAME" 来查找用户，再由 "pwd.getpwuid(os.getuid())[0]" 来获
   取当前用户 ID 的登录名。

   Availability: Unix, Windows.

os.getpgid(pid)

   根据进程 id *pid* 返回进程的组 ID 列表。如果 *pid* 为 0，则返回当前
   进程的进程组 ID 列表

   Availability: Unix.

os.getpgrp()

   返回当时进程组的 ID

   Availability: Unix.

os.getpid()

   返回当前进程 ID

os.getppid()

   返回父进程 ID。当父进程已经结束，在 Unix 中返回的 ID 是初始进程(1)中的一
   个，在 Windows 中仍然是同一个进程 ID，该进程 ID 有可能已经被进行进程所占
   用。

   Availability: Unix, Windows.

   在 3.2 版更改: 添加 WIndows 的支持。

os.getpriority(which, who)

   获取程序调度优先级。*which* 参数值可以是 "PRIO_PROCESS"，
   "PRIO_PGRP"，或 "PRIO_USER" 中的一个，*who* 是相对于 *which*
   ("PRIO_PROCESS" 的进程标识符，"PRIO_PGRP" 的进程组标识符和
   "PRIO_USER" 的用户 ID)。当 *who* 为 0 时（分别）表示调用的进程，调用
   进程的进程组或调用进程所属的真实用户 ID。

   Availability: Unix.

   3.3 新版功能.

os.PRIO_PROCESS
os.PRIO_PGRP
os.PRIO_USER

   函数 "getpriority()" 和 "setpriority()" 的参数。

   Availability: Unix.

   3.3 新版功能.

os.getresuid()

   返回一个由 (ruid, euid, suid) 所组成的元组，分别表示当前进程的真实
   用户 ID，有效用户 ID 和甲暂存用户 ID。

   Availability: Unix.

   3.2 新版功能.

os.getresgid()

   返回一个由 (rgid, egid, sgid) 所组成的元组，分别表示当前进程的真实
   组 ID，有效组 ID 和暂存组 ID。

   Availability: Unix.

   3.2 新版功能.

os.getuid()

   返回当前进程的真实用户 ID。

   Availability: Unix.

os.initgroups(username, gid)

   调用系统 initgroups()，使用指定用户所在的所有值来初始化组访问列表，
   包括指定的组 ID。

   Availability: Unix.

   3.2 新版功能.

os.putenv(key, value)

   将名为 *key* 的环境变量值设置为 *value*。该变量名修改会影响由
   "os.system()"， "popen()" ，"fork()" 和 "execv()" 发起的子进程。

   Availability: most flavors of Unix, Windows.

   注解: 在一些平台，包括 FreeBSD 和 Mac OS X，设置 "environ" 可能导
     致内存 泄露。详情参考 putenv 相关系统文档。

   当系统支持 "putenv()" 时，"os.environ" 中的参数赋值会自动转换为对
   "putenv()" 的调用。不过 "putenv()" 的调用不会更新 "os.environ"，因
   此最好使用 "os.environ" 对变量赋值。

os.setegid(egid)

   设置当前进程的有效组 ID。

   Availability: Unix.

os.seteuid(euid)

   设置当前进程的有效用户 ID。

   Availability: Unix.

os.setgid(gid)

   设置当前进程的组 ID。

   Availability: Unix.

os.setgroups(groups)

   将 *group* 参数值设置为与当进程相关联的附加组 ID 列表。*group* 参数必
   须为一个序列，每个元素应为每个组的数字 ID。该操作通常只适用于超级用
   户。

   Availability: Unix.

   注解: 在 Mac OS X 中，*groups* 的长度不能超过系统定义的最大有效组
     ID 个 数，一般为 16。 如果它没有返回与调用 setgroups() 所设置的相
     同的组 列表，请参阅 "getgroups()" 的文档。

os.setpgrp()

   根据已实现的版本（如果有）来调用系统 "setpgrp()" 或 "setpgrp(0, 0)"
   。相关说明，请参考 Unix 手册。

   Availability: Unix.

os.setpgid(pid, pgrp)

   使用系统调用 "setpgid()"，将 *pid* 对应进程的组 ID 设置为 *pgrp*。相
   关说明，请参考 Unix 手册。

   Availability: Unix.

os.setpriority(which, who, priority)

   设置程序调度优先级。 *which* 的值为 "PRIO_PROCESS", "PRIO_PGRP" 或
   "PRIO_USER" 之一，而 *who* 会相对于 *which* ("PRIO_PROCESS" 的进程
   标识符, "PRIO_PGRP" 的进程组标识符和 "PRIO_USER" 的用户 ID) 被解析
   。 *who* 值为零 (分别) 表示调用进程，调用进程的进程组或调用进程的真
   实用户 ID。 *priority* 是范围在 -20 至 19 的值。 默认优先级为 0；较
   小的优先级数值会更优先被调度。

   Availability: Unix

   3.3 新版功能.

os.setregid(rgid, egid)

   设置当前进程的真实和有效组 ID。

   Availability: Unix.

os.setresgid(rgid, egid, sgid)

   设置当前进程的真实，有效和暂存组 ID。

   Availability: Unix.

   3.2 新版功能.

os.setresuid(ruid, euid, suid)

   设置当前进程的真实，有效和暂存用户 ID。

   Availability: Unix.

   3.2 新版功能.

os.setreuid(ruid, euid)

   设置当前进程的真实和有效用户 ID。

   Availability: Unix.

os.getsid(pid)

   调用系统调用 "getsid()"。 相关语义请参阅 Unix 手册。

   Availability: Unix.

os.setsid()

   使用系统调用 "getsid()"。相关说明，请参考 Unix 手册。

   Availability: Unix.

os.setuid(uid)

   设置当前进程的用户 ID。

   Availability: Unix.

os.strerror(code)

   根据 *code* 中的错误码返回错误消息。 在某些平台上当给出未知错误码时
   "strerror()" 将返回 "NULL" 并会引发 "ValueError"。

os.supports_bytes_environ

   如果操作系统上原生环境类型是字节型则为 "True" (例如在 Windows 上为
   "False")。

   3.2 新版功能.

os.umask(mask)

   设定当前数值掩码并返回之前的掩码。

os.uname()

   返回当前操作系统的识别信息。返回值是一个有 5 个属性的对象：

   * "sysname" - 操作系统名

   * "nodename" - 机器在网络上的名称（需要先设定）

   * "release" - 操作系统发行信息

   * "version" - 操作系统版本信息

   * "machine" - 硬件标识符

   为了向后兼容，该对象也是可迭代的，像是一个按照 "sysname"，
   "nodename"，"release"，"version"，和 "machine" 顺序组成的元组。

   有些系统会将 "nodename" 截短为 8 个字符或截短至前缀部分；获取主机名
   的一个更好方式是 "socket.gethostname()"  或甚至可以用
   "socket.gethostbyaddr(socket.gethostname())"。

   Availability: recent flavors of Unix.

   在 3.3 版更改: 返回结果的类型由元组变成一个类似元组的对象，同时具有
   命名的属性。

os.unsetenv(key)

   取消设置（删除）名为 *key* 的环境变量。变量名的改变会影响由
   "os.system()"，"popen()"，"fork()" 和 "execv()" 触发的子进程。

   当系统支持 "unsetenv()" ，删除在 "os.environ" 中的变量会自动转换为
   对 "unsetenv()" 的调用。但是 "unsetenv()" 不能更新 "os.environ"，因
   此最好直接删除 "os.environ" 中的变量。

   Availability: most flavors of Unix, Windows.


16.1.3. 创建文件对象
====================

该函数创建新的 *文件对象*。 （另见 "open()" 关于打开文件的说明。）

os.fdopen(fd, *args, **kwargs)

   返回打开文件描述符 *fd* 对应文件的对象。类似内建 "open()" 函数，二
   者接受同样的参数。不同之处在于 "fdopen()" 第一个参数应该为整数。


16.1.4. 文件描述符操作
======================

这些函数对文件描述符所引用的 I/O 流进行操作。

文件描述符是一些小的整数，对应于当前进程所打开的文件。例如，标准输入的
文件描述符通常是 0，标准输出是 1，标准错误是 2。之后被进程打开的文件的文
件描述符会被依次指定为 3，4，5等。“文件描述符”这个词有点误导性，在 Unix
平台中套接字和管道也被文件描述符所引用。

当需要时，可以用 "fileno()" 可以获得 *file object* 所对应的文件描述符
。需要注意的是，直接使用文件描述符会绕过文件对象的方法，会忽略如数据内
部缓冲等情况。

os.close(fd)

   关闭文件描述符 *fd*。

   注解: 该功能适用于低级 I/O 操作，必须用于 "os.open()" 或 "pipe()"
     返回 的文件描述符。关闭由内建函数 "open()" ， "popen()" 或
     "fdopen()" 返回的 "文件对象"，则使用其相应的 "close()" 方法。

os.closerange(fd_low, fd_high)

   关闭从 *fd_low* （包括）到 *fd_high* （排除）间的文件描述符，并忽略
   错误。类似（但快于）:

      for fd in range(fd_low, fd_high):
          try:
              os.close(fd)
          except OSError:
              pass

os.device_encoding(fd)

   如果连接到终端，则返回一个与 *fd* 关联的设备描述字符，否则返回
   "None"。

os.dup(fd)

   返回一个文件描述符 *fd* 的副本。该文件描述符的副本是 不可继承的。

   在 Windows 中，当复制一个标准流（0: stdin, 1: stdout, 2: stderr）时
   ，新的文件描述符是 可继承的。

   在 3.4 版更改: 新的文件描述符现在是不可继承的。

os.dup2(fd, fd2, inheritable=True)

   Duplicate file descriptor *fd* to *fd2*, closing the latter first
   if necessary. The file descriptor *fd2* is inheritable by default,
   or non-inheritable if *inheritable* is "False".

   在 3.4 版更改: 添加可选参数 *inheritable*。

os.fchmod(fd, mode)

   将 *fd* 指定文件的权限状态修改为 *mode*。可以参考 "chmod()" 中列出
   *mode* 的可用值。从 Python 3.3开始，这相当于 "os.chmod(fd, mode)"。

   Availability: Unix.

os.fchown(fd, uid, gid)

   分别将 *fd* 指定文件的所有者和组 ID 修改为 *uid* 和 *gid* 的值。若
   不想变更其中的某个 ID，可将相应值设为 -1。参考  "chown()"。从
   Python 3.3 开始，这相当于 "os.chown(fd, uid, gid)"。

   Availability: Unix.

os.fdatasync(fd)

   强制将文件描述符 *fd* 指定文件写入磁盘。不强制更新元数据。

   Availability: Unix.

   注解: 该功能在 MacOS 中不可用。

os.fpathconf(fd, name)

   Return system configuration information relevant to an open file.
   *name* specifies the configuration value to retrieve; it may be a
   string which is the name of a defined system value; these names are
   specified in a number of standards (POSIX.1, Unix 95, Unix 98, and
   others).  Some platforms define additional names as well.  The
   names known to the host operating system are given in the
   "pathconf_names" dictionary.  For configuration variables not
   included in that mapping, passing an integer for *name* is also
   accepted.

   If *name* is a string and is not known, "ValueError" is raised.  If
   a specific value for *name* is not supported by the host system,
   even if it is included in "pathconf_names", an "OSError" is raised
   with "errno.EINVAL" for the error number.

   从 Python 3.3 起，此功能等价于 "os.pathconf(fd, name)"。

   Availability: Unix.

os.fstat(fd)

   获取文件描述符 *fd* 的状态. 返回一个 "stat_result" 对象。

   从 Python 3.3 起，此功能等价于 "os.stat(fd)"。

   参见: "stat()" 函数。

os.fstatvfs(fd)

   Return information about the filesystem containing the file
   associated with file descriptor *fd*, like "statvfs()".  As of
   Python 3.3, this is equivalent to "os.statvfs(fd)".

   Availability: Unix.

os.fsync(fd)

   Force write of file with filedescriptor *fd* to disk.  On Unix,
   this calls the native "fsync()" function; on Windows, the MS
   "_commit()" function.

   If you're starting with a buffered Python *file object* *f*, first
   do "f.flush()", and then do "os.fsync(f.fileno())", to ensure that
   all internal buffers associated with *f* are written to disk.

   Availability: Unix, Windows.

os.ftruncate(fd, length)

   Truncate the file corresponding to file descriptor *fd*, so that it
   is at most *length* bytes in size.  As of Python 3.3, this is
   equivalent to "os.truncate(fd, length)".

   Availability: Unix, Windows.

   在 3.5 版更改: 添加了 Windows 支持

os.get_blocking(fd)

   Get the blocking mode of the file descriptor: "False" if the
   "O_NONBLOCK" flag is set, "True" if the flag is cleared.

   See also "set_blocking()" and "socket.socket.setblocking()".

   Availability: Unix.

   3.5 新版功能.

os.isatty(fd)

   Return "True" if the file descriptor *fd* is open and connected to
   a tty(-like) device, else "False".

os.lockf(fd, cmd, len)

   Apply, test or remove a POSIX lock on an open file descriptor. *fd*
   is an open file descriptor. *cmd* specifies the command to use -
   one of "F_LOCK", "F_TLOCK", "F_ULOCK" or "F_TEST". *len* specifies
   the section of the file to lock.

   Availability: Unix.

   3.3 新版功能.

os.F_LOCK
os.F_TLOCK
os.F_ULOCK
os.F_TEST

   Flags that specify what action "lockf()" will take.

   Availability: Unix.

   3.3 新版功能.

os.lseek(fd, pos, how)

   Set the current position of file descriptor *fd* to position *pos*,
   modified by *how*: "SEEK_SET" or "0" to set the position relative
   to the beginning of the file; "SEEK_CUR" or "1" to set it relative
   to the current position; "SEEK_END" or "2" to set it relative to
   the end of the file. Return the new cursor position in bytes,
   starting from the beginning.

os.SEEK_SET
os.SEEK_CUR
os.SEEK_END

   Parameters to the "lseek()" function. Their values are 0, 1, and 2,
   respectively.

   3.3 新版功能: Some operating systems could support additional
   values, like "os.SEEK_HOLE" or "os.SEEK_DATA".

os.open(path, flags, mode=0o777, *, dir_fd=None)

   Open the file *path* and set various flags according to *flags* and
   possibly its mode according to *mode*.  When computing *mode*, the
   current umask value is first masked out.  Return the file
   descriptor for the newly opened file. The new file descriptor is
   non-inheritable.

   For a description of the flag and mode values, see the C run-time
   documentation; flag constants (like "O_RDONLY" and "O_WRONLY") are
   defined in the "os" module.  In particular, on Windows adding
   "O_BINARY" is needed to open files in binary mode.

   This function can support paths relative to directory descriptors
   with the *dir_fd* parameter.

   在 3.4 版更改: 新的文件描述符现在是不可继承的。

   注解: This function is intended for low-level I/O.  For normal
     usage, use the built-in function "open()", which returns a *file
     object* with "read()" and "write()" methods (and many more).  To
     wrap a file descriptor in a file object, use "fdopen()".

   3.3 新版功能: *dir_fd* 参数。

   在 3.5 版更改: 如果系统调用被中断，但信号处理程序没有触发异常，此函
   数现在会重试系统调用，而不是触发 "InterruptedError" 异常 (原因详见
   **PEP 475**)。

   在 3.6 版更改: 接受一个 *类路径对象*。

The following constants are options for the *flags* parameter to the
"open()" function.  They can be combined using the bitwise OR operator
"|".  Some of them are not available on all platforms.  For
descriptions of their availability and use, consult the *open(2)*
manual page on Unix or the MSDN on Windows.

os.O_RDONLY
os.O_WRONLY
os.O_RDWR
os.O_APPEND
os.O_CREAT
os.O_EXCL
os.O_TRUNC

   The above constants are available on Unix and Windows.

os.O_DSYNC
os.O_RSYNC
os.O_SYNC
os.O_NDELAY
os.O_NONBLOCK
os.O_NOCTTY
os.O_CLOEXEC

   这个常数仅在 Unix 系统中可用。

   在 3.3 版更改: Add "O_CLOEXEC" constant.

os.O_BINARY
os.O_NOINHERIT
os.O_SHORT_LIVED
os.O_TEMPORARY
os.O_RANDOM
os.O_SEQUENTIAL
os.O_TEXT

   这个常数仅在 Windows 系统中可用。

os.O_ASYNC
os.O_DIRECT
os.O_DIRECTORY
os.O_NOFOLLOW
os.O_NOATIME
os.O_PATH
os.O_TMPFILE
os.O_SHLOCK
os.O_EXLOCK

   The above constants are extensions and not present if they are not
   defined by the C library.

   在 3.4 版更改: Add "O_PATH" on systems that support it. Add
   "O_TMPFILE", only available on Linux Kernel 3.11   or newer.

os.openpty()

   Open a new pseudo-terminal pair. Return a pair of file descriptors
   "(master, slave)" for the pty and the tty, respectively. The new
   file descriptors are non-inheritable. For a (slightly) more
   portable approach, use the "pty" module.

   Availability: some flavors of Unix.

   在 3.4 版更改: 新的文件描述符不再可继承。

os.pipe()

   Create a pipe.  Return a pair of file descriptors "(r, w)" usable
   for reading and writing, respectively. The new file descriptor is
   non-inheritable.

   Availability: Unix, Windows.

   在 3.4 版更改: 新的文件描述符不再可继承。

os.pipe2(flags)

   Create a pipe with *flags* set atomically. *flags* can be
   constructed by ORing together one or more of these values:
   "O_NONBLOCK", "O_CLOEXEC". Return a pair of file descriptors "(r,
   w)" usable for reading and writing, respectively.

   Availability: some flavors of Unix.

   3.3 新版功能.

os.posix_fallocate(fd, offset, len)

   Ensures that enough disk space is allocated for the file specified
   by *fd* starting from *offset* and continuing for *len* bytes.

   Availability: Unix.

   3.3 新版功能.

os.posix_fadvise(fd, offset, len, advice)

   Announces an intention to access data in a specific pattern thus
   allowing the kernel to make optimizations. The advice applies to
   the region of the file specified by *fd* starting at *offset* and
   continuing for *len* bytes. *advice* is one of "POSIX_FADV_NORMAL",
   "POSIX_FADV_SEQUENTIAL", "POSIX_FADV_RANDOM", "POSIX_FADV_NOREUSE",
   "POSIX_FADV_WILLNEED" or "POSIX_FADV_DONTNEED".

   Availability: Unix.

   3.3 新版功能.

os.POSIX_FADV_NORMAL
os.POSIX_FADV_SEQUENTIAL
os.POSIX_FADV_RANDOM
os.POSIX_FADV_NOREUSE
os.POSIX_FADV_WILLNEED
os.POSIX_FADV_DONTNEED

   Flags that can be used in *advice* in "posix_fadvise()" that
   specify the access pattern that is likely to be used.

   Availability: Unix.

   3.3 新版功能.

os.pread(fd, buffersize, offset)

   Read from a file descriptor, *fd*, at a position of *offset*. It
   will read up to *buffersize* number of bytes. The file offset
   remains unchanged.

   Availability: Unix.

   3.3 新版功能.

os.pwrite(fd, str, offset)

   Write *bytestring* to a file descriptor, *fd*, from *offset*,
   leaving the file offset unchanged.

   Availability: Unix.

   3.3 新版功能.

os.read(fd, n)

   Read at most *n* bytes from file descriptor *fd*. Return a
   bytestring containing the bytes read.  If the end of the file
   referred to by *fd* has been reached, an empty bytes object is
   returned.

   注解: This function is intended for low-level I/O and must be
     applied to a file descriptor as returned by "os.open()" or
     "pipe()".  To read a "file object" returned by the built-in
     function "open()" or by "popen()" or "fdopen()", or "sys.stdin",
     use its "read()" or "readline()" methods.

   在 3.5 版更改: 如果系统调用被中断，但信号处理程序没有触发异常，此函
   数现在会重试系统调用，而不是触发 "InterruptedError" 异常 (原因详见
   **PEP 475**)。

os.sendfile(out, in, offset, count)
os.sendfile(out, in, offset, count[, headers][, trailers], flags=0)

   Copy *count* bytes from file descriptor *in* to file descriptor
   *out* starting at *offset*. Return the number of bytes sent. When
   EOF is reached return 0.

   The first function notation is supported by all platforms that
   define "sendfile()".

   On Linux, if *offset* is given as "None", the bytes are read from
   the current position of *in* and the position of *in* is updated.

   The second case may be used on Mac OS X and FreeBSD where *headers*
   and *trailers* are arbitrary sequences of buffers that are written
   before and after the data from *in* is written. It returns the same
   as the first case.

   On Mac OS X and FreeBSD, a value of 0 for *count* specifies to send
   until the end of *in* is reached.

   All platforms support sockets as *out* file descriptor, and some
   platforms allow other types (e.g. regular file, pipe) as well.

   Cross-platform applications should not use *headers*, *trailers*
   and *flags* arguments.

   Availability: Unix.

   注解: For a higher-level wrapper of "sendfile()", see
     "socket.socket.sendfile()".

   3.3 新版功能.

os.set_blocking(fd, blocking)

   Set the blocking mode of the specified file descriptor. Set the
   "O_NONBLOCK" flag if blocking is "False", clear the flag otherwise.

   See also "get_blocking()" and "socket.socket.setblocking()".

   Availability: Unix.

   3.5 新版功能.

os.SF_NODISKIO
os.SF_MNOWAIT
os.SF_SYNC

   Parameters to the "sendfile()" function, if the implementation
   supports them.

   Availability: Unix.

   3.3 新版功能.

os.readv(fd, buffers)

   Read from a file descriptor *fd* into a number of mutable *bytes-
   like objects* *buffers*. "readv()" will transfer data into each
   buffer until it is full and then move on to the next buffer in the
   sequence to hold the rest of the data. "readv()" returns the total
   number of bytes read (which may be less than the total capacity of
   all the objects).

   Availability: Unix.

   3.3 新版功能.

os.tcgetpgrp(fd)

   Return the process group associated with the terminal given by *fd*
   (an open file descriptor as returned by "os.open()").

   Availability: Unix.

os.tcsetpgrp(fd, pg)

   Set the process group associated with the terminal given by *fd*
   (an open file descriptor as returned by "os.open()") to *pg*.

   Availability: Unix.

os.ttyname(fd)

   Return a string which specifies the terminal device associated with
   file descriptor *fd*.  If *fd* is not associated with a terminal
   device, an exception is raised.

   Availability: Unix.

os.write(fd, str)

   Write the bytestring in *str* to file descriptor *fd*. Return the
   number of bytes actually written.

   注解: This function is intended for low-level I/O and must be
     applied to a file descriptor as returned by "os.open()" or
     "pipe()".  To write a "file object" returned by the built-in
     function "open()" or by "popen()" or "fdopen()", or "sys.stdout"
     or "sys.stderr", use its "write()" method.

   在 3.5 版更改: 如果系统调用被中断，但信号处理程序没有触发异常，此函
   数现在会重试系统调用，而不是触发 "InterruptedError" 异常 (原因详见
   **PEP 475**)。

os.writev(fd, buffers)

   Write the contents of *buffers* to file descriptor *fd*. *buffers*
   must be a sequence of *bytes-like objects*. Buffers are processed
   in array order. Entire contents of first buffer is written before
   proceeding to second, and so on. The operating system may set a
   limit (sysconf() value SC_IOV_MAX) on the number of buffers that
   can be used.

   "writev()" writes the contents of each object to the file
   descriptor and returns the total number of bytes written.

   Availability: Unix.

   3.3 新版功能.


16.1.4.1. Querying the size of a terminal
-----------------------------------------

3.3 新版功能.

os.get_terminal_size(fd=STDOUT_FILENO)

   Return the size of the terminal window as "(columns, lines)", tuple
   of type "terminal_size".

   The optional argument "fd" (default "STDOUT_FILENO", or standard
   output) specifies which file descriptor should be queried.

   If the file descriptor is not connected to a terminal, an "OSError"
   is raised.

   "shutil.get_terminal_size()" is the high-level function which
   should normally be used, "os.get_terminal_size" is the low-level
   implementation.

   Availability: Unix, Windows.

class os.terminal_size

   A subclass of tuple, holding "(columns, lines)" of the terminal
   window size.

   columns

      Width of the terminal window in characters.

   lines

      Height of the terminal window in characters.


16.1.4.2. Inheritance of File Descriptors
-----------------------------------------

3.4 新版功能.

A file descriptor has an "inheritable" flag which indicates if the
file descriptor can be inherited by child processes.  Since Python
3.4, file descriptors created by Python are non-inheritable by
default.

On UNIX, non-inheritable file descriptors are closed in child
processes at the execution of a new program, other file descriptors
are inherited.

On Windows, non-inheritable handles and file descriptors are closed in
child processes, except for standard streams (file descriptors 0, 1
and 2: stdin, stdout and stderr), which are always inherited.  Using
"spawn*" functions, all inheritable handles and all inheritable file
descriptors are inherited. Using the "subprocess" module, all file
descriptors except standard streams are closed, and inheritable
handles are only inherited if the *close_fds* parameter is "False".

os.get_inheritable(fd)

   Get the "inheritable" flag of the specified file descriptor (a
   boolean).

os.set_inheritable(fd, inheritable)

   Set the "inheritable" flag of the specified file descriptor.

os.get_handle_inheritable(handle)

   Get the "inheritable" flag of the specified handle (a boolean).

   Availability: Windows.

os.set_handle_inheritable(handle, inheritable)

   Set the "inheritable" flag of the specified handle.

   Availability: Windows.


16.1.5. Files and Directories
=============================

On some Unix platforms, many of these functions support one or more of
these features:

* **specifying a file descriptor:** For some functions, the *path*
  argument can be not only a string giving a path name, but also a
  file descriptor.  The function will then operate on the file
  referred to by the descriptor.  (For POSIX systems, Python will call
  the "f..." version of the function.)

  You can check whether or not *path* can be specified as a file
  descriptor on your platform using "os.supports_fd".  If it is
  unavailable, using it will raise a "NotImplementedError".

  If the function also supports *dir_fd* or *follow_symlinks*
  arguments, it is an error to specify one of those when supplying
  *path* as a file descriptor.

* **paths relative to directory descriptors:** If *dir_fd* is not
  "None", it should be a file descriptor referring to a directory, and
  the path to operate on should be relative; path will then be
  relative to that directory.  If the path is absolute, *dir_fd* is
  ignored.  (For POSIX systems, Python will call the "...at" or
  "f...at" version of the function.)

  You can check whether or not *dir_fd* is supported on your platform
  using "os.supports_dir_fd".  If it is unavailable, using it will
  raise a "NotImplementedError".

* **not following symlinks:** If *follow_symlinks* is "False", and
  the last element of the path to operate on is a symbolic link, the
  function will operate on the symbolic link itself instead of the
  file the link points to.  (For POSIX systems, Python will call the
  "l..." version of the function.)

  You can check whether or not *follow_symlinks* is supported on your
  platform using "os.supports_follow_symlinks".  If it is unavailable,
  using it will raise a "NotImplementedError".

os.access(path, mode, *, dir_fd=None, effective_ids=False, follow_symlinks=True)

   Use the real uid/gid to test for access to *path*.  Note that most
   operations will use the effective uid/gid, therefore this routine
   can be used in a suid/sgid environment to test if the invoking user
   has the specified access to *path*.  *mode* should be "F_OK" to
   test the existence of *path*, or it can be the inclusive OR of one
   or more of "R_OK", "W_OK", and "X_OK" to test permissions.  Return
   "True" if access is allowed, "False" if not. See the Unix man page
   *access(2)* for more information.

   This function can support specifying paths relative to directory
   descriptors and not following symlinks.

   If *effective_ids* is "True", "access()" will perform its access
   checks using the effective uid/gid instead of the real uid/gid.
   *effective_ids* may not be supported on your platform; you can
   check whether or not it is available using
   "os.supports_effective_ids".  If it is unavailable, using it will
   raise a "NotImplementedError".

   注解: Using "access()" to check if a user is authorized to e.g.
     open a file before actually doing so using "open()" creates a
     security hole, because the user might exploit the short time
     interval between checking and opening the file to manipulate it.
     It's preferable to use *EAFP* techniques. For example:

        if os.access("myfile", os.R_OK):
            with open("myfile") as fp:
                return fp.read()
        return "some default data"

     is better written as:

        try:
            fp = open("myfile")
        except PermissionError:
            return "some default data"
        else:
            with fp:
                return fp.read()

   注解: I/O operations may fail even when "access()" indicates that
     they would succeed, particularly for operations on network
     filesystems which may have permissions semantics beyond the usual
     POSIX permission-bit model.

   在 3.3 版更改: Added the *dir_fd*, *effective_ids*, and
   *follow_symlinks* parameters.

   在 3.6 版更改: 接受一个 *类路径对象*。

os.F_OK
os.R_OK
os.W_OK
os.X_OK

   Values to pass as the *mode* parameter of "access()" to test the
   existence, readability, writability and executability of *path*,
   respectively.

os.chdir(path)

   Change the current working directory to *path*.

   This function can support specifying a file descriptor.  The
   descriptor must refer to an opened directory, not an open file.

   3.3 新版功能: Added support for specifying *path* as a file
   descriptor on some platforms.

   在 3.6 版更改: 接受一个 *类路径对象*。

os.chflags(path, flags, *, follow_symlinks=True)

   Set the flags of *path* to the numeric *flags*. *flags* may take a
   combination (bitwise OR) of the following values (as defined in the
   "stat" module):

   * "stat.UF_NODUMP"

   * "stat.UF_IMMUTABLE"

   * "stat.UF_APPEND"

   * "stat.UF_OPAQUE"

   * "stat.UF_NOUNLINK"

   * "stat.UF_COMPRESSED"

   * "stat.UF_HIDDEN"

   * "stat.SF_ARCHIVED"

   * "stat.SF_IMMUTABLE"

   * "stat.SF_APPEND"

   * "stat.SF_NOUNLINK"

   * "stat.SF_SNAPSHOT"

   This function can support not following symlinks.

   Availability: Unix.

   3.3 新版功能: The *follow_symlinks* argument.

   在 3.6 版更改: 接受一个 *类路径对象*。

os.chmod(path, mode, *, dir_fd=None, follow_symlinks=True)

   Change the mode of *path* to the numeric *mode*. *mode* may take
   one of the following values (as defined in the "stat" module) or
   bitwise ORed combinations of them:

   * "stat.S_ISUID"

   * "stat.S_ISGID"

   * "stat.S_ENFMT"

   * "stat.S_ISVTX"

   * "stat.S_IREAD"

   * "stat.S_IWRITE"

   * "stat.S_IEXEC"

   * "stat.S_IRWXU"

   * "stat.S_IRUSR"

   * "stat.S_IWUSR"

   * "stat.S_IXUSR"

   * "stat.S_IRWXG"

   * "stat.S_IRGRP"

   * "stat.S_IWGRP"

   * "stat.S_IXGRP"

   * "stat.S_IRWXO"

   * "stat.S_IROTH"

   * "stat.S_IWOTH"

   * "stat.S_IXOTH"

   This function can support specifying a file descriptor, paths
   relative to directory descriptors and not following symlinks.

   注解: Although Windows supports "chmod()", you can only set the
     file's read-only flag with it (via the "stat.S_IWRITE" and
     "stat.S_IREAD" constants or a corresponding integer value).  All
     other bits are ignored.

   3.3 新版功能: Added support for specifying *path* as an open file
   descriptor, and the *dir_fd* and *follow_symlinks* arguments.

   在 3.6 版更改: 接受一个 *类路径对象*。

os.chown(path, uid, gid, *, dir_fd=None, follow_symlinks=True)

   Change the owner and group id of *path* to the numeric *uid* and
   *gid*.  To leave one of the ids unchanged, set it to -1.

   This function can support specifying a file descriptor, paths
   relative to directory descriptors and not following symlinks.

   See "shutil.chown()" for a higher-level function that accepts names
   in addition to numeric ids.

   Availability: Unix.

   3.3 新版功能: Added support for specifying an open file descriptor
   for *path*, and the *dir_fd* and *follow_symlinks* arguments.

   在 3.6 版更改: Supports a *path-like object*.

os.chroot(path)

   Change the root directory of the current process to *path*.

   Availability: Unix.

   在 3.6 版更改: 接受一个 *类路径对象*。

os.fchdir(fd)

   Change the current working directory to the directory represented
   by the file descriptor *fd*.  The descriptor must refer to an
   opened directory, not an open file.  As of Python 3.3, this is
   equivalent to "os.chdir(fd)".

   Availability: Unix.

os.getcwd()

   Return a string representing the current working directory.

os.getcwdb()

   Return a bytestring representing the current working directory.

os.lchflags(path, flags)

   Set the flags of *path* to the numeric *flags*, like "chflags()",
   but do not follow symbolic links.  As of Python 3.3, this is
   equivalent to "os.chflags(path, flags, follow_symlinks=False)".

   Availability: Unix.

   在 3.6 版更改: 接受一个 *类路径对象*。

os.lchmod(path, mode)

   Change the mode of *path* to the numeric *mode*. If path is a
   symlink, this affects the symlink rather than the target.  See the
   docs for "chmod()" for possible values of *mode*.  As of Python
   3.3, this is equivalent to "os.chmod(path, mode,
   follow_symlinks=False)".

   Availability: Unix.

   在 3.6 版更改: 接受一个 *类路径对象*。

os.lchown(path, uid, gid)

   Change the owner and group id of *path* to the numeric *uid* and
   *gid*.  This function will not follow symbolic links.  As of Python
   3.3, this is equivalent to "os.chown(path, uid, gid,
   follow_symlinks=False)".

   Availability: Unix.

   在 3.6 版更改: 接受一个 *类路径对象*。

os.link(src, dst, *, src_dir_fd=None, dst_dir_fd=None, follow_symlinks=True)

   Create a hard link pointing to *src* named *dst*.

   This function can support specifying *src_dir_fd* and/or
   *dst_dir_fd* to supply paths relative to directory descriptors, and
   not following symlinks.

   Availability: Unix, Windows.

   在 3.2 版更改: Added Windows support.

   3.3 新版功能: Added the *src_dir_fd*, *dst_dir_fd*, and
   *follow_symlinks* arguments.

   在 3.6 版更改: Accepts a *path-like object* for *src* and *dst*.

os.listdir(path='.')

   Return a list containing the names of the entries in the directory
   given by *path*.  The list is in arbitrary order, and does not
   include the special entries "'.'" and "'..'" even if they are
   present in the directory.

   *path* may be a *path-like object*.  If *path* is of type "bytes"
   (directly or indirectly through the "PathLike" interface), the
   filenames returned will also be of type "bytes"; in all other
   circumstances, they will be of type "str".

   This function can also support specifying a file descriptor; the
   file descriptor must refer to a directory.

   注解: To encode "str" filenames to "bytes", use "fsencode()".

   参见: The "scandir()" function returns directory entries along
     with file attribute information, giving better performance for
     many common use cases.

   在 3.2 版更改: The *path* parameter became optional.

   3.3 新版功能: Added support for specifying an open file descriptor
   for *path*.

   在 3.6 版更改: 接受一个 *类路径对象*。

os.lstat(path, *, dir_fd=None)

   Perform the equivalent of an "lstat()" system call on the given
   path. Similar to "stat()", but does not follow symbolic links.
   Return a "stat_result" object.

   On platforms that do not support symbolic links, this is an alias
   for "stat()".

   As of Python 3.3, this is equivalent to "os.stat(path,
   dir_fd=dir_fd, follow_symlinks=False)".

   This function can also support paths relative to directory
   descriptors.

   参见: "stat()" 函数。

   在 3.2 版更改: Added support for Windows 6.0 (Vista) symbolic
   links.

   在 3.3 版更改: Added the *dir_fd* parameter.

   在 3.6 版更改: Accepts a *path-like object* for *src* and *dst*.

os.mkdir(path, mode=0o777, *, dir_fd=None)

   Create a directory named *path* with numeric mode *mode*.

   If the directory already exists, "FileExistsError" is raised.

   On some systems, *mode* is ignored.  Where it is used, the current
   umask value is first masked out.  If bits other than the last 9
   (i.e. the last 3 digits of the octal representation of the *mode*)
   are set, their meaning is platform-dependent.  On some platforms,
   they are ignored and you should call "chmod()" explicitly to set
   them.

   This function can also support paths relative to directory
   descriptors.

   It is also possible to create temporary directories; see the
   "tempfile" module's "tempfile.mkdtemp()" function.

   3.3 新版功能: *dir_fd* 参数。

   在 3.6 版更改: 接受一个 *类路径对象*。

os.makedirs(name, mode=0o777, exist_ok=False)

   Recursive directory creation function.  Like "mkdir()", but makes
   all intermediate-level directories needed to contain the leaf
   directory.

   The *mode* parameter is passed to "mkdir()"; see the mkdir()
   description for how it is interpreted.

   If *exist_ok* is "False" (the default), an "OSError" is raised if
   the target directory already exists.

   注解: "makedirs()" will become confused if the path elements to
     create include "pardir" (eg. ".." on UNIX systems).

   This function handles UNC paths correctly.

   3.2 新版功能: The *exist_ok* parameter.

   在 3.4.1 版更改: Before Python 3.4.1, if *exist_ok* was "True" and
   the directory existed, "makedirs()" would still raise an error if
   *mode* did not match the mode of the existing directory. Since this
   behavior was impossible to implement safely, it was removed in
   Python 3.4.1. See bpo-21082.

   在 3.6 版更改: 接受一个 *类路径对象*。

os.mkfifo(path, mode=0o666, *, dir_fd=None)

   Create a FIFO (a named pipe) named *path* with numeric mode *mode*.
   The current umask value is first masked out from the mode.

   This function can also support paths relative to directory
   descriptors.

   FIFOs are pipes that can be accessed like regular files.  FIFOs
   exist until they are deleted (for example with "os.unlink()").
   Generally, FIFOs are used as rendezvous between "client" and
   "server" type processes: the server opens the FIFO for reading, and
   the client opens it for writing.  Note that "mkfifo()" doesn't open
   the FIFO --- it just creates the rendezvous point.

   Availability: Unix.

   3.3 新版功能: *dir_fd* 参数。

   在 3.6 版更改: 接受一个 *类路径对象*。

os.mknod(path, mode=0o600, device=0, *, dir_fd=None)

   Create a filesystem node (file, device special file or named pipe)
   named *path*. *mode* specifies both the permissions to use and the
   type of node to be created, being combined (bitwise OR) with one of
   "stat.S_IFREG", "stat.S_IFCHR", "stat.S_IFBLK", and "stat.S_IFIFO"
   (those constants are available in "stat").  For "stat.S_IFCHR" and
   "stat.S_IFBLK", *device* defines the newly created device special
   file (probably using "os.makedev()"), otherwise it is ignored.

   This function can also support paths relative to directory
   descriptors.

   Availability: Unix.

   3.3 新版功能: *dir_fd* 参数。

   在 3.6 版更改: 接受一个 *类路径对象*。

os.major(device)

   Extract the device major number from a raw device number (usually
   the "st_dev" or "st_rdev" field from "stat").

os.minor(device)

   Extract the device minor number from a raw device number (usually
   the "st_dev" or "st_rdev" field from "stat").

os.makedev(major, minor)

   Compose a raw device number from the major and minor device
   numbers.

os.pathconf(path, name)

   Return system configuration information relevant to a named file.
   *name* specifies the configuration value to retrieve; it may be a
   string which is the name of a defined system value; these names are
   specified in a number of standards (POSIX.1, Unix 95, Unix 98, and
   others).  Some platforms define additional names as well.  The
   names known to the host operating system are given in the
   "pathconf_names" dictionary.  For configuration variables not
   included in that mapping, passing an integer for *name* is also
   accepted.

   If *name* is a string and is not known, "ValueError" is raised.  If
   a specific value for *name* is not supported by the host system,
   even if it is included in "pathconf_names", an "OSError" is raised
   with "errno.EINVAL" for the error number.

   This function can support specifying a file descriptor.

   Availability: Unix.

   在 3.6 版更改: 接受一个 *类路径对象*。

os.pathconf_names

   Dictionary mapping names accepted by "pathconf()" and "fpathconf()"
   to the integer values defined for those names by the host operating
   system.  This can be used to determine the set of names known to
   the system.

   Availability: Unix.

os.readlink(path, *, dir_fd=None)

   Return a string representing the path to which the symbolic link
   points.  The result may be either an absolute or relative pathname;
   if it is relative, it may be converted to an absolute pathname
   using "os.path.join(os.path.dirname(path), result)".

   If the *path* is a string object (directly or indirectly through a
   "PathLike" interface), the result will also be a string object, and
   the call may raise a UnicodeDecodeError. If the *path* is a bytes
   object (direct or indirectly), the result will be a bytes object.

   This function can also support paths relative to directory
   descriptors.

   Availability: Unix, Windows

   在 3.2 版更改: Added support for Windows 6.0 (Vista) symbolic
   links.

   3.3 新版功能: *dir_fd* 参数。

   在 3.6 版更改: 接受一个 *类路径对象*。

os.remove(path, *, dir_fd=None)

   Remove (delete) the file *path*.  If *path* is a directory,
   "OSError" is raised.  Use "rmdir()" to remove directories.

   This function can support paths relative to directory descriptors.

   On Windows, attempting to remove a file that is in use causes an
   exception to be raised; on Unix, the directory entry is removed but
   the storage allocated to the file is not made available until the
   original file is no longer in use.

   This function is semantically identical to "unlink()".

   3.3 新版功能: *dir_fd* 参数。

   在 3.6 版更改: 接受一个 *类路径对象*。

os.removedirs(name)

   Remove directories recursively.  Works like "rmdir()" except that,
   if the leaf directory is successfully removed, "removedirs()"
   tries to successively remove every parent directory mentioned in
   *path* until an error is raised (which is ignored, because it
   generally means that a parent directory is not empty). For example,
   "os.removedirs('foo/bar/baz')" will first remove the directory
   "'foo/bar/baz'", and then remove "'foo/bar'" and "'foo'" if they
   are empty. Raises "OSError" if the leaf directory could not be
   successfully removed.

   在 3.6 版更改: 接受一个 *类路径对象*。

os.rename(src, dst, *, src_dir_fd=None, dst_dir_fd=None)

   Rename the file or directory *src* to *dst*.  If *dst* is a
   directory, "OSError" will be raised.  On Unix, if *dst* exists and
   is a file, it will be replaced silently if the user has permission.
   The operation may fail on some Unix flavors if *src* and *dst* are
   on different filesystems.  If successful, the renaming will be an
   atomic operation (this is a POSIX requirement).  On Windows, if
   *dst* already exists, "OSError" will be raised even if it is a
   file.

   This function can support specifying *src_dir_fd* and/or
   *dst_dir_fd* to supply paths relative to directory descriptors.

   If you want cross-platform overwriting of the destination, use
   "replace()".

   3.3 新版功能: The *src_dir_fd* and *dst_dir_fd* arguments.

   在 3.6 版更改: Accepts a *path-like object* for *src* and *dst*.

os.renames(old, new)

   Recursive directory or file renaming function. Works like
   "rename()", except creation of any intermediate directories needed
   to make the new pathname good is attempted first. After the rename,
   directories corresponding to rightmost path segments of the old
   name will be pruned away using "removedirs()".

   注解: This function can fail with the new directory structure
     made if you lack permissions needed to remove the leaf directory
     or file.

   在 3.6 版更改: Accepts a *path-like object* for *old* and *new*.

os.replace(src, dst, *, src_dir_fd=None, dst_dir_fd=None)

   Rename the file or directory *src* to *dst*.  If *dst* is a
   directory, "OSError" will be raised.  If *dst* exists and is a
   file, it will be replaced silently if the user has permission.  The
   operation may fail if *src* and *dst* are on different filesystems.
   If successful, the renaming will be an atomic operation (this is a
   POSIX requirement).

   This function can support specifying *src_dir_fd* and/or
   *dst_dir_fd* to supply paths relative to directory descriptors.

   3.3 新版功能.

   在 3.6 版更改: Accepts a *path-like object* for *src* and *dst*.

os.rmdir(path, *, dir_fd=None)

   Remove (delete) the directory *path*.  Only works when the
   directory is empty, otherwise, "OSError" is raised.  In order to
   remove whole directory trees, "shutil.rmtree()" can be used.

   This function can support paths relative to directory descriptors.

   3.3 新版功能: The *dir_fd* parameter.

   在 3.6 版更改: 接受一个 *类路径对象*。

os.scandir(path='.')

   Return an iterator of "os.DirEntry" objects corresponding to the
   entries in the directory given by *path*. The entries are yielded
   in arbitrary order, and the special entries "'.'" and "'..'" are
   not included.

   Using "scandir()" instead of "listdir()" can significantly increase
   the performance of code that also needs file type or file attribute
   information, because "os.DirEntry" objects expose this information
   if the operating system provides it when scanning a directory. All
   "os.DirEntry" methods may perform a system call, but "is_dir()" and
   "is_file()" usually only require a system call for symbolic links;
   "os.DirEntry.stat()" always requires a system call on Unix but only
   requires one for symbolic links on Windows.

   *path* may be a *path-like object*.  If *path* is of type "bytes"
   (directly or indirectly through the "PathLike" interface), the type
   of the "name" and "path" attributes of each "os.DirEntry" will be
   "bytes"; in all other circumstances, they will be of type "str".

   The "scandir()" iterator supports the *context manager* protocol
   and has the following method:

   scandir.close()

      Close the iterator and free acquired resources.

      This is called automatically when the iterator is exhausted or
      garbage collected, or when an error happens during iterating.
      However it is advisable to call it explicitly or use the "with"
      statement.

      3.6 新版功能.

   The following example shows a simple use of "scandir()" to display
   all the files (excluding directories) in the given *path* that
   don't start with "'.'". The "entry.is_file()" call will generally
   not make an additional system call:

      with os.scandir(path) as it:
          for entry in it:
              if not entry.name.startswith('.') and entry.is_file():
                  print(entry.name)

   注解: On Unix-based systems, "scandir()" uses the system's
     opendir() and readdir() functions. On Windows, it uses the Win32
     FindFirstFileW and FindNextFileW functions.

   3.5 新版功能.

   3.6 新版功能: Added support for the *context manager* protocol and
   the "close()" method.  If a "scandir()" iterator is neither
   exhausted nor explicitly closed a "ResourceWarning" will be emitted
   in its destructor.The function accepts a *path-like object*.

class os.DirEntry

   Object yielded by "scandir()" to expose the file path and other
   file attributes of a directory entry.

   "scandir()" will provide as much of this information as possible
   without making additional system calls. When a "stat()" or
   "lstat()" system call is made, the "os.DirEntry" object will cache
   the result.

   "os.DirEntry" instances are not intended to be stored in long-lived
   data structures; if you know the file metadata has changed or if a
   long time has elapsed since calling "scandir()", call
   "os.stat(entry.path)" to fetch up-to-date information.

   Because the "os.DirEntry" methods can make operating system calls,
   they may also raise "OSError". If you need very fine-grained
   control over errors, you can catch "OSError" when calling one of
   the "os.DirEntry" methods and handle as appropriate.

   To be directly usable as a *path-like object*, "os.DirEntry"
   implements the "PathLike" interface.

   Attributes and methods on a "os.DirEntry" instance are as follows:

   name

      The entry's base filename, relative to the "scandir()" *path*
      argument.

      The "name" attribute will be "bytes" if the "scandir()" *path*
      argument is of type "bytes" and "str" otherwise.  Use
      "fsdecode()" to decode byte filenames.

   path

      The entry's full path name: equivalent to
      "os.path.join(scandir_path, entry.name)" where *scandir_path* is
      the "scandir()" *path* argument.  The path is only absolute if
      the "scandir()" *path* argument was absolute.

      The "path" attribute will be "bytes" if the "scandir()" *path*
      argument is of type "bytes" and "str" otherwise.  Use
      "fsdecode()" to decode byte filenames.

   inode()

      Return the inode number of the entry.

      The result is cached on the "os.DirEntry" object. Use
      "os.stat(entry.path, follow_symlinks=False).st_ino" to fetch up-
      to-date information.

      On the first, uncached call, a system call is required on
      Windows but not on Unix.

   is_dir(*, follow_symlinks=True)

      Return "True" if this entry is a directory or a symbolic link
      pointing to a directory; return "False" if the entry is or
      points to any other kind of file, or if it doesn't exist
      anymore.

      If *follow_symlinks* is "False", return "True" only if this
      entry is a directory (without following symlinks); return
      "False" if the entry is any other kind of file or if it doesn't
      exist anymore.

      The result is cached on the "os.DirEntry" object, with a
      separate cache for *follow_symlinks* "True" and "False". Call
      "os.stat()" along with "stat.S_ISDIR()" to fetch up-to-date
      information.

      On the first, uncached call, no system call is required in most
      cases. Specifically, for non-symlinks, neither Windows or Unix
      require a system call, except on certain Unix file systems, such
      as network file systems, that return "dirent.d_type ==
      DT_UNKNOWN". If the entry is a symlink, a system call will be
      required to follow the symlink unless *follow_symlinks* is
      "False".

      This method can raise "OSError", such as "PermissionError", but
      "FileNotFoundError" is caught and not raised.

   is_file(*, follow_symlinks=True)

      Return "True" if this entry is a file or a symbolic link
      pointing to a file; return "False" if the entry is or points to
      a directory or other non-file entry, or if it doesn't exist
      anymore.

      If *follow_symlinks* is "False", return "True" only if this
      entry is a file (without following symlinks); return "False" if
      the entry is a directory or other non-file entry, or if it
      doesn't exist anymore.

      The result is cached on the "os.DirEntry" object. Caching,
      system calls made, and exceptions raised are as per "is_dir()".

   is_symlink()

      Return "True" if this entry is a symbolic link (even if broken);
      return "False" if the entry points to a directory or any kind of
      file, or if it doesn't exist anymore.

      The result is cached on the "os.DirEntry" object. Call
      "os.path.islink()" to fetch up-to-date information.

      On the first, uncached call, no system call is required in most
      cases. Specifically, neither Windows or Unix require a system
      call, except on certain Unix file systems, such as network file
      systems, that return "dirent.d_type == DT_UNKNOWN".

      This method can raise "OSError", such as "PermissionError", but
      "FileNotFoundError" is caught and not raised.

   stat(*, follow_symlinks=True)

      Return a "stat_result" object for this entry. This method
      follows symbolic links by default; to stat a symbolic link add
      the "follow_symlinks=False" argument.

      On Unix, this method always requires a system call. On Windows,
      it only requires a system call if *follow_symlinks* is "True"
      and the entry is a symbolic link.

      On Windows, the "st_ino", "st_dev" and "st_nlink" attributes of
      the "stat_result" are always set to zero. Call "os.stat()" to
      get these attributes.

      The result is cached on the "os.DirEntry" object, with a
      separate cache for *follow_symlinks* "True" and "False". Call
      "os.stat()" to fetch up-to-date information.

   Note that there is a nice correspondence between several attributes
   and methods of "os.DirEntry" and of "pathlib.Path".  In particular,
   the "name" attribute has the same meaning, as do the "is_dir()",
   "is_file()", "is_symlink()" and "stat()" methods.

   3.5 新版功能.

   在 3.6 版更改: Added support for the "PathLike" interface.  Added
   support for "bytes" paths on Windows.

os.stat(path, *, dir_fd=None, follow_symlinks=True)

   Get the status of a file or a file descriptor. Perform the
   equivalent of a "stat()" system call on the given path. *path* may
   be specified as either a string or bytes -- directly or indirectly
   through the "PathLike" interface -- or as an open file descriptor.
   Return a "stat_result" object.

   This function normally follows symlinks; to stat a symlink add the
   argument "follow_symlinks=False", or use "lstat()".

   This function can support specifying a file descriptor and not
   following symlinks.

   示例:

      >>> import os
      >>> statinfo = os.stat('somefile.txt')
      >>> statinfo
      os.stat_result(st_mode=33188, st_ino=7876932, st_dev=234881026,
      st_nlink=1, st_uid=501, st_gid=501, st_size=264, st_atime=1297230295,
      st_mtime=1297230027, st_ctime=1297230027)
      >>> statinfo.st_size
      264

   参见: "fstat()" and "lstat()" functions.

   3.3 新版功能: Added the *dir_fd* and *follow_symlinks* arguments,
   specifying a file descriptor instead of a path.

   在 3.6 版更改: 接受一个 *类路径对象*。

class os.stat_result

   Object whose attributes correspond roughly to the members of the
   "stat" structure. It is used for the result of "os.stat()",
   "os.fstat()" and "os.lstat()".

   Attributes:

   st_mode

      File mode: file type and file mode bits (permissions).

   st_ino

      Platform dependent, but if non-zero, uniquely identifies the
      file for a given value of "st_dev". Typically:

      * the inode number on Unix,

      * the file index on Windows

   st_dev

      Identifier of the device on which this file resides.

   st_nlink

      Number of hard links.

   st_uid

      User identifier of the file owner.

   st_gid

      Group identifier of the file owner.

   st_size

      Size of the file in bytes, if it is a regular file or a symbolic
      link. The size of a symbolic link is the length of the pathname
      it contains, without a terminating null byte.

   Timestamps:

   st_atime

      Time of most recent access expressed in seconds.

   st_mtime

      Time of most recent content modification expressed in seconds.

   st_ctime

      Platform dependent:

      * the time of most recent metadata change on Unix,

      * the time of creation on Windows, expressed in seconds.

   st_atime_ns

      Time of most recent access expressed in nanoseconds as an
      integer.

   st_mtime_ns

      Time of most recent content modification expressed in
      nanoseconds as an integer.

   st_ctime_ns

      Platform dependent:

      * the time of most recent metadata change on Unix,

      * the time of creation on Windows, expressed in nanoseconds as
        an integer.

   See also the "stat_float_times()" function.

   注解: The exact meaning and resolution of the "st_atime",
     "st_mtime", and "st_ctime" attributes depend on the operating
     system and the file system. For example, on Windows systems using
     the FAT or FAT32 file systems, "st_mtime" has 2-second
     resolution, and "st_atime" has only 1-day resolution.  See your
     operating system documentation for details.Similarly, although
     "st_atime_ns", "st_mtime_ns", and "st_ctime_ns" are always
     expressed in nanoseconds, many systems do not provide nanosecond
     precision. On systems that do provide nanosecond precision, the
     floating- point object used to store "st_atime", "st_mtime", and
     "st_ctime" cannot preserve all of it, and as such will be
     slightly inexact. If you need the exact timestamps you should
     always use "st_atime_ns", "st_mtime_ns", and "st_ctime_ns".

   On some Unix systems (such as Linux), the following attributes may
   also be available:

   st_blocks

      Number of 512-byte blocks allocated for file. This may be
      smaller than "st_size"/512 when the file has holes.

   st_blksize

      "Preferred" blocksize for efficient file system I/O. Writing to
      a file in smaller chunks may cause an inefficient read-modify-
      rewrite.

   st_rdev

      Type of device if an inode device.

   st_flags

      User defined flags for file.

   On other Unix systems (such as FreeBSD), the following attributes
   may be available (but may be only filled out if root tries to use
   them):

   st_gen

      File generation number.

   st_birthtime

      Time of file creation.

   On Mac OS systems, the following attributes may also be available:

   st_rsize

      Real size of the file.

   st_creator

      Creator of the file.

   st_type

      File type.

   On Windows systems, the following attribute is also available:

   st_file_attributes

      Windows file attributes: "dwFileAttributes" member of the
      "BY_HANDLE_FILE_INFORMATION" structure returned by
      "GetFileInformationByHandle()". See the "FILE_ATTRIBUTE_*"
      constants in the "stat" module.

   The standard module "stat" defines functions and constants that are
   useful for extracting information from a "stat" structure. (On
   Windows, some items are filled with dummy values.)

   For backward compatibility, a "stat_result" instance is also
   accessible as a tuple of at least 10 integers giving the most
   important (and portable) members of the "stat" structure, in the
   order "st_mode", "st_ino", "st_dev", "st_nlink", "st_uid",
   "st_gid", "st_size", "st_atime", "st_mtime", "st_ctime". More items
   may be added at the end by some implementations. For compatibility
   with older Python versions, accessing "stat_result" as a tuple
   always returns integers.

   3.3 新版功能: Added the "st_atime_ns", "st_mtime_ns", and
   "st_ctime_ns" members.

   3.5 新版功能: Added the "st_file_attributes" member on Windows.

   在 3.5 版更改: Windows now returns the file index as "st_ino" when
   available.

os.stat_float_times([newvalue])

   Determine whether "stat_result" represents time stamps as float
   objects. If *newvalue* is "True", future calls to "stat()" return
   floats, if it is "False", future calls return ints. If *newvalue*
   is omitted, return the current setting.

   For compatibility with older Python versions, accessing
   "stat_result" as a tuple always returns integers.

   Python now returns float values by default. Applications which do
   not work correctly with floating point time stamps can use this
   function to restore the old behaviour.

   The resolution of the timestamps (that is the smallest possible
   fraction) depends on the system. Some systems only support second
   resolution; on these systems, the fraction will always be zero.

   It is recommended that this setting is only changed at program
   startup time in the *__main__* module; libraries should never
   change this setting. If an application uses a library that works
   incorrectly if floating point time stamps are processed, this
   application should turn the feature off until the library has been
   corrected.

   3.3 版后已移除.

os.statvfs(path)

   Perform a "statvfs()" system call on the given path.  The return
   value is an object whose attributes describe the filesystem on the
   given path, and correspond to the members of the "statvfs"
   structure, namely: "f_bsize", "f_frsize", "f_blocks", "f_bfree",
   "f_bavail", "f_files", "f_ffree", "f_favail", "f_flag",
   "f_namemax".

   Two module-level constants are defined for the "f_flag" attribute's
   bit-flags: if "ST_RDONLY" is set, the filesystem is mounted read-
   only, and if "ST_NOSUID" is set, the semantics of setuid/setgid
   bits are disabled or not supported.

   Additional module-level constants are defined for GNU/glibc based
   systems. These are "ST_NODEV" (disallow access to device special
   files), "ST_NOEXEC" (disallow program execution), "ST_SYNCHRONOUS"
   (writes are synced at once), "ST_MANDLOCK" (allow mandatory locks
   on an FS), "ST_WRITE" (write on file/directory/symlink),
   "ST_APPEND" (append-only file), "ST_IMMUTABLE" (immutable file),
   "ST_NOATIME" (do not update access times), "ST_NODIRATIME" (do not
   update directory access times), "ST_RELATIME" (update atime
   relative to mtime/ctime).

   This function can support specifying a file descriptor.

   Availability: Unix.

   在 3.2 版更改: The "ST_RDONLY" and "ST_NOSUID" constants were
   added.

   3.3 新版功能: Added support for specifying an open file descriptor
   for *path*.

   在 3.4 版更改: The "ST_NODEV", "ST_NOEXEC", "ST_SYNCHRONOUS",
   "ST_MANDLOCK", "ST_WRITE", "ST_APPEND", "ST_IMMUTABLE",
   "ST_NOATIME", "ST_NODIRATIME", and "ST_RELATIME" constants were
   added.

   在 3.6 版更改: 接受一个 *类路径对象*。

os.supports_dir_fd

   A "Set" object indicating which functions in the "os" module permit
   use of their *dir_fd* parameter.  Different platforms provide
   different functionality, and an option that might work on one might
   be unsupported on another.  For consistency's sakes, functions that
   support *dir_fd* always allow specifying the parameter, but will
   raise an exception if the functionality is not actually available.

   To check whether a particular function permits use of its *dir_fd*
   parameter, use the "in" operator on "supports_dir_fd".  As an
   example, this expression determines whether the *dir_fd* parameter
   of "os.stat()" is locally available:

      os.stat in os.supports_dir_fd

   Currently *dir_fd* parameters only work on Unix platforms; none of
   them work on Windows.

   3.3 新版功能.

os.supports_effective_ids

   A "Set" object indicating which functions in the "os" module permit
   use of the *effective_ids* parameter for "os.access()".  If the
   local platform supports it, the collection will contain
   "os.access()", otherwise it will be empty.

   To check whether you can use the *effective_ids* parameter for
   "os.access()", use the "in" operator on "supports_effective_ids",
   like so:

      os.access in os.supports_effective_ids

   Currently *effective_ids* only works on Unix platforms; it does not
   work on Windows.

   3.3 新版功能.

os.supports_fd

   A "Set" object indicating which functions in the "os" module permit
   specifying their *path* parameter as an open file descriptor.
   Different platforms provide different functionality, and an option
   that might work on one might be unsupported on another.  For
   consistency's sakes, functions that support *fd* always allow
   specifying the parameter, but will raise an exception if the
   functionality is not actually available.

   To check whether a particular function permits specifying an open
   file descriptor for its *path* parameter, use the "in" operator on
   "supports_fd". As an example, this expression determines whether
   "os.chdir()" accepts open file descriptors when called on your
   local platform:

      os.chdir in os.supports_fd

   3.3 新版功能.

os.supports_follow_symlinks

   A "Set" object indicating which functions in the "os" module permit
   use of their *follow_symlinks* parameter.  Different platforms
   provide different functionality, and an option that might work on
   one might be unsupported on another.  For consistency's sakes,
   functions that support *follow_symlinks* always allow specifying
   the parameter, but will raise an exception if the functionality is
   not actually available.

   To check whether a particular function permits use of its
   *follow_symlinks* parameter, use the "in" operator on
   "supports_follow_symlinks".  As an example, this expression
   determines whether the *follow_symlinks* parameter of "os.stat()"
   is locally available:

      os.stat in os.supports_follow_symlinks

   3.3 新版功能.

os.symlink(src, dst, target_is_directory=False, *, dir_fd=None)

   Create a symbolic link pointing to *src* named *dst*.

   On Windows, a symlink represents either a file or a directory, and
   does not morph to the target dynamically.  If the target is
   present, the type of the symlink will be created to match.
   Otherwise, the symlink will be created as a directory if
   *target_is_directory* is "True" or a file symlink (the default)
   otherwise.  On non-Windows platforms, *target_is_directory* is
   ignored.

   Symbolic link support was introduced in Windows 6.0 (Vista).
   "symlink()" will raise a "NotImplementedError" on Windows versions
   earlier than 6.0.

   This function can support paths relative to directory descriptors.

   注解: On Windows, the *SeCreateSymbolicLinkPrivilege* is required
     in order to successfully create symlinks. This privilege is not
     typically granted to regular users but is available to accounts
     which can escalate privileges to the administrator level. Either
     obtaining the privilege or running your application as an
     administrator are ways to successfully create symlinks."OSError"
     is raised when the function is called by an unprivileged user.

   Availability: Unix, Windows.

   在 3.2 版更改: Added support for Windows 6.0 (Vista) symbolic
   links.

   3.3 新版功能: Added the *dir_fd* argument, and now allow
   *target_is_directory* on non-Windows platforms.

   在 3.6 版更改: Accepts a *path-like object* for *src* and *dst*.

os.sync()

   Force write of everything to disk.

   Availability: Unix.

   3.3 新版功能.

os.truncate(path, length)

   Truncate the file corresponding to *path*, so that it is at most
   *length* bytes in size.

   This function can support specifying a file descriptor.

   Availability: Unix, Windows.

   3.3 新版功能.

   在 3.5 版更改: 添加了 Windows 支持

   在 3.6 版更改: 接受一个 *类路径对象*。

os.unlink(path, *, dir_fd=None)

   Remove (delete) the file *path*.  This function is semantically
   identical to "remove()"; the "unlink" name is its traditional Unix
   name.  Please see the documentation for "remove()" for further
   information.

   3.3 新版功能: The *dir_fd* parameter.

   在 3.6 版更改: 接受一个 *类路径对象*。

os.utime(path, times=None, *[, ns], dir_fd=None, follow_symlinks=True)

   Set the access and modified times of the file specified by *path*.

   "utime()" takes two optional parameters, *times* and *ns*. These
   specify the times set on *path* and are used as follows:

   * If *ns* is specified, it must be a 2-tuple of the form
     "(atime_ns, mtime_ns)" where each member is an int expressing
     nanoseconds.

   * If *times* is not "None", it must be a 2-tuple of the form
     "(atime, mtime)" where each member is an int or float expressing
     seconds.

   * If *times* is "None" and *ns* is unspecified, this is
     equivalent to specifying "ns=(atime_ns, mtime_ns)" where both
     times are the current time.

   It is an error to specify tuples for both *times* and *ns*.

   Whether a directory can be given for *path* depends on whether the
   operating system implements directories as files (for example,
   Windows does not).  Note that the exact times you set here may not
   be returned by a subsequent "stat()" call, depending on the
   resolution with which your operating system records access and
   modification times; see "stat()".  The best way to preserve exact
   times is to use the *st_atime_ns* and *st_mtime_ns* fields from the
   "os.stat()" result object with the *ns* parameter to *utime*.

   This function can support specifying a file descriptor, paths
   relative to directory descriptors and not following symlinks.

   3.3 新版功能: Added support for specifying an open file descriptor
   for *path*, and the *dir_fd*, *follow_symlinks*, and *ns*
   parameters.

   在 3.6 版更改: 接受一个 *类路径对象*。

os.walk(top, topdown=True, onerror=None, followlinks=False)

   Generate the file names in a directory tree by walking the tree
   either top-down or bottom-up. For each directory in the tree rooted
   at directory *top* (including *top* itself), it yields a 3-tuple
   "(dirpath, dirnames, filenames)".

   *dirpath* is a string, the path to the directory.  *dirnames* is a
   list of the names of the subdirectories in *dirpath* (excluding
   "'.'" and "'..'"). *filenames* is a list of the names of the non-
   directory files in *dirpath*. Note that the names in the lists
   contain no path components.  To get a full path (which begins with
   *top*) to a file or directory in *dirpath*, do
   "os.path.join(dirpath, name)".

   If optional argument *topdown* is "True" or not specified, the
   triple for a directory is generated before the triples for any of
   its subdirectories (directories are generated top-down).  If
   *topdown* is "False", the triple for a directory is generated after
   the triples for all of its subdirectories (directories are
   generated bottom-up). No matter the value of *topdown*, the list of
   subdirectories is retrieved before the tuples for the directory and
   its subdirectories are generated.

   When *topdown* is "True", the caller can modify the *dirnames* list
   in-place (perhaps using "del" or slice assignment), and "walk()"
   will only recurse into the subdirectories whose names remain in
   *dirnames*; this can be used to prune the search, impose a specific
   order of visiting, or even to inform "walk()" about directories the
   caller creates or renames before it resumes "walk()" again.
   Modifying *dirnames* when *topdown* is "False" has no effect on the
   behavior of the walk, because in bottom-up mode the directories in
   *dirnames* are generated before *dirpath* itself is generated.

   By default, errors from the "scandir()" call are ignored.  If
   optional argument *onerror* is specified, it should be a function;
   it will be called with one argument, an "OSError" instance.  It can
   report the error to continue with the walk, or raise the exception
   to abort the walk.  Note that the filename is available as the
   "filename" attribute of the exception object.

   By default, "walk()" will not walk down into symbolic links that
   resolve to directories. Set *followlinks* to "True" to visit
   directories pointed to by symlinks, on systems that support them.

   注解: Be aware that setting *followlinks* to "True" can lead to
     infinite recursion if a link points to a parent directory of
     itself. "walk()" does not keep track of the directories it
     visited already.

   注解: If you pass a relative pathname, don't change the current
     working directory between resumptions of "walk()".  "walk()"
     never changes the current directory, and assumes that its caller
     doesn't either.

   This example displays the number of bytes taken by non-directory
   files in each directory under the starting directory, except that
   it doesn't look under any CVS subdirectory:

      import os
      from os.path import join, getsize
      for root, dirs, files in os.walk('Python/Lib/email'):
          print(root, "consumes", end=" ")
          print(sum(getsize(join(root, name)) for name in files), end=" ")
          print("bytes in", len(files), "non-directory files")
          if 'CVS' in dirs:
              dirs.remove('CVS')  # don't visit CVS directories

   In the next example (simple implementation of "shutil.rmtree()"),
   walking the tree bottom-up is essential, "rmdir()" doesn't allow
   deleting a directory before the directory is empty:

      # Delete everything reachable from the directory named in "top",
      # assuming there are no symbolic links.
      # CAUTION:  This is dangerous!  For example, if top == '/', it
      # could delete all your disk files.
      import os
      for root, dirs, files in os.walk(top, topdown=False):
          for name in files:
              os.remove(os.path.join(root, name))
          for name in dirs:
              os.rmdir(os.path.join(root, name))

   在 3.5 版更改: This function now calls "os.scandir()" instead of
   "os.listdir()", making it faster by reducing the number of calls to
   "os.stat()".

   在 3.6 版更改: 接受一个 *类路径对象*。

os.fwalk(top='.', topdown=True, onerror=None, *, follow_symlinks=False, dir_fd=None)

   This behaves exactly like "walk()", except that it yields a 4-tuple
   "(dirpath, dirnames, filenames, dirfd)", and it supports "dir_fd".

   *dirpath*, *dirnames* and *filenames* are identical to "walk()"
   output, and *dirfd* is a file descriptor referring to the directory
   *dirpath*.

   This function always supports paths relative to directory
   descriptors and not following symlinks.  Note however that, unlike
   other functions, the "fwalk()" default value for *follow_symlinks*
   is "False".

   注解: Since "fwalk()" yields file descriptors, those are only
     valid until the next iteration step, so you should duplicate them
     (e.g. with "dup()") if you want to keep them longer.

   This example displays the number of bytes taken by non-directory
   files in each directory under the starting directory, except that
   it doesn't look under any CVS subdirectory:

      import os
      for root, dirs, files, rootfd in os.fwalk('Python/Lib/email'):
          print(root, "consumes", end="")
          print(sum([os.stat(name, dir_fd=rootfd).st_size for name in files]),
                end="")
          print("bytes in", len(files), "non-directory files")
          if 'CVS' in dirs:
              dirs.remove('CVS')  # don't visit CVS directories

   In the next example, walking the tree bottom-up is essential:
   "rmdir()" doesn't allow deleting a directory before the directory
   is empty:

      # Delete everything reachable from the directory named in "top",
      # assuming there are no symbolic links.
      # CAUTION:  This is dangerous!  For example, if top == '/', it
      # could delete all your disk files.
      import os
      for root, dirs, files, rootfd in os.fwalk(top, topdown=False):
          for name in files:
              os.unlink(name, dir_fd=rootfd)
          for name in dirs:
              os.rmdir(name, dir_fd=rootfd)

   Availability: Unix.

   3.3 新版功能.

   在 3.6 版更改: 接受一个 *类路径对象*。


16.1.5.1. Linux extended attributes
-----------------------------------

3.3 新版功能.

These functions are all available on Linux only.

os.getxattr(path, attribute, *, follow_symlinks=True)

   Return the value of the extended filesystem attribute *attribute*
   for *path*. *attribute* can be bytes or str (directly or indirectly
   through the "PathLike" interface). If it is str, it is encoded with
   the filesystem encoding.

   This function can support specifying a file descriptor and not
   following symlinks.

   在 3.6 版更改: Accepts a *path-like object* for *path* and
   *attribute*.

os.listxattr(path=None, *, follow_symlinks=True)

   Return a list of the extended filesystem attributes on *path*.  The
   attributes in the list are represented as strings decoded with the
   filesystem encoding.  If *path* is "None", "listxattr()" will
   examine the current directory.

   This function can support specifying a file descriptor and not
   following symlinks.

   在 3.6 版更改: 接受一个 *类路径对象*。

os.removexattr(path, attribute, *, follow_symlinks=True)

   Removes the extended filesystem attribute *attribute* from *path*.
   *attribute* should be bytes or str (directly or indirectly through
   the "PathLike" interface). If it is a string, it is encoded with
   the filesystem encoding.

   This function can support specifying a file descriptor and not
   following symlinks.

   在 3.6 版更改: Accepts a *path-like object* for *path* and
   *attribute*.

os.setxattr(path, attribute, value, flags=0, *, follow_symlinks=True)

   Set the extended filesystem attribute *attribute* on *path* to
   *value*. *attribute* must be a bytes or str with no embedded NULs
   (directly or indirectly through the "PathLike" interface). If it is
   a str, it is encoded with the filesystem encoding.  *flags* may be
   "XATTR_REPLACE" or "XATTR_CREATE". If "XATTR_REPLACE" is given and
   the attribute does not exist, "EEXISTS" will be raised. If
   "XATTR_CREATE" is given and the attribute already exists, the
   attribute will not be created and "ENODATA" will be raised.

   This function can support specifying a file descriptor and not
   following symlinks.

   注解: A bug in Linux kernel versions less than 2.6.39 caused the
     flags argument to be ignored on some filesystems.

   在 3.6 版更改: Accepts a *path-like object* for *path* and
   *attribute*.

os.XATTR_SIZE_MAX

   The maximum size the value of an extended attribute can be.
   Currently, this is 64 KiB on Linux.

os.XATTR_CREATE

   This is a possible value for the flags argument in "setxattr()". It
   indicates the operation must create an attribute.

os.XATTR_REPLACE

   This is a possible value for the flags argument in "setxattr()". It
   indicates the operation must replace an existing attribute.


16.1.6. Process Management
==========================

These functions may be used to create and manage processes.

The various "exec*" functions take a list of arguments for the new
program loaded into the process.  In each case, the first of these
arguments is passed to the new program as its own name rather than as
an argument a user may have typed on a command line.  For the C
programmer, this is the "argv[0]" passed to a program's "main()".  For
example, "os.execv('/bin/echo', ['foo', 'bar'])" will only print "bar"
on standard output; "foo" will seem to be ignored.

os.abort()

   Generate a "SIGABRT" signal to the current process.  On Unix, the
   default behavior is to produce a core dump; on Windows, the process
   immediately returns an exit code of "3".  Be aware that calling
   this function will not call the Python signal handler registered
   for "SIGABRT" with "signal.signal()".

os.execl(path, arg0, arg1, ...)
os.execle(path, arg0, arg1, ..., env)
os.execlp(file, arg0, arg1, ...)
os.execlpe(file, arg0, arg1, ..., env)
os.execv(path, args)
os.execve(path, args, env)
os.execvp(file, args)
os.execvpe(file, args, env)

   These functions all execute a new program, replacing the current
   process; they do not return.  On Unix, the new executable is loaded
   into the current process, and will have the same process id as the
   caller.  Errors will be reported as "OSError" exceptions.

   The current process is replaced immediately. Open file objects and
   descriptors are not flushed, so if there may be data buffered on
   these open files, you should flush them using "sys.stdout.flush()"
   or "os.fsync()" before calling an "exec*" function.

   The "l" and "v" variants of the "exec*" functions differ in how
   command-line arguments are passed.  The "l" variants are perhaps
   the easiest to work with if the number of parameters is fixed when
   the code is written; the individual parameters simply become
   additional parameters to the "execl*()" functions.  The "v"
   variants are good when the number of parameters is variable, with
   the arguments being passed in a list or tuple as the *args*
   parameter.  In either case, the arguments to the child process
   should start with the name of the command being run, but this is
   not enforced.

   The variants which include a "p" near the end ("execlp()",
   "execlpe()", "execvp()", and "execvpe()") will use the "PATH"
   environment variable to locate the program *file*.  When the
   environment is being replaced (using one of the "exec*e" variants,
   discussed in the next paragraph), the new environment is used as
   the source of the "PATH" variable. The other variants, "execl()",
   "execle()", "execv()", and "execve()", will not use the "PATH"
   variable to locate the executable; *path* must contain an
   appropriate absolute or relative path.

   For "execle()", "execlpe()", "execve()", and "execvpe()" (note that
   these all end in "e"), the *env* parameter must be a mapping which
   is used to define the environment variables for the new process
   (these are used instead of the current process' environment); the
   functions "execl()", "execlp()", "execv()", and "execvp()" all
   cause the new process to inherit the environment of the current
   process.

   For "execve()" on some platforms, *path* may also be specified as
   an open file descriptor.  This functionality may not be supported
   on your platform; you can check whether or not it is available
   using "os.supports_fd". If it is unavailable, using it will raise a
   "NotImplementedError".

   Availability: Unix, Windows.

   3.3 新版功能: Added support for specifying an open file descriptor
   for *path* for "execve()".

   在 3.6 版更改: 接受一个 *类路径对象*。

os._exit(n)

   Exit the process with status *n*, without calling cleanup handlers,
   flushing stdio buffers, etc.

   注解: The standard way to exit is "sys.exit(n)".  "_exit()"
     should normally only be used in the child process after a
     "fork()".

The following exit codes are defined and can be used with "_exit()",
although they are not required.  These are typically used for system
programs written in Python, such as a mail server's external command
delivery program.

注解: Some of these may not be available on all Unix platforms,
  since there is some variation.  These constants are defined where
  they are defined by the underlying platform.

os.EX_OK

   Exit code that means no error occurred.

   Availability: Unix.

os.EX_USAGE

   Exit code that means the command was used incorrectly, such as when
   the wrong number of arguments are given.

   Availability: Unix.

os.EX_DATAERR

   Exit code that means the input data was incorrect.

   Availability: Unix.

os.EX_NOINPUT

   Exit code that means an input file did not exist or was not
   readable.

   Availability: Unix.

os.EX_NOUSER

   Exit code that means a specified user did not exist.

   Availability: Unix.

os.EX_NOHOST

   Exit code that means a specified host did not exist.

   Availability: Unix.

os.EX_UNAVAILABLE

   Exit code that means that a required service is unavailable.

   Availability: Unix.

os.EX_SOFTWARE

   Exit code that means an internal software error was detected.

   Availability: Unix.

os.EX_OSERR

   Exit code that means an operating system error was detected, such
   as the inability to fork or create a pipe.

   Availability: Unix.

os.EX_OSFILE

   Exit code that means some system file did not exist, could not be
   opened, or had some other kind of error.

   Availability: Unix.

os.EX_CANTCREAT

   Exit code that means a user specified output file could not be
   created.

   Availability: Unix.

os.EX_IOERR

   Exit code that means that an error occurred while doing I/O on some
   file.

   Availability: Unix.

os.EX_TEMPFAIL

   Exit code that means a temporary failure occurred.  This indicates
   something that may not really be an error, such as a network
   connection that couldn't be made during a retryable operation.

   Availability: Unix.

os.EX_PROTOCOL

   Exit code that means that a protocol exchange was illegal, invalid,
   or not understood.

   Availability: Unix.

os.EX_NOPERM

   Exit code that means that there were insufficient permissions to
   perform the operation (but not intended for file system problems).

   Availability: Unix.

os.EX_CONFIG

   Exit code that means that some kind of configuration error
   occurred.

   Availability: Unix.

os.EX_NOTFOUND

   Exit code that means something like "an entry was not found".

   Availability: Unix.

os.fork()

   Fork a child process.  Return "0" in the child and the child's
   process id in the parent.  If an error occurs "OSError" is raised.

   Note that some platforms including FreeBSD <= 6.3 and Cygwin have
   known issues when using fork() from a thread.

   警告: See "ssl" for applications that use the SSL module with
     fork().

   Availability: Unix.

os.forkpty()

   Fork a child process, using a new pseudo-terminal as the child's
   controlling terminal. Return a pair of "(pid, fd)", where *pid* is
   "0" in the child, the new child's process id in the parent, and
   *fd* is the file descriptor of the master end of the pseudo-
   terminal.  For a more portable approach, use the "pty" module.  If
   an error occurs "OSError" is raised.

   Availability: some flavors of Unix.

os.kill(pid, sig)

   Send signal *sig* to the process *pid*.  Constants for the specific
   signals available on the host platform are defined in the "signal"
   module.

   Windows: The "signal.CTRL_C_EVENT" and "signal.CTRL_BREAK_EVENT"
   signals are special signals which can only be sent to console
   processes which share a common console window, e.g., some
   subprocesses. Any other value for *sig* will cause the process to
   be unconditionally killed by the TerminateProcess API, and the exit
   code will be set to *sig*. The Windows version of "kill()"
   additionally takes process handles to be killed.

   See also "signal.pthread_kill()".

   3.2 新版功能: Windows support.

os.killpg(pgid, sig)

   Send the signal *sig* to the process group *pgid*.

   Availability: Unix.

os.nice(increment)

   Add *increment* to the process's "niceness".  Return the new
   niceness.

   Availability: Unix.

os.plock(op)

   Lock program segments into memory.  The value of *op* (defined in
   "<sys/lock.h>") determines which segments are locked.

   Availability: Unix.

os.popen(cmd, mode='r', buffering=-1)

   Open a pipe to or from command *cmd*. The return value is an open
   file object connected to the pipe, which can be read or written
   depending on whether *mode* is "'r'" (default) or "'w'". The
   *buffering* argument has the same meaning as the corresponding
   argument to the built-in "open()" function. The returned file
   object reads or writes text strings rather than bytes.

   The "close" method returns "None" if the subprocess exited
   successfully, or the subprocess's return code if there was an
   error. On POSIX systems, if the return code is positive it
   represents the return value of the process left-shifted by one
   byte.  If the return code is negative, the process was terminated
   by the signal given by the negated value of the return code.  (For
   example, the return value might be "- signal.SIGKILL" if the
   subprocess was killed.)  On Windows systems, the return value
   contains the signed integer return code from the child process.

   This is implemented using "subprocess.Popen"; see that class's
   documentation for more powerful ways to manage and communicate with
   subprocesses.

os.spawnl(mode, path, ...)
os.spawnle(mode, path, ..., env)
os.spawnlp(mode, file, ...)
os.spawnlpe(mode, file, ..., env)
os.spawnv(mode, path, args)
os.spawnve(mode, path, args, env)
os.spawnvp(mode, file, args)
os.spawnvpe(mode, file, args, env)

   Execute the program *path* in a new process.

   (Note that the "subprocess" module provides more powerful
   facilities for spawning new processes and retrieving their results;
   using that module is preferable to using these functions.  Check
   especially the Replacing Older Functions with the subprocess Module
   section.)

   If *mode* is "P_NOWAIT", this function returns the process id of
   the new process; if *mode* is "P_WAIT", returns the process's exit
   code if it exits normally, or "-signal", where *signal* is the
   signal that killed the process.  On Windows, the process id will
   actually be the process handle, so can be used with the "waitpid()"
   function.

   The "l" and "v" variants of the "spawn*" functions differ in how
   command-line arguments are passed.  The "l" variants are perhaps
   the easiest to work with if the number of parameters is fixed when
   the code is written; the individual parameters simply become
   additional parameters to the "spawnl*()" functions.  The "v"
   variants are good when the number of parameters is variable, with
   the arguments being passed in a list or tuple as the *args*
   parameter.  In either case, the arguments to the child process must
   start with the name of the command being run.

   The variants which include a second "p" near the end ("spawnlp()",
   "spawnlpe()", "spawnvp()", and "spawnvpe()") will use the "PATH"
   environment variable to locate the program *file*.  When the
   environment is being replaced (using one of the "spawn*e" variants,
   discussed in the next paragraph), the new environment is used as
   the source of the "PATH" variable.  The other variants, "spawnl()",
   "spawnle()", "spawnv()", and "spawnve()", will not use the "PATH"
   variable to locate the executable; *path* must contain an
   appropriate absolute or relative path.

   For "spawnle()", "spawnlpe()", "spawnve()", and "spawnvpe()" (note
   that these all end in "e"), the *env* parameter must be a mapping
   which is used to define the environment variables for the new
   process (they are used instead of the current process'
   environment); the functions "spawnl()", "spawnlp()", "spawnv()",
   and "spawnvp()" all cause the new process to inherit the
   environment of the current process.  Note that keys and values in
   the *env* dictionary must be strings; invalid keys or values will
   cause the function to fail, with a return value of "127".

   As an example, the following calls to "spawnlp()" and "spawnvpe()"
   are equivalent:

      import os
      os.spawnlp(os.P_WAIT, 'cp', 'cp', 'index.html', '/dev/null')

      L = ['cp', 'index.html', '/dev/null']
      os.spawnvpe(os.P_WAIT, 'cp', L, os.environ)

   Availability: Unix, Windows.  "spawnlp()", "spawnlpe()",
   "spawnvp()" and "spawnvpe()" are not available on Windows.
   "spawnle()" and "spawnve()" are not thread-safe on Windows; we
   advise you to use the "subprocess" module instead.

   在 3.6 版更改: 接受一个 *类路径对象*。

os.P_NOWAIT
os.P_NOWAITO

   Possible values for the *mode* parameter to the "spawn*" family of
   functions.  If either of these values is given, the "spawn*()"
   functions will return as soon as the new process has been created,
   with the process id as the return value.

   Availability: Unix, Windows.

os.P_WAIT

   Possible value for the *mode* parameter to the "spawn*" family of
   functions.  If this is given as *mode*, the "spawn*()" functions
   will not return until the new process has run to completion and
   will return the exit code of the process the run is successful, or
   "-signal" if a signal kills the process.

   Availability: Unix, Windows.

os.P_DETACH
os.P_OVERLAY

   Possible values for the *mode* parameter to the "spawn*" family of
   functions.  These are less portable than those listed above.
   "P_DETACH" is similar to "P_NOWAIT", but the new process is
   detached from the console of the calling process. If "P_OVERLAY" is
   used, the current process will be replaced; the "spawn*" function
   will not return.

   Availability: Windows.

os.startfile(path[, operation])

   Start a file with its associated application.

   When *operation* is not specified or "'open'", this acts like
   double-clicking the file in Windows Explorer, or giving the file
   name as an argument to the **start** command from the interactive
   command shell: the file is opened with whatever application (if
   any) its extension is associated.

   When another *operation* is given, it must be a "command verb" that
   specifies what should be done with the file. Common verbs
   documented by Microsoft are "'print'" and  "'edit'" (to be used on
   files) as well as "'explore'" and "'find'" (to be used on
   directories).

   "startfile()" returns as soon as the associated application is
   launched. There is no option to wait for the application to close,
   and no way to retrieve the application's exit status.  The *path*
   parameter is relative to the current directory.  If you want to use
   an absolute path, make sure the first character is not a slash
   ("'/'"); the underlying Win32 "ShellExecute()" function doesn't
   work if it is.  Use the "os.path.normpath()" function to ensure
   that the path is properly encoded for Win32.

   To reduce interpreter startup overhead, the Win32 "ShellExecute()"
   function is not resolved until this function is first called.  If
   the function cannot be resolved, "NotImplementedError" will be
   raised.

   Availability: Windows.

os.system(command)

   Execute the command (a string) in a subshell.  This is implemented
   by calling the Standard C function "system()", and has the same
   limitations. Changes to "sys.stdin", etc. are not reflected in the
   environment of the executed command. If *command* generates any
   output, it will be sent to the interpreter standard output stream.

   On Unix, the return value is the exit status of the process encoded
   in the format specified for "wait()".  Note that POSIX does not
   specify the meaning of the return value of the C "system()"
   function, so the return value of the Python function is system-
   dependent.

   On Windows, the return value is that returned by the system shell
   after running *command*.  The shell is given by the Windows
   environment variable "COMSPEC": it is usually **cmd.exe**, which
   returns the exit status of the command run; on systems using a non-
   native shell, consult your shell documentation.

   The "subprocess" module provides more powerful facilities for
   spawning new processes and retrieving their results; using that
   module is preferable to using this function.  See the Replacing
   Older Functions with the subprocess Module section in the
   "subprocess" documentation for some helpful recipes.

   Availability: Unix, Windows.

os.times()

   Returns the current global process times. The return value is an
   object with five attributes:

   * "user" - user time

   * "system" - system time

   * "children_user" - user time of all child processes

   * "children_system" - system time of all child processes

   * "elapsed" - elapsed real time since a fixed point in the past

   For backwards compatibility, this object also behaves like a five-
   tuple containing "user", "system", "children_user",
   "children_system", and "elapsed" in that order.

   See the Unix manual page *times(2)* or the corresponding Windows
   Platform API documentation. On Windows, only "user" and "system"
   are known; the other attributes are zero.

   Availability: Unix, Windows.

   在 3.3 版更改: 返回结果的类型由元组变成一个类似元组的对象，同时具有
   命名的属性。

os.wait()

   Wait for completion of a child process, and return a tuple
   containing its pid and exit status indication: a 16-bit number,
   whose low byte is the signal number that killed the process, and
   whose high byte is the exit status (if the signal number is zero);
   the high bit of the low byte is set if a core file was produced.

   Availability: Unix.

os.waitid(idtype, id, options)

   Wait for the completion of one or more child processes. *idtype*
   can be "P_PID", "P_PGID" or "P_ALL". *id* specifies the pid to wait
   on. *options* is constructed from the ORing of one or more of
   "WEXITED", "WSTOPPED" or "WCONTINUED" and additionally may be ORed
   with "WNOHANG" or "WNOWAIT". The return value is an object
   representing the data contained in the "siginfo_t" structure,
   namely: "si_pid", "si_uid", "si_signo", "si_status", "si_code" or
   "None" if "WNOHANG" is specified and there are no children in a
   waitable state.

   Availability: Unix.

   3.3 新版功能.

os.P_PID
os.P_PGID
os.P_ALL

   These are the possible values for *idtype* in "waitid()". They
   affect how *id* is interpreted.

   Availability: Unix.

   3.3 新版功能.

os.WEXITED
os.WSTOPPED
os.WNOWAIT

   Flags that can be used in *options* in "waitid()" that specify what
   child signal to wait for.

   Availability: Unix.

   3.3 新版功能.

os.CLD_EXITED
os.CLD_DUMPED
os.CLD_TRAPPED
os.CLD_CONTINUED

   These are the possible values for "si_code" in the result returned
   by "waitid()".

   Availability: Unix.

   3.3 新版功能.

os.waitpid(pid, options)

   The details of this function differ on Unix and Windows.

   On Unix: Wait for completion of a child process given by process id
   *pid*, and return a tuple containing its process id and exit status
   indication (encoded as for "wait()").  The semantics of the call
   are affected by the value of the integer *options*, which should be
   "0" for normal operation.

   If *pid* is greater than "0", "waitpid()" requests status
   information for that specific process.  If *pid* is "0", the
   request is for the status of any child in the process group of the
   current process.  If *pid* is "-1", the request pertains to any
   child of the current process.  If *pid* is less than "-1", status
   is requested for any process in the process group "-pid" (the
   absolute value of *pid*).

   An "OSError" is raised with the value of errno when the syscall
   returns -1.

   On Windows: Wait for completion of a process given by process
   handle *pid*, and return a tuple containing *pid*, and its exit
   status shifted left by 8 bits (shifting makes cross-platform use of
   the function easier). A *pid* less than or equal to "0" has no
   special meaning on Windows, and raises an exception. The value of
   integer *options* has no effect. *pid* can refer to any process
   whose id is known, not necessarily a child process. The "spawn*"
   functions called with "P_NOWAIT" return suitable process handles.

   在 3.5 版更改: 如果系统调用被中断，但信号处理程序没有触发异常，此函
   数现在会重试系统调用，而不是触发 "InterruptedError" 异常 (原因详见
   **PEP 475**)。

os.wait3(options)

   Similar to "waitpid()", except no process id argument is given and
   a 3-element tuple containing the child's process id, exit status
   indication, and resource usage information is returned.  Refer to
   "resource"."getrusage()" for details on resource usage information.
   The option argument is the same as that provided to "waitpid()" and
   "wait4()".

   Availability: Unix.

os.wait4(pid, options)

   Similar to "waitpid()", except a 3-element tuple, containing the
   child's process id, exit status indication, and resource usage
   information is returned. Refer to "resource"."getrusage()" for
   details on resource usage information.  The arguments to "wait4()"
   are the same as those provided to "waitpid()".

   Availability: Unix.

os.WNOHANG

   The option for "waitpid()" to return immediately if no child
   process status is available immediately. The function returns "(0,
   0)" in this case.

   Availability: Unix.

os.WCONTINUED

   This option causes child processes to be reported if they have been
   continued from a job control stop since their status was last
   reported.

   Availability: some Unix systems.

os.WUNTRACED

   This option causes child processes to be reported if they have been
   stopped but their current state has not been reported since they
   were stopped.

   Availability: Unix.

The following functions take a process status code as returned by
"system()", "wait()", or "waitpid()" as a parameter.  They may be used
to determine the disposition of a process.

os.WCOREDUMP(status)

   Return "True" if a core dump was generated for the process,
   otherwise return "False".

   Availability: Unix.

os.WIFCONTINUED(status)

   Return "True" if the process has been continued from a job control
   stop, otherwise return "False".

   Availability: Unix.

os.WIFSTOPPED(status)

   Return "True" if the process has been stopped, otherwise return
   "False".

   Availability: Unix.

os.WIFSIGNALED(status)

   Return "True" if the process exited due to a signal, otherwise
   return "False".

   Availability: Unix.

os.WIFEXITED(status)

   Return "True" if the process exited using the *exit(2)* system
   call, otherwise return "False".

   Availability: Unix.

os.WEXITSTATUS(status)

   If "WIFEXITED(status)" is true, return the integer parameter to the
   *exit(2)* system call.  Otherwise, the return value is meaningless.

   Availability: Unix.

os.WSTOPSIG(status)

   Return the signal which caused the process to stop.

   Availability: Unix.

os.WTERMSIG(status)

   Return the signal which caused the process to exit.

   Availability: Unix.


16.1.7. Interface to the scheduler
==================================

These functions control how a process is allocated CPU time by the
operating system. They are only available on some Unix platforms. For
more detailed information, consult your Unix manpages.

3.3 新版功能.

The following scheduling policies are exposed if they are supported by
the operating system.

os.SCHED_OTHER

   The default scheduling policy.

os.SCHED_BATCH

   Scheduling policy for CPU-intensive processes that tries to
   preserve interactivity on the rest of the computer.

os.SCHED_IDLE

   Scheduling policy for extremely low priority background tasks.

os.SCHED_SPORADIC

   Scheduling policy for sporadic server programs.

os.SCHED_FIFO

   A First In First Out scheduling policy.

os.SCHED_RR

   A round-robin scheduling policy.

os.SCHED_RESET_ON_FORK

   This flag can be OR'ed with any other scheduling policy. When a
   process with this flag set forks, its child's scheduling policy and
   priority are reset to the default.

class os.sched_param(sched_priority)

   This class represents tunable scheduling parameters used in
   "sched_setparam()", "sched_setscheduler()", and "sched_getparam()".
   It is immutable.

   At the moment, there is only one possible parameter:

   sched_priority

      The scheduling priority for a scheduling policy.

os.sched_get_priority_min(policy)

   Get the minimum priority value for *policy*. *policy* is one of the
   scheduling policy constants above.

os.sched_get_priority_max(policy)

   Get the maximum priority value for *policy*. *policy* is one of the
   scheduling policy constants above.

os.sched_setscheduler(pid, policy, param)

   Set the scheduling policy for the process with PID *pid*. A *pid*
   of 0 means the calling process. *policy* is one of the scheduling
   policy constants above. *param* is a "sched_param" instance.

os.sched_getscheduler(pid)

   Return the scheduling policy for the process with PID *pid*. A
   *pid* of 0 means the calling process. The result is one of the
   scheduling policy constants above.

os.sched_setparam(pid, param)

   Set a scheduling parameters for the process with PID *pid*. A *pid*
   of 0 means the calling process. *param* is a "sched_param"
   instance.

os.sched_getparam(pid)

   Return the scheduling parameters as a "sched_param" instance for
   the process with PID *pid*. A *pid* of 0 means the calling process.

os.sched_rr_get_interval(pid)

   Return the round-robin quantum in seconds for the process with PID
   *pid*. A *pid* of 0 means the calling process.

os.sched_yield()

   Voluntarily relinquish the CPU.

os.sched_setaffinity(pid, mask)

   Restrict the process with PID *pid* (or the current process if
   zero) to a set of CPUs.  *mask* is an iterable of integers
   representing the set of CPUs to which the process should be
   restricted.

os.sched_getaffinity(pid)

   Return the set of CPUs the process with PID *pid* (or the current
   process if zero) is restricted to.


16.1.8. Miscellaneous System Information
========================================

os.confstr(name)

   Return string-valued system configuration values. *name* specifies
   the configuration value to retrieve; it may be a string which is
   the name of a defined system value; these names are specified in a
   number of standards (POSIX, Unix 95, Unix 98, and others).  Some
   platforms define additional names as well. The names known to the
   host operating system are given as the keys of the "confstr_names"
   dictionary.  For configuration variables not included in that
   mapping, passing an integer for *name* is also accepted.

   If the configuration value specified by *name* isn't defined,
   "None" is returned.

   If *name* is a string and is not known, "ValueError" is raised.  If
   a specific value for *name* is not supported by the host system,
   even if it is included in "confstr_names", an "OSError" is raised
   with "errno.EINVAL" for the error number.

   Availability: Unix.

os.confstr_names

   Dictionary mapping names accepted by "confstr()" to the integer
   values defined for those names by the host operating system. This
   can be used to determine the set of names known to the system.

   Availability: Unix.

os.cpu_count()

   Return the number of CPUs in the system. Returns "None" if
   undetermined.

   该数量不同于当前进程可以使用的 CPU 数量。可用的 CPU 数量可以由
   "len(os.sched_getaffinity(0))" 方法获得。

   3.4 新版功能.

os.getloadavg()

   Return the number of processes in the system run queue averaged
   over the last 1, 5, and 15 minutes or raises "OSError" if the load
   average was unobtainable.

   Availability: Unix.

os.sysconf(name)

   Return integer-valued system configuration values. If the
   configuration value specified by *name* isn't defined, "-1" is
   returned.  The comments regarding the *name* parameter for
   "confstr()" apply here as well; the dictionary that provides
   information on the known names is given by "sysconf_names".

   Availability: Unix.

os.sysconf_names

   Dictionary mapping names accepted by "sysconf()" to the integer
   values defined for those names by the host operating system. This
   can be used to determine the set of names known to the system.

   Availability: Unix.

The following data values are used to support path manipulation
operations.  These are defined for all platforms.

Higher-level operations on pathnames are defined in the "os.path"
module.

os.curdir

   The constant string used by the operating system to refer to the
   current directory. This is "'.'" for Windows and POSIX. Also
   available via "os.path".

os.pardir

   The constant string used by the operating system to refer to the
   parent directory. This is "'..'" for Windows and POSIX. Also
   available via "os.path".

os.sep

   The character used by the operating system to separate pathname
   components. This is "'/'" for POSIX and "'\\'" for Windows.  Note
   that knowing this is not sufficient to be able to parse or
   concatenate pathnames --- use "os.path.split()" and
   "os.path.join()" --- but it is occasionally useful. Also available
   via "os.path".

os.altsep

   An alternative character used by the operating system to separate
   pathname components, or "None" if only one separator character
   exists.  This is set to "'/'" on Windows systems where "sep" is a
   backslash. Also available via "os.path".

os.extsep

   The character which separates the base filename from the extension;
   for example, the "'.'" in "os.py". Also available via "os.path".

os.pathsep

   The character conventionally used by the operating system to
   separate search path components (as in "PATH"), such as "':'" for
   POSIX or "';'" for Windows. Also available via "os.path".

os.defpath

   The default search path used by "exec*p*" and "spawn*p*" if the
   environment doesn't have a "'PATH'" key. Also available via
   "os.path".

os.linesep

   The string used to separate (or, rather, terminate) lines on the
   current platform.  This may be a single character, such as "'\n'"
   for POSIX, or multiple characters, for example, "'\r\n'" for
   Windows. Do not use *os.linesep* as a line terminator when writing
   files opened in text mode (the default); use a single "'\n'"
   instead, on all platforms.

os.devnull

   The file path of the null device. For example: "'/dev/null'" for
   POSIX, "'nul'" for Windows.  Also available via "os.path".

os.RTLD_LAZY
os.RTLD_NOW
os.RTLD_GLOBAL
os.RTLD_LOCAL
os.RTLD_NODELETE
os.RTLD_NOLOAD
os.RTLD_DEEPBIND

   Flags for use with the "setdlopenflags()" and "getdlopenflags()"
   functions.  See the Unix manual page *dlopen(3)* for what the
   different flags mean.

   3.3 新版功能.


16.1.9. Random numbers
======================

os.getrandom(size, flags=0)

   Get up to *size* random bytes. The function can return less bytes
   than requested.

   These bytes can be used to seed user-space random number generators
   or for cryptographic purposes.

   "getrandom()" relies on entropy gathered from device drivers and
   other sources of environmental noise. Unnecessarily reading large
   quantities of data will have a negative impact on  other users  of
   the "/dev/random" and "/dev/urandom" devices.

   The flags argument is a bit mask that can contain zero or more of
   the following values ORed together: "os.GRND_RANDOM" and
   "GRND_NONBLOCK".

   See also the Linux getrandom() manual page.

   Availability: Linux 3.17 and newer.

   3.6 新版功能.

os.urandom(size)

   Return a string of *size* random bytes suitable for cryptographic
   use.

   This function returns random bytes from an OS-specific randomness
   source.  The returned data should be unpredictable enough for
   cryptographic applications, though its exact quality depends on the
   OS implementation.

   On Linux, if the "getrandom()" syscall is available, it is used in
   blocking mode: block until the system urandom entropy pool is
   initialized (128 bits of entropy are collected by the kernel). See
   the **PEP 524** for the rationale. On Linux, the "getrandom()"
   function can be used to get random bytes in non-blocking mode
   (using the "GRND_NONBLOCK" flag) or to poll until the system
   urandom entropy pool is initialized.

   On a Unix-like system, random bytes are read from the
   "/dev/urandom" device. If the "/dev/urandom" device is not
   available or not readable, the "NotImplementedError" exception is
   raised.

   On Windows, it will use "CryptGenRandom()".

   参见: The "secrets" module provides higher level functions. For
     an easy-to-use interface to the random number generator provided
     by your platform, please see "random.SystemRandom".

   在 3.6.0 版更改: On Linux, "getrandom()" is now used in blocking
   mode to increase the security.

   在 3.5.2 版更改: On Linux, if the "getrandom()" syscall blocks (the
   urandom entropy pool is not initialized yet), fall back on reading
   "/dev/urandom".

   在 3.5 版更改: On Linux 3.17 and newer, the "getrandom()" syscall
   is now used when available.  On OpenBSD 5.6 and newer, the C
   "getentropy()" function is now used. These functions avoid the
   usage of an internal file descriptor.

os.GRND_NONBLOCK

   By  default, when reading from "/dev/random", "getrandom()" blocks
   if no random bytes are available, and when reading from
   "/dev/urandom", it blocks if the entropy pool has not yet been
   initialized.

   If the "GRND_NONBLOCK" flag is set, then "getrandom()" does not
   block in these cases, but instead immediately raises
   "BlockingIOError".

   3.6 新版功能.

os.GRND_RANDOM

   If  this  bit  is  set,  then  random bytes are drawn from the
   "/dev/random" pool instead of the "/dev/urandom" pool.

   3.6 新版功能.
