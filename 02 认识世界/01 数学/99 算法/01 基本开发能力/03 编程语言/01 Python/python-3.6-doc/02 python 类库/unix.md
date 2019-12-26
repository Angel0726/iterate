---
title: unix
toc: true
date: 2019-06-30
---
35. Unix 专有服务
*****************

本章描述的模块提供了 Unix 操作系统独有特性的接口，在某些情况下也适用于
它的某些或许多衍生版。 以下为模块概览：

* 35.1. "posix" --- The most common POSIX system calls

  * 35.1.1. Large File Support

  * 35.1.2. Notable Module Contents

* 35.2. "pwd" --- 用户密码数据库

* 35.3. "spwd" --- The shadow password database

* 35.4. "grp" --- The group database

* 35.5. "crypt" --- Function to check Unix passwords

  * 35.5.1. Hashing Methods

  * 35.5.2. Module Attributes

  * 35.5.3. 模块函数

  * 35.5.4. 示例

* 35.6. "termios" --- POSIX style tty control

  * 35.6.1. 示例

* 35.7. "tty" --- 终端控制功能

* 35.8. "pty" --- Pseudo-terminal utilities

  * 35.8.1. 示例

* 35.9. "fcntl" --- The "fcntl" and "ioctl" system calls

* 35.10. "pipes" --- Interface to shell pipelines

  * 35.10.1. Template Objects

* 35.11. "resource" --- Resource usage information

  * 35.11.1. Resource Limits

  * 35.11.2. Resource Usage

* 35.12. "nis" --- Interface to Sun's NIS (Yellow Pages)

* 35.13. Unix syslog 库例程

  * 35.13.1. 示例

    * 35.13.1.1. Simple example
