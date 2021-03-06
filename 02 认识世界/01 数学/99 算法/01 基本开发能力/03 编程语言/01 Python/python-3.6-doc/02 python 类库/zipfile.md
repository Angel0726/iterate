---
title: zipfile
toc: true
date: 2019-06-30
---
13.5. "zipfile" --- 使用 ZIP 存档
*******************************

**源代码：** Lib/zipfile.py

======================================================================

ZIP 文件格式是一个常用的归档与压缩标准。 这个模块提供了创建、读取、写
入、添加及列出 ZIP 文件的工具。 任何对此模块的进阶使用都将需要理解此格
式，其定义参见 PKZIP 应用程序笔记。

此模块目前不能处理分卷 ZIP 文件。它可以处理使用 ZIP64 扩展（超过 4 GB
的 ZIP 文件）的 ZIP 文件。它支持解密 ZIP 归档中的加密文件，但是目前不
能创建一个加密的文件。解密非常慢，因为它是使用原生 Python 而不是 C 实
现的。

这个模块定义了以下内容：

exception zipfile.BadZipFile

   为损坏的 ZIP 文件抛出的错误。

   3.2 新版功能.

exception zipfile.BadZipfile

   "BadZipFile" 的别名，与旧版本 Python 保持兼容性。

   3.2 版后已移除.

exception zipfile.LargeZipFile

   当 ZIP 文件取药 ZIP64 功能但是未启用时抛出此错误。

class zipfile.ZipFile

   用于读写 ZIP 文件的类。 欲了解构造函数的描述，参阅段落 ZipFile 对象
   。

class zipfile.PyZipFile

   用于创建包含 Python 库的 ZIP 归档的类。

class zipfile.ZipInfo(filename='NoName', date_time=(1980, 1, 1, 0, 0, 0))

   用于表示档案内一个成员信息的类。 此类的实例会由 "ZipFile" 对象的
   "getinfo()" 和 "infolist()" 方法返回。 大多数 "zipfile" 模块的用户
   都不必创建它们，只需使用此模块所创建的实例。 *filename* 应当是档案
   成员的全名，*date_time* 应当是包含六个字段的描述最近修改时间的元组
   ；这些字段的描述请参阅 ZipInfo Objects。

zipfile.is_zipfile(filename)

   根据文件的 Magic Number，如果 *filename* 是一个有效的 ZIP 文件则返
   回 "True"，否则返回 "False"。 *filename* 也可能是一个文件或类文件对
   象。

   在 3.1 版更改: 支持文件或类文件对象。

zipfile.ZIP_STORED

   未被压缩的归档成员的数字常数。

zipfile.ZIP_DEFLATED

   常用的 ZIP 压缩方法的数字常数。需要 "zlib" 模块。

zipfile.ZIP_BZIP2

   BZIP2 压缩方法的数字常数。需要 "bz2" 模块。

   3.3 新版功能.

zipfile.ZIP_LZMA

   LZMA 压缩方法的数字常数。需要 "lzma" 模块。

   3.3 新版功能.

   注解: ZIP 文件格式规范包括自 2001 年以来对 bzip2 压缩的支持，以及
     自 2006 年以来对 LZMA 压缩的支持。但是，一些工具（包括较旧的
     Python 版本）不支持这些压缩方法，并且可能拒绝完全处理 ZIP 文件，
     或者无法 提取单个文件。

参见:

  PKZIP 应用程序笔记
     Phil Katz 编写的 ZIP 文件格式文档，此格式和使用的算法的创建者。

  Info-ZIP 主页
     有关 Info-ZIP 项目的 ZIP 存档程序和开发库的信息。


13.5.1. ZipFile 对象
====================

