---
title: unix
toc: true
date: 2019-06-30
---
2. 在 Unix 平台中使用 Python
*************************


2.1. 获取最新版本的 Python
=========================


2.1.1. 在 Linux 中
----------------

Python预装在大多数 Linux 发行版上，并作为一个包提供给所有其他用户。 但是
，您可能想要使用的某些功能在发行版提供的软件包中不可用。这时您可以从源
代码轻松编译最新版本的 Python。

如果 Python 没有预先安装并且不在发行版提供的库中，您可以轻松地为自己使用
的发行版创建包。 参阅以下链接：

参见:

  https://www.debian.org/doc/manuals/maint-guide/first.en.html
     对于 Debian 用户

  https://en.opensuse.org/Portal:Packaging
     对于 OpenSuse 用户

  https://docs.fedoraproject.org/en-
  US/Fedora_Draft_Documentation/0.1/html/RPM_Guide/ch-creating-
  rpms.html
     对于 Fedora 用户

  http://www.slackbook.org/html/package-management-making-
  packages.html
     对于 Slackware 用户


2.1.2. 在 FreeBSD 和 OpenBSD 上
---------------------------

* FreeBSD用户，使用以下命令添加包:

     pkg install Python3

* OpenBSD用户，使用以下命令添加包:

     pkg_add -r Python

     pkg_add ftp://ftp.openbsd.org/pub/OpenBSD/4.2/packages/<insert your architecture here>/Python-<version>.tgz

  例如：i386用户获取 Python 2.5.1的可用版本:

     pkg_add ftp://ftp.openbsd.org/pub/OpenBSD/4.2/packages/i386/Python-2.5.1p2.tgz


2.1.3. 在 OpenSolaris 系统上
--------------------------

你可以从 OpenCSW 获取、安装及使用各种版本的 Python。比如 "pkgutil -i
Python27" 。


2.2. 构建 Python
===============

如果你想自己编译 CPython，首先要做的是获取 source 。您可以下载最新版本
的源代码，也可以直接提取最新的 clone 。 （如果你想要制作补丁，则需要克
隆代码。）

构建过程包括通常:

   ./configure
   make
   make install

注：特定 Unix 平台的配置选项和注意事项通常记录在 Python 源代码树根目录的
README.rst 文件中。

警告: "make install" 可以覆盖或伪装 "Python3" 二进制文件。因此，建议
  使用 "make altinstall" 而不是 "make altinstall" ，因为后者只安装了
  "*exec_prefix*/bin/Python*version*" 。


2.3. 与 Python 相关的路径和文件
=============================

这取决于本地安装惯例； "prefix" （ "${prefix}" ）和 "exec_prefix" （
"${exec_prefix}" ）  取决于安装，应解释为 GNU 软件；它们可能相同。

例如，在大多数 Linux 系统上，两者的默认值是 "/usr" 。

+-------------------------------------------------+--------------------------------------------+
| 文件/目录                                       | 含义                                       |
|=================================================|============================================|
| "*exec_prefix*/bin/Python3"                     | 解释器的推荐位置                           |
+-------------------------------------------------+--------------------------------------------+
| "*prefix*/lib/Python*version*",                 | 包含标准模块的目录的推荐位置               |
| "*exec_prefix*/lib/Python*version*"             |                                            |
+-------------------------------------------------+--------------------------------------------+
| "*prefix*/include/Python*version*",             | 包含开发 Python 扩展和嵌入解释器所需的       |
| "*exec_prefix*/include/Python*version*"         | include文件的目录的推荐位置                |
+-------------------------------------------------+--------------------------------------------+


2.4. 杂项
=========

要在 Unix 上使用 Python 脚本，需要添加可执行权限，例如：

   $ chmod +x script

并在脚本的顶部放置一个合适的 Shebang 线。一个很好的选择通常是:

   #!/usr/bin/env Python3

将在整个 "PATH" 中搜索 Python 解释器。但是，某些 Unix 系统可能没有 **env**
命令，因此可能需要将 "/usr/bin/Python3" 硬编码为解释器路径。

要在 Python 脚本中使用 shell 命令，请查看 "subprocess" 模块。


2.5. 编辑器和集成开发环境
=========================

有很多支持 Python 编程语言的集成开发环境。大多数编辑器和集成开发环境支持
语法高亮，调试工具和 **PEP 8** 检查。

请访问 Python Editors 和 Integrated Development Environments 以获取完
整列表。
