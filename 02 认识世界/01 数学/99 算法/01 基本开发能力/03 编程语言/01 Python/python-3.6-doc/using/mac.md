---
title: mac
toc: true
date: 2019-06-30
---
4. 在苹果系统上使用 Python
**************************

作者:
   Bob Savage <bobsavage@mac.com>

运行 Mac OS X 的 Macintosh上 的 Python 原则上与任何其他 Unix 平台上的
Python 非常相似，但是还有一些额外的功能，例如 IDE 和包管理器，值得一提
。


4.1. 获取和安装 MacPython
=========================

Mac OS X 10.8 附带 Apple 预安装的 Python 2.7 。 如果你愿意，可以从
Python 网站（ https://www.Python.org ）安装最新版本的 Python 3 。
Python 的当前“通用二进制”版本可以在 Mac 的新 Intel 和传统 PPC CPU 上本
地运行。

你安装后得到的东西有：

* A "MacPython 3.6" folder in your "Applications" folder. In here
  you find IDLE, the development environment that is a standard part
  of official Python distributions; PythonLauncher, which handles
  double- clicking Python scripts from the Finder; and the "Build
  Applet" tool, which allows you to package Python scripts as
  standalone applications on your system.

* 框架 "/Library/Frameworks/Python.framework" ，包括 Python 可执行文
  件 和库。安装程序将此位置添加到 shell 路径。 要卸载 MacPython ，你可
  以 简单地移除这三个项目。 Python 可执行文件的符号链接放在
  /usr/local/bin/ 中。

Apple 提供的 Python 版本分别安装在
"/System/Library/Frameworks/Python.framework" 和 "/usr/bin/Python" 中
。 你永远不应修改或删除这些内容，因为它们由 Apple 控制并由 Apple 或第
三方软件使用。 请记住，如果你选择从 Python.org 安装较新的 Python 版本
，那么你的计算机上将安装两个不同但都有用的 Python ，因此你的路径和用法
与你想要执行的操作一致非常重要。

IDLE 包含一个帮助菜单，允许你访问 Python 文档。 如果您是 Python 的新手
，你应该开始阅读该文档中的教程介绍。

如果你熟悉其他 Unix 平台上的 Python ，那么你应该阅读有关从 Unix shell
运行 Python 脚本的部分。


4.1.1. 如何运行 Python 脚本
---------------------------

在 Mac OS X 上开始使用 Python 的最佳方法是通过 IDLE 集成开发环境，参见
IDE 部分，并在 IDE 运行时使用“帮助”菜单。

If you want to run Python scripts from the Terminal window command
line or from the Finder you first need an editor to create your
script. Mac OS X comes with a number of standard Unix command line
editors, **vim** and **emacs** among them. If you want a more Mac-like
editor, **BBEdit** or **TextWrangler** from Bare Bones Software (see
http://www.barebones.com/products/bbedit/index.html) are good choices,
as is **TextMate** (see https://macromates.com/). Other editors
include **Gvim** (http://macvim.org) and **Aquamacs**
(http://aquamacs.org/).

要从终端窗口运行脚本，必须确保:file:*/usr/local/bin* 位于 shell 搜索路
径中。

要从 Finder 运行你的脚本，你有两个选择：

* 把脚本拖拽到 **PythonLauncher**

* 选择 **PythonLauncher** 作为通过 finder Info 窗口打开脚本（或任何
  .py 脚本）的默认应用程序，然后双击脚本。 **PythonLauncher** 有各种首
  选项来控制脚本的启动方式。 拖拽方式允许你为一次调用更改这些选项，或
  使用其“首选项”菜单全局更改内容。


4.1.2. 运行有图形界面的脚本
---------------------------

对于旧版本的 Python ，你需要注意一个 Mac OS X 的怪异之处：与 Aqua 窗口
管理器通信的程序（换而言之，任何具有图形界面的程序）需要以特殊方式运行
。 使用 **Pythonw** 而不是 **Python** 来启动这样的脚本。

With Python 3.6, you can use either **Python** or **Pythonw**.


4.1.3. 配置
-----------

OS X 上的 Python 遵循所有标准的 Unix 环境变量，例如 "PythonPATH" ，但
是为 Finder 启动的程序设置这些变量是非标准的，因为 Finder 在启动时不读
取你的 ".profile" 或 ".cshrc" 。你需要创建一个文件
"~/.MacOSX/environment.plist" 。 有关详细信息，请参阅 Apple 的技术文档
QA1067 。

更多关于在 MacPython 中安装 Python 包的信息，参阅 安装额外的 Python 包
部分。


4.2. IDE
========

MacPython ships with the standard IDLE development environment. A good
introduction to using IDLE can be found at
https://hkn.eecs.berkeley.edu/~dyoo/Python/idle_intro/index.html.


4.3. 安装额外的 Python 包
=========================

有几个方法可以安装额外的 Python 包：

* 可以通过标准的 Python distutils 模式（ "Python setup.py install"
  ） 安装软件包。

* 许多包也可以通过 **setuptools** 扩展或 **pip** 包装器安装，请参阅
  https://pip.pypa.io/ 。


4.4. Mac 上的图形界面编程
=========================

使用 Python 在 Mac 上构建 GUI 应用程序有多种选择。

*PyObjC* is a Python binding to Apple's Objective-C/Cocoa framework,
which is the foundation of most modern Mac development. Information on
PyObjC is available from https://Pythonhosted.org/pyobjc/.

标准的 Python GUI 工具包是 "tkinter" ，基于跨平台的 Tk 工具包（
https://www.tcl.tk ）。 Apple 的 OS X 捆绑了 Aqua 原生版本的 Tk ，最新
版本可以从 https://www.activestate.com 下载和安装；它也可以从源代码构
建。

*wxPython* is another popular cross-platform GUI toolkit that runs
natively on Mac OS X. Packages and documentation are available from
http://www.wxPython.org.

*PyQt* 是另一种流行的跨平台 GUI 工具包，可在 Mac OS X 上本机运行。更多
信息可在 https://riverbankcomputing.com/software/pyqt/intro 上找到。


4.5. 在 Mac 上分发 Python 应用程序
==================================

放置在 MacPython 3.6 文件夹中的“ Build Applet ”工具适用于在你自己的计
算机上打包小型 Python 脚本以作为标准 Mac 应用程序运行。 但是，此工具不
够强大，无法将 Python 应用程序分发给其他用户。

在 Mac 上部署独立 Python 应用程序的标准工具是 **py2app** 。有关安装和
使用 py2app 的更多信息，请访问 http://undefined.org/Python/#py2app 。


4.6. 其他资源
=============

MacPython 邮件列表是 Mac 上 Python 用户和开发人员的优秀支持资源：

https://www.Python.org/community/sigs/current/Pythonmac-sig/

另一个有用的资源是 MacPython wiki ：

https://wiki.Python.org/moin/MacPython
