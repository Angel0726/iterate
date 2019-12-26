---
title: chcp 不是内部或外部命令 也不是可运行的程序 或批处理文件
toc: true
date: 2019-06-30
---
# 'chcp' 不是内部或外部命令，也不是可运行的程序 或批处理文件。 'cmd' 不是内部或外部命令，也不是可运行的

之前装了 `vn.py` 的 exe，然后 发现 activate tensorflow 的时候总是进到 vn 里面。可能是这个 vn 项目在安装的时候把 activate 固定住了，所以卸载了 `vn.py` ，发现有如下问题：


<center>

![](http://images.iterate.site/blog/image/20190630/PlIgzwx493nS.png?imageslim){ width=55% }
</center>

上网搜了下，是环境变量的问题。

确认了下环境变量，果然，anaconda 的地址没有了，因此添加下面三条：

<center>

![](http://images.iterate.site/blog/image/20190630/Bt6WOkxK0s86.png?imageslim){ width=55% }

</center>

添加完之后就可以了。


# 相关

- ['chcp' 不是内部或外部命令，也不是可运行的程序 或批处理文件。 'cmd' 不是内部或外部命令，也不是可运行的](https://www.cnblogs.com/Aaron12/p/9989470.html)
