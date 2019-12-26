---
title: 关于api-ms-win-crt-runtimel1-1-0.dll缺失的解决方案
toc: true
date: 2019-10-18
---
# 关于api-ms-win-crt-runtimel1-1-0.dll缺失的解决方案

有时我们在使用文件程序的时候，碰到一些问题，提示dll文件缺失，例如标题中的。

<center>

![mark](http://images.iterate.site/blog/image/20191018/W9DsE3LiW9PF.png?imageslim)

</center>

有些情况下载这些dll文件补上就行，有些则不行，像标题这种情况，则需要安装一个小程序。

## 安装VC redit.exe程序解决

是VC的一个程序：[VC redit.exe](https://www.microsoft.com/zh-cn/download/details.aspx?id=48145)

链接：<https://www.microsoft.com/zh-cn/download/details.aspx?id=48145>

下载时选择x86还是x64的根据自己系统的需求，安装成功就可以了。

但是对于有些电脑系统，还会出现这些情况，提示安装失败：

<center>

![mark](http://images.iterate.site/blog/image/20191018/vBmvI0clcvXk.png?imageslim)

</center>

查看一下日志文件：

<center>

![mark](http://images.iterate.site/blog/image/20191018/oh4SlhktnD50.png?imageslim)

</center>

可以看到是更新程序出了问题，我们查看一下系统的事件，

右击计算机->管理->左侧的 事件查看器->Windows日志->Setup，如下

<center>

![mark](http://images.iterate.site/blog/image/20191018/z4UaFMh7jdro.png?imageslim)

</center>

我们查看他的详细信息，发现是有一个更新打不上：

<center>

![mark](http://images.iterate.site/blog/image/20191018/eFVnbE4C6gQh.png?imageslim)

</center>

对，就是KB2999226这个补丁程序。

## 安装KB2999226补丁程序

补丁码：[KB2999226](https://www.microsoft.com/zh-cn/download/details.aspx?id=49077)

链接：<https://www.microsoft.com/zh-cn/download/details.aspx?id=49077>

这个链接是针对win7的，若想要别的，直接在微软官网搜索补丁号即可。

有些时候系统无法更新，关于系统无法更新，若有这个问题，后文叙述。

我们安装的时候可能会发现还是安装不上，查了一下这个补丁程序的系统要求：

<center>

![mark](http://images.iterate.site/blog/image/20191018/0v4j51mLREIz.png?imageslim)

</center>

系统要求是windows7 SP1。估计大多数的问题就在这了，MSU KB2999226不能安装的原因就是系统不是Win7 SP1，只有更新到SP1才能更新这个补丁。所以把系统更新到SP1即可。

## 更新到Win7 SP1

现在把系统更新到SP1。

Win7 SP1 的补丁码是 [KB976932](https://www.microsoft.com/zh-cn/download/details.aspx?id=5842)，

链接：<https://www.microsoft.com/zh-cn/download/details.aspx?id=5842>

有时候会发现补丁安装失败，如果真的出现了，那就这样做吧：进系统后，关闭安全程序，最好设置不开机启动，等到安装完成后，拔网线重启，然后就马到功成了。

这时，我们就能正确安装了，对那个补丁MSU的补丁要不要专门安装一次也无所谓。然后再次安装VC redit.exe就行了。

## 无法安装更新解决方案-删除目录：

1.打开cmd，关闭wuauserv服务，执行

```
net stop wuauserv
```

2.打开windows目录，或者直接win+R 运行 %windir%打开目录
3.找到一个文件夹SoftwareDistribution，删掉或者改名都行，这是更新程序使用的文件夹
4.开启wuauserv服务，执行

```
net start wuauserv
```

# 可以补充进来的

- [关于api-ms-win-crt-runtimel1-1-0.dll缺失的解决方案](https://blog.csdn.net/huqiao1206/article/details/50768481)
