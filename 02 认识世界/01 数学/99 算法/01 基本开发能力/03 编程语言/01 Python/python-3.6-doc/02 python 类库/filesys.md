---
title: filesys
toc: true
date: 2019-06-30
---
11. 文件和目录访问
******************

本章中描述的模块处理磁盘文件和目录。 例如，有一些模块用于读取文件的属
性，以可移植的方式操作路径以及创建临时文件。 本章的完整模块列表如下：

* 11.1. "pathlib" --- 面向对象的文件系统路径

  * 11.1.1. 基础使用

  * 11.1.2. 纯路径

    * 11.1.2.1. 通用性质

    * 11.1.2.2. 运算符

    * 11.1.2.3. 访问个别部分

    * 11.1.2.4. 方法和特征属性

  * 11.1.3. 具体路径

    * 11.1.3.1. 方法

* 11.2. "os.path" --- 常见路径操作

* 11.3. "fileinput" --- Iterate over lines from multiple input
  streams

* 11.4. "stat" --- Interpreting "stat()" results

* 11.5. "filecmp" --- File and Directory Comparisons

  * 11.5.1. The "dircmp" class

* 11.6. "tempfile" --- Generate temporary files and directories

  * 11.6.1. 示例

  * 11.6.2. Deprecated functions and variables

* 11.7. "glob" --- Unix style pathname pattern expansion

* 11.8. "fnmatch" --- Unix filename pattern matching

* 11.9. "linecache" --- Random access to text lines

* 11.10. "shutil" --- High-level file operations

  * 11.10.1. Directory and files operations

    * 11.10.1.1. copytree example

    * 11.10.1.2. rmtree example

  * 11.10.2. Archiving operations

    * 11.10.2.1. Archiving example

  * 11.10.3. Querying the size of the output terminal

* 11.11. "macpath" --- Mac OS 9 路径操作函数

参见:

  模块 "os"
     操作系统接口，包括处理比 Python *文件对象* 更低级别文件的功能。

  模块 "io"
     Python的内置 I/O 库，包括抽象类和一些具体的类，如文件 I/O 。

  内置函数 "open()"
     使用 Python 打开文件进行读写的标准方法。
