---
title: 07 PyCharm 实用拓展功能
toc: true
date: 2019-10-18
---

# PyCharm 实用拓展功能


# PyCharm 调试时传入命令行参数

<span style="color:red;">需要补充下。</span>





## PyCharm中清除已编译.pyc中间文件

选中你的workspace > 右键 > clean Python compiled files

还可以自己写一个清除代码

## PyCharm设置外部工具


[Python小工具 ](https://blog.csdn.net/pipisorry/article/details/46754515)针对当前PyCharm中打开的py文件对应的目录删除其中所有的pyc文件。如果是直接运行（而不是在下面的tools中运行），则删除`E:\mine\Python_workspace\WebSite`目录下的pyc文件。

## 将上面的删除代码改成外部工具

PyCharm > settings > tools > external tools > +添加

Name: DelPyc

program: $PyInterpreterDirectory$/Python Python安装路径

Parameters: $ProjectFileDir$/Oth/Utility/DelPyc.py $FileDir$

Work directory: $FileDir$

Note:Parameters后面的 $FileDir$参数是说，DelPyc是针对当前PyCharm中打开的py文件对应的目录删除其中所有的pyc文件。

之后可以通过下面的方式直接执行


<center>

![mark](http://images.iterate.site/blog/image/20191109/q5F8sfnnCso7.png?imageslim)

</center>

Note:再添加一个Tools名为DelPycIn

program: Python安装路径，e.g.     D:\Python3.4.2\Python.exe

Parameters: E:\mine\Python_workspace\Utility\DelPyc.py

Work directory 使用变量 $FileDir$

参数中没有$FileDir$，这样就可以直接删除常用目录r'E:\mine\Python_workspace\WebSite'了，两个一起用更方便

## 代码质量

当你在打字的时候，PyCharm会检查你的代码是否符合PEP8。它会让你知道，你是否有太多的空格或空行等等。如果你愿意，你可以配置PyCharm运行pylint作为外部工具。


# 相关

- [PyCharm快捷键、常用设置、配置管理](https://blog.csdn.net/pipisorry/article/details/39909057)
- [（原+转）PyCharm中传入命令行参数](https://www.cnblogs.com/darkknightzh/p/5670821.html)
