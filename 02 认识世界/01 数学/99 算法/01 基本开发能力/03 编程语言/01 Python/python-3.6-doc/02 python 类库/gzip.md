---
title: gzip
toc: true
date: 2019-06-30
---
13.2. "gzip" --- 对 **gzip** 格式的支持
***************************************

**源代码：** Lib/gzip.py

======================================================================

此模块提供的简单接口帮助用户压缩和解压缩文件，功能类似于 GNU 应用程序
**gzip** 和 **gunzip**。

数据压缩由 "zlib" 模块提供。

"gzip" 模块提供 "GzipFile" 类和 "open()"、"compress()"、"decompress()"
几个便利的函数。"GzipFile" 类可以读写 **gzip** 格式的文件，还能自动压
缩和解压缩数据，这让操作压缩文件如同操作普通的 *file object* 一样方便
。

注意，此模块不支持部分可以被 **gzip** 和 **gunzip** 解压的格式，如利用
**compress** 或 **pack** 压缩所得的文件。

这个模块定义了以下内容：

gzip.open(filename, mode='rb', compresslevel=9, encoding=None, errors=None, newline=None)

   以二进制方式或者文本方式打开一个 gzip 格式的压缩文件，返回一个
   *file object*。

   *filename* 参数可以是一个实际的文件名(一个 a "str" 对象或者 "bytes"
   对象), 或者是一个用来读写的已存在的文件对象。

   *mode* 参数可以是二进制模式： "'r'", "'rb'", "'a'", "'ab'", "'w'",
   "'wb'", "'x'" or "'xb'" , 或者是文本模式 "'rt'", "'at'", "'wt'", or
   "'xt'"。默认值是 "'rb'"。

   The *compresslevel* argument is an integer from 0 to 9, as for the
   "GzipFile" constructor.

   对于二进制模式，这个函数等价于 "GzipFile" 构造器：
   "GzipFile(filename, mode, compresslevel)"。在这个例子中，
   *encoding*, *errors* 和 *newline* 三个参数一定不要设置。

   对于文本模式，将会创建一个 "GzipFile" 对象，并将它封装到一个
   "io.TextIOWrapper" 实例中， 这个实例默认了指定编码，错误抓获行为和
   行。

   在 3.3 版更改: 支持 *filename* 为一个文件对象，支持文本模式和
   *encoding*, *errors* 和 *newline* 参数。

   在 3.4 版更改: 支持 "'x'", "'xb'" 和``'xt'`` 三种模式。

   在 3.6 版更改: 接受一个 *path-like object*。

class gzip.GzipFile(filename=None, mode=None, compresslevel=9, fileobj=None, mtime=None)

   Constructor for the "GzipFile" class, which simulates most of the
   methods of a *file object*, with the exception of the "truncate()"
   method.  At least one of *fileobj* and *filename* must be given a
   non-trivial value.

   新的实例基于  *fileobj*，它可以是一个普通文件，一个 "io.BytesIO" 对
   象，或者任何一个与文件相似的对象。当 *filename* 是一个文件对象时，
   它的默认值是 "None"。

   当  *fileobj* 为 "None" 时， *filename* 参数只用于  **gzip** 文件头
   中，这个文件有可能包含未压缩文件的源文件名。如果文件可以被识别，默
   认 *fileobj* 的文件名；否则默认为空字符串，在这种情况下文件头将不包
   含源文件名。

   The *mode* argument can be any of "'r'", "'rb'", "'a'", "'ab'",
   "'w'", "'wb'", "'x'", or "'xb'", depending on whether the file will
   be read or written.  The default is the mode of *fileobj* if
   discernible; otherwise, the default is "'rb'".

   需要注意的是，文件默认使用二进制模式打开。如果要以文本模式打开文件
   一个压缩文件，请使用 "open()" 方法(或者使用 "io.TextIOWrapper" 包装
   "GzipFile" )。

   *compresslevel* 参数是一个从 "0" to "9" 的整数，用于控制压缩等级；
   "1" 最快但压缩比例最小，"9" 最慢但压缩比例最大。 "0" 不压缩。默认为
   "9"。

   The *mtime* argument is an optional numeric timestamp to be written
   to the last modification time field in the stream when compressing.
   It should only be provided in compression mode.  If omitted or
   "None", the current time is used.  See the "mtime" attribute for
   more details.

   调用 "GzipFile" 的 "close()" 方法不会关闭 *fileobj*，因为你可以希望
   增加其它内容到已经压缩的数中。你可以将一个 "io.BytesIO" 对象作为
   *fileobj*，也可以使用 "io.BytesIO" 的 "getvalue()" 方法从内存缓存中
   恢复数据。

   "GzipFile" 支持 "io.BufferedIOBase" 类的接口, 包括迭代和 "with" 语
   句。只有 "truncate()" 方法没有实现。

   "GzipFile" 还提供了以下的方法和属性:

   peek(n)

      在不移动文件指针的情况下读取  *n* 个未压缩字节。最多只有一个单独
      的读取流来服务这个方法调用。返回的字节数不一定刚好等于要求的数量
      。

      注解: 调用 "peek()" 并没有改变 "GzipFile" 的文件指针，它可能改
        变潜在 文件对象(例如： "GzipFile" 使用 *fileobj* 参数进行初始
        化)。

      3.2 新版功能.

   mtime

      在解压的过程中，最后修改时间字段的值可能来自于这个属性，以整数的
      形式出现。在读取任何文件头信息前，初始值为 "None"。

      所有 **gzip** 东方压缩流中必须包含时间戳这个字段。以便于像
      **gunzip**这样的程序可以使用时间戳。格式与 "time.time()" 的返回
      值和  "os.stat()" 对象的 "st_mtime" 属性值一样。

   在 3.1 版更改: 支持 "with" 语句，构造器参数 *mtime* 和  "mtime" 属
   性。

   在 3.2 版更改: 添加了对零填充和不可搜索文件的支持。

   在 3.3 版更改: 实现 "io.BufferedIOBase.read1()"  方法。

   在 3.4 版更改: 支持 "'x'" and "'xb'" 两种模式。

   在 3.5 版更改: 支持写入任意 *bytes-like objects*。"read()" 方法可以
   接受``None``为参数。

   在 3.6 版更改: 接受一个 *path-like object*。

gzip.compress(data, compresslevel=9)

   压缩 *data* 返回一个包含压缩数据的 "bytes" 对象。 *compresslevel*
   的意义与上面提到的 "GzipFile" 构造器一至。

   3.2 新版功能.

gzip.decompress(data)

   解压数据，返回一个 "bytes" 包含未解压数据的对象。

   3.2 新版功能.


13.2.1. 用法示例
================

读取压缩文件示例：

   import gzip
   with gzip.open('/home/joe/file.txt.gz', 'rb') as f:
       file_content = f.read()

创建 GZIP 文件示例：

   import gzip
   content = b"Lots of content here"
   with gzip.open('/home/joe/file.txt.gz', 'wb') as f:
       f.write(content)

使用 GZIP 压缩已有的文件示例：

   import gzip
   import shutil
   with open('/home/joe/file.txt', 'rb') as f_in:
       with gzip.open('/home/joe/file.txt.gz', 'wb') as f_out:
           shutil.copyfileobj(f_in, f_out)

使用 GZIP 压缩二进制字符串示例：

   import gzip
   s_in = b"Lots of content here"
   s_out = gzip.compress(s_in)

参见:

  模块 "zlib"
     支持 **gzip** 格式所需要的基本压缩模块。
