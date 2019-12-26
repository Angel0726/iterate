---
title: modules
toc: true
date: 2019-06-30
---
31. 导入模块
************

本章中介绍的模块提供了导入其他 Python 模块和挂钩以自定义导入过程的新方法
。

本章描述的完整模块列表如下：

* 31.1. "zipimport" --- Import modules from Zip archives

  * 31.1.1. zipimporter Objects

  * 31.1.2. 示例

* 31.2. "pkgutil" --- Package extension utility

* 31.3. "modulefinder" --- 查找脚本使用的模块

  * 31.3.1. "ModuleFinder" 的示例用法

* 31.4. "runpy" --- Locating and executing Python modules

* 31.5. "importlib" --- The implementation of "import"

  * 31.5.1. 概述

  * 31.5.2. 函数

  * 31.5.3. "importlib.abc" —— 关于导入的抽象基类

  * 31.5.4. "importlib.machinery" -- Importers and path hooks

  * 31.5.5. "importlib.util" -- Utility code for importers

  * 31.5.6. 示例

    * 31.5.6.1. Importing programmatically

    * 31.5.6.2. Checking if a module can be imported

    * 31.5.6.3. Importing a source file directly

    * 31.5.6.4. Setting up an importer

    * 31.5.6.5. Approximating "importlib.import_module()"
