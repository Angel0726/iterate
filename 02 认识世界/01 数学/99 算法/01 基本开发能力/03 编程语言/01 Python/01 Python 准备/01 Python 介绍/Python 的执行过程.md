---
title: Python 的执行过程
toc: true
date: 2018-07-28 23:18:04
---

# 可以补充进来的

# Python 的执行过程


Python在执行时：

1. 首先会将.py文件中的源代码编译成 Python 的 byte code（字节码）
2. 然后再由 Python Virtual Machine（Python虚拟机）来执行这些编译好的 byte code。


这种机制的基本思想跟 Java，.NET是一致的。然而，Python Virtual Machine与 Java 或.NET的 Virtual Machine不同的是，Python的 Virtual Machine是一种更高级的 Virtual Machine。这里的高级并不是通常意义上的高级，不是说 Python 的 Virtual Machine比 Java 或.NET的功能更强大，而是说和 Java 或.NET相比，Python的 Virtual Machine距离真实机器的距离更远。或者可以这么说，Python的 Virtual Machine是一种抽象层次更高的 Virtual Machine。

基于 C 的 Python 编译出的字节码文件，通常是.pyc格式。<span style="color:red;">没明白？</span>

一个用编译性语言比如 C 或 C++写的程序可以从源文件（即 C 或 C++语言）转换到一个你的计算机使用的语言（二进制代码，即 0 和 1）。这个过程通过编译器和不同的标记、选项完成。运行程序的时候，连接/转载器软件把你的程序从硬盘复制到内存中并且运行。而 Python 语言写的程序不需要编译成二进制代码。你可以直接从源代码运行 程序。在计算机内部，Python解释器把源代码转换成称为字节码的中间形式，然后再把它翻译成计算机使用的机器语言并运行。这使得使用 Python 更加简单。也使得 Python 程序更加易于移植。





# 相关

1. [Python基础教程 w3cschool](https://www.w3cschool.cn/Python/)
2. [Python 3 教程 菜鸟教程](http://www.runoob.com/Python3/Python3-tutorial.html)
