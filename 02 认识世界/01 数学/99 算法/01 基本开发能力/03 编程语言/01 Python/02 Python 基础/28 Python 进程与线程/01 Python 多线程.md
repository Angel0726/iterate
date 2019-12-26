---
title: 01 Python 多线程
toc: true
date: 2019-11-17
---
Python不支持线程。用 Python 编写多线程应用（multithreaded applications）并不方便，因为 Python 有一个叫做全局解释器锁（global interpreter lock (GIL)）的机制，这个机制让编译器只能在一次运行一个 Python 指令。对于一些大数据量的处理，Python并不合适。GIL并不会在短时间内消失。虽然很多大数据处理应用程序为了能在较短的时间内完成 数据集的处理工作都需要运行在计算机集群上，但是仍然有一些情况需要用单进程多线程系统来解决。<span style="color:red;">还有一些什么情况？为什么不能用 Python 的多进程？关于 Python 的多进程的使用还是要好好总结下。</span>但并不是说 Python 不能运行多线程，并行代码。Python C扩展能使用本地多线程（通过 C 或 C++）来并行运行代码，而不通过 GIL 机制，前提是不和 Python object（对象）进行过多交互。<span style="color:red;">什么叫不进行过多交互？到底应该怎么处理？</span> 比如说，Cython项目可以集成 OpenMP （一个用于并行计算的 C 框架） 以实现并行处理循环进而大幅度提髙数值算法的速度。<span style="color:red;">什么意思？Cython 是怎么实现的？要总结下。Cython 到底与我们平时用的 Python 有什么区别？</span>
