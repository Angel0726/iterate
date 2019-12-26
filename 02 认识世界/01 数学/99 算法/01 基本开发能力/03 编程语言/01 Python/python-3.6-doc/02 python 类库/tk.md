---
title: tk
toc: true
date: 2019-06-30
---
25. Tk图形用户界面(GUI)
***********************

Tcl/Tk集成到 Python 中已经有一些年头了。Python程序员可以通过 "tkinter"
包和它的扩展， "tkinter.tix" 模块和 "tkinter.ttk" 模块，来使用这套鲁棒
的、平台无关的窗口工具集。

"tkinter" 包使用面向对象的方式对 Tcl/Tk进行了一层薄包装。使用 "tkinter"
，你不需要写 Tcl 代码，但可能需要参考 Tk 文档，甚至 Tcl 文档。 "tkinter" 使
用 Python 类，对 Tk 的窗体小部件（Widgets）进行了一系列的封装。除此之外，
内部模块 "_tkinter" 针对 Python 和 Tcl 之间的交互，提供了一套线程安全的机
制。

"tkinter" 最大的优点就一个字：快，再一个，是 Python 自带的。尽管官方文档
不太完整，但有其他资源可以参考，比如 Tk 手册，教程等。 "tkinter" 也以比
较过时的外观为人所知，但在 Tk 8.5中，这一点得到了极大的改观。除此之外，
如果有兴趣，还有其他的一些 GUI 库可供使用。更多信息，请参考 其他图形用户
界面（GUI）包  小节。

* 25.1. "tkinter" --- Tcl/Tk的 Python 接口

  * 25.1.1. Tkinter 模块

  * 25.1.2. Tkinter Life Preserver

    * 25.1.2.1. How To Use This Section

    * 25.1.2.2. A Simple Hello World Program

  * 25.1.3. A (Very) Quick Look at Tcl/Tk

  * 25.1.4. Mapping Basic Tk into Tkinter

  * 25.1.5. How Tk and Tkinter are Related

  * 25.1.6. Handy Reference

    * 25.1.6.1. Setting Options

    * 25.1.6.2. The Packer

    * 25.1.6.3. Packer Options

    * 25.1.6.4. Coupling Widget Variables

    * 25.1.6.5. The Window Manager

    * 25.1.6.6. Tk Option Data Types

    * 25.1.6.7. Bindings and Events

    * 25.1.6.8. The index Parameter

    * 25.1.6.9. Images

  * 25.1.7. File Handlers

* 25.2. "tkinter.ttk" --- Tk themed widgets

  * 25.2.1. Using Ttk

  * 25.2.2. Ttk Widgets

  * 25.2.3. Widget

    * 25.2.3.1. Standard Options

    * 25.2.3.2. Scrollable Widget Options

    * 25.2.3.3. Label Options

    * 25.2.3.4. Compatibility Options

    * 25.2.3.5. Widget States

    * 25.2.3.6. ttk.Widget

  * 25.2.4. Combobox

    * 25.2.4.1. 选项

    * 25.2.4.2. Virtual events

    * 25.2.4.3. ttk.Combobox

  * 25.2.5. Notebook

    * 25.2.5.1. 选项

    * 25.2.5.2. Tab Options

    * 25.2.5.3. Tab Identifiers

    * 25.2.5.4. Virtual Events

    * 25.2.5.5. ttk.Notebook

  * 25.2.6. Progressbar

    * 25.2.6.1. 选项

    * 25.2.6.2. ttk.Progressbar

  * 25.2.7. Separator

    * 25.2.7.1. 选项

  * 25.2.8. Sizegrip

    * 25.2.8.1. Platform-specific notes

    * 25.2.8.2. Bugs

  * 25.2.9. Treeview

    * 25.2.9.1. 选项

    * 25.2.9.2. Item Options

    * 25.2.9.3. Tag Options

    * 25.2.9.4. Column Identifiers

    * 25.2.9.5. Virtual Events

    * 25.2.9.6. ttk.Treeview

  * 25.2.10. Ttk Styling

    * 25.2.10.1. Layouts

* 25.3. "tkinter.tix" --- Extension widgets for Tk

  * 25.3.1. Using Tix

  * 25.3.2. Tix Widgets

    * 25.3.2.1. Basic Widgets

    * 25.3.2.2. File Selectors

    * 25.3.2.3. Hierarchical ListBox

    * 25.3.2.4. Tabular ListBox

    * 25.3.2.5. Manager Widgets

    * 25.3.2.6. Image Types

    * 25.3.2.7. Miscellaneous Widgets

    * 25.3.2.8. Form Geometry Manager

  * 25.3.3. Tix Commands

* 25.4. "tkinter.scrolledtext" --- 滚动文字控件

* 25.5. IDLE

  * 25.5.1. 目录

    * 25.5.1.1. 文件目录 （命令行和编辑器）

    * 25.5.1.2. 编辑目录（命令行和编辑器）

    * 25.5.1.3. Format menu (Editor window only)

    * 25.5.1.4. Run menu (Editor window only)

    * 25.5.1.5. Shell menu (Shell window only)

    * 25.5.1.6. Debug menu (Shell window only)

    * 25.5.1.7. Options menu (Shell and Editor)

    * 25.5.1.8. Window menu (Shell and Editor)

    * 25.5.1.9. Help menu (Shell and Editor)

    * 25.5.1.10. Context Menus

  * 25.5.2. Editing and navigation

    * 25.5.2.1. Editor windows

    * 25.5.2.2. Key bindings

    * 25.5.2.3. Automatic indentation

    * 25.5.2.4. Completions

    * 25.5.2.5. Calltips

    * 25.5.2.6. Python Shell window

    * 25.5.2.7. Text colors

  * 25.5.3. Startup and code execution

    * 25.5.3.1. Command line usage

    * 25.5.3.2. Startup failure

    * 25.5.3.3. Running user code

    * 25.5.3.4. User output in Shell

    * 25.5.3.5. Developing tkinter applications

    * 25.5.3.6. Running without a subprocess

  * 25.5.4. Help and preferences

    * 25.5.4.1. Help sources

    * 25.5.4.2. Setting preferences

    * 25.5.4.3. IDLE on macOS

    * 25.5.4.4. Extensions

* 25.6. 其他图形用户界面（GUI）包