class zipfile.ZipFile(file, mode='r', compression=ZIP_STORED, allowZip64=True)

   Open a ZIP file, where *file* can be a path to a file (a string), a
   file-like object or a *path-like object*. The *mode* parameter
   should be "'r'" to read an existing file, "'w'" to truncate and
   write a new file, "'a'" to append to an existing file, or "'x'" to
   exclusively create and write a new file. If *mode* is "'x'" and
   *file* refers to an existing file, a "FileExistsError" will be
   raised. If *mode* is "'a'" and *file* refers to an existing ZIP
   file, then additional files are added to it.  If *file* does not
   refer to a ZIP file, then a new ZIP archive is appended to the
   file.  This is meant for adding a ZIP archive to another file (such
   as "Python.exe").  If *mode* is "'a'" and the file does not exist
   at all, it is created. If *mode* is "'r'" or "'a'", the file should
   be seekable. *compression* is the ZIP compression method to use
   when writing the archive, and should be "ZIP_STORED",
   "ZIP_DEFLATED", "ZIP_BZIP2" or "ZIP_LZMA"; unrecognized values will
   cause "NotImplementedError" to be raised.  If "ZIP_DEFLATED",
   "ZIP_BZIP2" or "ZIP_LZMA" is specified but the corresponding module
   ("zlib", "bz2" or "lzma") is not available, "RuntimeError" is
   raised. The default is "ZIP_STORED".  If *allowZip64* is "True"
   (the default) zipfile will create ZIP files that use the ZIP64
   extensions when the zipfile is larger than 4 GiB. If it is  false
   "zipfile" will raise an exception when the ZIP file would require
   ZIP64 extensions.

   If the file is created with mode "'w'", "'x'" or "'a'" and then
   "closed" without adding any files to the archive, the appropriate
   ZIP structures for an empty archive will be written to the file.

   ZipFile is also a context manager and therefore supports the "with"
   statement.  In the example, *myzip* is closed after the "with"
   statement's suite is finished---even if an exception occurs:

      with ZipFile('spam.zip', 'w') as myzip:
          myzip.write('eggs.txt')

   3.2 新版功能: Added the ability to use "ZipFile" as a context
   manager.

   在 3.3 版更改: Added support for "bzip2" and "lzma" compression.

   在 3.4 版更改: ZIP64 extensions are enabled by default.

   在 3.5 版更改: Added support for writing to unseekable streams.
   Added support for the "'x'" mode.

   在 3.6 版更改: Previously, a plain "RuntimeError" was raised for
   unrecognized compression values.

   在 3.6.2 版更改: The *file* parameter accepts a *path-like object*.

ZipFile.close()

   Close the archive file.  You must call "close()" before exiting
   your program or essential records will not be written.

ZipFile.getinfo(name)

   Return a "ZipInfo" object with information about the archive member
   *name*.  Calling "getinfo()" for a name not currently contained in
   the archive will raise a "KeyError".

ZipFile.infolist()

   Return a list containing a "ZipInfo" object for each member of the
   archive.  The objects are in the same order as their entries in the
   actual ZIP file on disk if an existing archive was opened.

ZipFile.namelist()

   Return a list of archive members by name.

ZipFile.open(name, mode='r', pwd=None, *, force_zip64=False)

   Access a member of the archive as a binary file-like object.
   *name* can be either the name of a file within the archive or a
   "ZipInfo" object.  The *mode* parameter, if included, must be "'r'"
   (the default) or "'w'".  *pwd* is the password used to decrypt
   encrypted ZIP files.

   "open()" is also a context manager and therefore supports the
   "with" statement:

      with ZipFile('spam.zip') as myzip:
          with myzip.open('eggs.txt') as myfile:
              print(myfile.read())

   With *mode* "'r'" the file-like object ("ZipExtFile") is read-only
   and provides the following methods: "read()", "readline()",
   "readlines()", "__iter__()", "__next__()".  These objects can
   operate independently of the ZipFile.

   With "mode='w'", a writable file handle is returned, which supports
   the "write()" method.  While a writable file handle is open,
   attempting to read or write other files in the ZIP file will raise
   a "ValueError".

   When writing a file, if the file size is not known in advance but
   may exceed 2 GiB, pass "force_zip64=True" to ensure that the header
   format is capable of supporting large files.  If the file size is
   known in advance, construct a "ZipInfo" object with "file_size"
   set, and use that as the *name* parameter.

   注解: The "open()", "read()" and "extract()" methods can take a
     filename or a "ZipInfo" object.  You will appreciate this when
     trying to read a ZIP file that contains members with duplicate
     names.

   在 3.6 版更改: Removed support of "mode='U'".  Use
   "io.TextIOWrapper" for reading compressed text files in *universal
   newlines* mode.

   在 3.6 版更改: "open()" can now be used to write files into the
   archive with the "mode='w'" option.

   在 3.6 版更改: Calling "open()" on a closed ZipFile will raise a
   "ValueError". Previously, a "RuntimeError" was raised.

