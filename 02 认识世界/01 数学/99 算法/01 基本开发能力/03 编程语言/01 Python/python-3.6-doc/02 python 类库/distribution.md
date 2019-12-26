---
title: distribution
toc: true
date: 2019-06-30
---
28. 软件打包和分发
******************

这些库可帮助你发布和安装 Python 软件。 虽然这些模块设计为与`Python 包
索引 <https://pypi.org>`__结合使用，但它们也可以与本地索引服务器一起使
用，或者根本不使用任何索引服务器。

* 28.1. "distutils" --- 构建和安装 Python 模块

* 28.2. "ensurepip" --- Bootstrapping the "pip" installer

  * 28.2.1. Command line interface

  * 28.2.2. Module API

* 28.3. "venv" --- 创建虚拟环境

  * 28.3.1. 创建虚拟环境

  * 28.3.2. API

  * 28.3.3. 一个扩展 "EnvBuilder" 的例子

* 28.4. "zipapp" --- Manage executable Python zip archives

  * 28.4.1. Basic Example

  * 28.4.2. 命令行界面

  * 28.4.3. Python API

  * 28.4.4. 示例

  * 28.4.5. Specifying the Interpreter

  * 28.4.6. Creating Standalone Applications with zipapp

    * 28.4.6.1. Making a Windows executable

    * 28.4.6.2. Caveats

  * 28.4.7. The Python Zip Application Archive Format
