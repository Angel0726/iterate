---
title: Python 文件文件夹处理
toc: true
date: 2018-06-22 22:13:34
---
## 可以补充进来的
- 没有整理




## Python File(文件) 方法


file 对象使用 open 函数来创建，下表列出了 file 对象常用的函数：

- file.close()关闭文件。关闭后文件不能再进行读写操作。
- file.flush()刷新文件内部缓冲，直接把内部缓冲区的数据立刻写入文件, 而不是被动的等待输出缓冲区写入。
- file.fileno()返回一个整型的文件描述符(file descriptor FD 整型), 可以用在如 os 模块的 read 方法等一些底层操作上。
- file.isatty()如果文件连接到一个终端设备返回 True，否则返回 False。
- file.next()返回文件下一行。
- file.read([size])从文件读取指定的字节数，如果未给定或为负则读取所有。
- file.readline([size])读取整行，包括 "\n" 字符。
- file.readlines([sizehint])读取所有行并返回列表，若给定 sizeint>0，返回总和大约为 sizeint 字节的行, 实际读取值可能比 sizhint 较大, 因为需要填充缓冲区。
- file.seek(offset[, whence])设置文件当前位置
- file.tell()返回文件当前位置。
- file.truncate([size])截取文件，截取的字节通过 size 指定，默认为当前文件位置。
- file.write(str)将字符串写入文件，没有返回值。
- file.writelines(sequence)向文件写入一个序列字符串列表，如果需要换行则要自己加入每行的换行符。





# 相关
  1. [Python基础教程 w3cschool](https://www.w3cschool.cn/Python/)
  2. [Python 3 教程 菜鸟教程](http://www.runoob.com/Python3/Python3-tutorial.html)