ZipFile.extract(member, path=None, pwd=None)

   Extract a member from the archive to the current working directory;
   *member* must be its full name or a "ZipInfo" object.  Its file
   information is extracted as accurately as possible.  *path*
   specifies a different directory to extract to.  *member* can be a
   filename or a "ZipInfo" object. *pwd* is the password used for
   encrypted files.

   Returns the normalized path created (a directory or new file).

   注解: If a member filename is an absolute path, a drive/UNC
     sharepoint and leading (back)slashes will be stripped, e.g.:
     "///foo/bar" becomes "foo/bar" on Unix, and "C:\foo\bar" becomes
     "foo\bar" on Windows. And all "".."" components in a member
     filename will be removed, e.g.: "../../foo../../ba..r" becomes
     "foo../ba..r".  On Windows illegal characters (":", "<", ">",
     "|", """, "?", and "*") replaced by underscore ("_").

   在 3.6 版更改: Calling "extract()" on a closed ZipFile will raise a
   "ValueError".  Previously, a "RuntimeError" was raised.

   在 3.6.2 版更改: The *path* parameter accepts a *path-like object*.

ZipFile.extractall(path=None, members=None, pwd=None)

   Extract all members from the archive to the current working
   directory.  *path* specifies a different directory to extract to.
   *members* is optional and must be a subset of the list returned by
   "namelist()".  *pwd* is the password used for encrypted files.

   警告: Never extract archives from untrusted sources without prior
     inspection. It is possible that files are created outside of
     *path*, e.g. members that have absolute filenames starting with
     ""/"" or filenames with two dots "".."".  This module attempts to
     prevent that. See "extract()" note.

   在 3.6 版更改: Calling "extractall()" on a closed ZipFile will
   raise a "ValueError".  Previously, a "RuntimeError" was raised.

   在 3.6.2 版更改: The *path* parameter accepts a *path-like object*.

ZipFile.printdir()

   Print a table of contents for the archive to "sys.stdout".

ZipFile.setpassword(pwd)

   Set *pwd* as default password to extract encrypted files.

ZipFile.read(name, pwd=None)

   Return the bytes of the file *name* in the archive.  *name* is the
   name of the file in the archive, or a "ZipInfo" object.  The
   archive must be open for read or append. *pwd* is the password used
   for encrypted  files and, if specified, it will override the
   default password set with "setpassword()".  Calling "read()" on a
   ZipFile that uses a compression method other than "ZIP_STORED",
   "ZIP_DEFLATED", "ZIP_BZIP2" or "ZIP_LZMA" will raise a
   "NotImplementedError". An error will also be raised if the
   corresponding compression module is not available.

   在 3.6 版更改: Calling "read()" on a closed ZipFile will raise a
   "ValueError". Previously, a "RuntimeError" was raised.

ZipFile.testzip()

   Read all the files in the archive and check their CRC's and file
   headers. Return the name of the first bad file, or else return
   "None".

   在 3.6 版更改: Calling "testzip()" on a closed ZipFile will raise a
   "ValueError".  Previously, a "RuntimeError" was raised.

ZipFile.write(filename, arcname=None, compress_type=None)

   Write the file named *filename* to the archive, giving it the
   archive name *arcname* (by default, this will be the same as
   *filename*, but without a drive letter and with leading path
   separators removed).  If given, *compress_type* overrides the value
   given for the *compression* parameter to the constructor for the
   new entry. The archive must be open with mode "'w'", "'x'" or
   "'a'".

   注解: Archive names should be relative to the archive root, that
     is, they should not start with a path separator.

   注解: If "arcname" (or "filename", if "arcname" is  not given)
     contains a null byte, the name of the file in the archive will be
     truncated at the null byte.

   在 3.6 版更改: Calling "write()" on a ZipFile created with mode
   "'r'" or a closed ZipFile will raise a "ValueError".  Previously, a
   "RuntimeError" was raised.

ZipFile.writestr(zinfo_or_arcname, data[, compress_type])

   Write a file into the archive.  The contents is *data*, which may
   be either a "str" or a "bytes" instance; if it is a "str", it is
   encoded as UTF-8 first.  *zinfo_or_arcname* is either the file name
   it will be given in the archive, or a "ZipInfo" instance.  If it's
   an instance, at least the filename, date, and time must be given.
   If it's a name, the date and time is set to the current date and
   time. The archive must be opened with mode "'w'", "'x'" or "'a'".

   If given, *compress_type* overrides the value given for the
   *compression* parameter to the constructor for the new entry, or in
   the *zinfo_or_arcname* (if that is a "ZipInfo" instance).

   注解: When passing a "ZipInfo" instance as the *zinfo_or_arcname*
     parameter, the compression method used will be that specified in
     the *compress_type* member of the given "ZipInfo" instance.  By
     default, the "ZipInfo" constructor sets this member to
     "ZIP_STORED".

   在 3.2 版更改: The *compress_type* argument.

   在 3.6 版更改: Calling "writestr()" on a ZipFile created with mode
   "'r'" or a closed ZipFile will raise a "ValueError".  Previously, a
   "RuntimeError" was raised.

The following data attributes are also available:

ZipFile.filename

   Name of the ZIP file.

ZipFile.debug

   The level of debug output to use.  This may be set from "0" (the
   default, no output) to "3" (the most output).  Debugging
   information is written to "sys.stdout".

ZipFile.comment

   The comment associated with the ZIP file as a "bytes" object. If
   assigning a comment to a "ZipFile" instance created with mode
   "'w'", "'x'" or "'a'", it should be no longer than 65535 bytes.
   Comments longer than this will be truncated.


13.5.2. PyZipFile Objects
=========================

The "PyZipFile" constructor takes the same parameters as the "ZipFile"
constructor, and one additional parameter, *optimize*.

class zipfile.PyZipFile(file, mode='r', compression=ZIP_STORED, allowZip64=True, optimize=-1)

   3.2 新版功能: The *optimize* parameter.

   在 3.4 版更改: ZIP64 extensions are enabled by default.

   Instances have one method in addition to those of "ZipFile"
   objects:

   writepy(pathname, basename='', filterfunc=None)

      Search for files "*.py" and add the corresponding file to the
      archive.

      If the *optimize* parameter to "PyZipFile" was not given or
      "-1", the corresponding file is a "*.pyc" file, compiling if
      necessary.

      If the *optimize* parameter to "PyZipFile" was "0", "1" or "2",
      only files with that optimization level (see "compile()") are
      added to the archive, compiling if necessary.

      If *pathname* is a file, the filename must end with ".py", and
      just the (corresponding "*.pyc") file is added at the top level
      (no path information).  If *pathname* is a file that does not
      end with ".py", a "RuntimeError" will be raised.  If it is a
      directory, and the directory is not a package directory, then
      all the files "*.pyc" are added at the top level.  If the
      directory is a package directory, then all "*.pyc" are added
      under the package name as a file path, and if any subdirectories
      are package directories, all of these are added recursively.

      *basename* is intended for internal use only.

      *filterfunc*, if given, must be a function taking a single
      string argument.  It will be passed each path (including each
      individual full file path) before it is added to the archive.
      If *filterfunc* returns a false value, the path will not be
      added, and if it is a directory its contents will be ignored.
      For example, if our test files are all either in "test"
      directories or start with the string "test_", we can use a
      *filterfunc* to exclude them:

         >>> zf = PyZipFile('myprog.zip')
         >>> def notests(s):
         ...     fn = os.path.basename(s)
         ...     return (not (fn == 'test' or fn.startswith('test_')))
         >>> zf.writepy('myprog', filterfunc=notests)

      The "writepy()" method makes archives with file names like this:

         string.pyc                   # Top level name
         test/__init__.pyc            # Package directory
         test/testall.pyc             # Module test.testall
         test/bogus/__init__.pyc      # Subpackage directory
         test/bogus/myfile.pyc        # Submodule test.bogus.myfile

      3.4 新版功能: The *filterfunc* parameter.

      在 3.6.2 版更改: The *pathname* parameter accepts a *path-like
      object*.


13.5.3. ZipInfo Objects
=======================

Instances of the "ZipInfo" class are returned by the "getinfo()" and
"infolist()" methods of "ZipFile" objects.  Each object stores
information about a single member of the ZIP archive.

There is one classmethod to make a "ZipInfo" instance for a filesystem
file:

classmethod ZipInfo.from_file(filename, arcname=None)

   Construct a "ZipInfo" instance for a file on the filesystem, in
   preparation for adding it to a zip file.

   *filename* should be the path to a file or directory on the
   filesystem.

   If *arcname* is specified, it is used as the name within the
   archive. If *arcname* is not specified, the name will be the same
   as *filename*, but with any drive letter and leading path
   separators removed.

   3.6 新版功能.

   在 3.6.2 版更改: The *filename* parameter accepts a *path-like
   object*.

Instances have the following methods and attributes:

ZipInfo.is_dir()

   Return "True" if this archive member is a directory.

   This uses the entry's name: directories should always end with "/".

   3.6 新版功能.

ZipInfo.filename

   Name of the file in the archive.

ZipInfo.date_time

   上次修改存档成员的时间和日期。这是六个值的元组：

   +---------+----------------------------+
   | 索引    | 值                         |
   |=========|============================|
   | "0"     | Year (>= 1980)             |
   +---------+----------------------------+
   | "1"     | 月（1为基数）              |
   +---------+----------------------------+
   | "2"     | 月份中的日期（1为基数）    |
   +---------+----------------------------+
   | "3"     | 小时（0为基数）            |
   +---------+----------------------------+
   | "4"     | 分钟（0为基数）            |
   +---------+----------------------------+
   | "5"     | 秒（0为基数）              |
   +---------+----------------------------+

   注解: ZIP文件格式不支持 1980 年以前的时间戳。

ZipInfo.compress_type

   Type of compression for the archive member.

ZipInfo.comment

   Comment for the individual archive member as a "bytes" object.

ZipInfo.extra

   Expansion field data.  The PKZIP Application Note contains some
   comments on the internal structure of the data contained in this
   "bytes" object.

ZipInfo.create_system

   System which created ZIP archive.

ZipInfo.create_version

   PKZIP version which created ZIP archive.

ZipInfo.extract_version

   PKZIP version needed to extract archive.

ZipInfo.reserved

   必须为零。

ZipInfo.flag_bits

   ZIP 标志位。

ZipInfo.volume

   Volume number of file header.

ZipInfo.internal_attr

   Internal attributes.

ZipInfo.external_attr

   External file attributes.

ZipInfo.header_offset

   Byte offset to the file header.

ZipInfo.CRC

   CRC-32 of the uncompressed file.

ZipInfo.compress_size

   Size of the compressed data.

ZipInfo.file_size

   Size of the uncompressed file.


13.5.4. 命令行界面
==================

The "zipfile" module provides a simple command-line interface to
interact with ZIP archives.

If you want to create a new ZIP archive, specify its name after the
"-c" option and then list the filename(s) that should be included:

   $ Python -m zipfile -c monty.zip spam.txt eggs.txt

Passing a directory is also acceptable:

   $ Python -m zipfile -c monty.zip life-of-brian_1979/

If you want to extract a ZIP archive into the specified directory, use
the "-e" option:

   $ Python -m zipfile -e monty.zip target-dir/

For a list of the files in a ZIP archive, use the "-l" option:

   $ Python -m zipfile -l monty.zip


13.5.4.1. 命令行选项
--------------------

-l <zipfile>

   List files in a zipfile.

-c <zipfile> <source1> ... <sourceN>

   Create zipfile from source files.

-e <zipfile> <output_dir>

   Extract zipfile into target directory.

-t <zipfile>

   Test whether the zipfile is valid or not.
