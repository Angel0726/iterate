---
title: 11 Python 的扩充
toc: true
date: 2019-11-17
---



Python本身被设计为可扩充的。并非所有的特性和功能都集成到语言核心。Python提供了丰富的 API 和工具，以便程序员能够轻松地使用 C 语言、C++、Cython来编写扩充模块。<span style="color:red;">怎么使用 CPython 来编写扩充模块？</span>

这几年，Cython项目（http://cython.org）已经成为 Python 领域中创建编译型扩展以及对接 C/C++ 代码的一大途径。<span style="color:red;">Cython 到底有什么厉害的地方？要总结下</span>

Python编译器本身也可以被集成到其它需要脚本语言的程序内。因此，很多人还把 Python 作为一种“胶水语言”（glue language）使用。使用 Python 将其他语言编写的程序进行集成和封装。在 Google 内部的很多项目，例如 Google Engine使用 C++编写性能要求极高的部分，然后用 Python 或 Java/Go调用相应的模块。<span style="color:red;">怎么把编译器封装到别的程序里面？</span>
