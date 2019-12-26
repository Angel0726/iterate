---
title: Ubuntu 安装 sogou 拼音输入法
toc: true
date: 2018-10-17
---
# Ubuntu 16.04 安装 sogou 拼音输入法

安装完 Ubuntu 16.04 后，要更换为国内的软件源：

```
sudo gedit /etc/apt/sources.list   #用文本编辑器打开源列表
```



在文件开头添加下面的阿里云的软件源：

```
deb http://mirrors.aliyun.com/ubuntu/ quantal main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ quantal-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ quantal-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ quantal-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ quantal-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ quantal main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ quantal-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ quantal-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ quantal-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ quantal-backports main restricted universe multiverse
```

更新软件源：

```
sudo apt-get update
```

更多软件源请自行百度，阿里云的软件源是 Ubuntu 官方推荐的国内软件源。清华的软件源也可以。

Ubuntu 的键盘输入法系统包含 iBus、fcitx 等多种，因为 sogou 是基于 fcitx 的，而系统默认的是 iBus，所以安装使用不同的输入法，相关的键盘输入法系统也要设置改变。<span style="color:red;">没想到还与键盘输入法有关，之前看到这个地方总是有点不清楚</span>

一、安装 sogou 输入法步骤：

1、首先到搜狗输入法官网下载搜狗输入法，下载的是个 deb 文件。

搜狗输入法 Linux 版下载地址：http://pinyin.sogou.com/linux/?r=pinyin

2、Ubuntu 16.04安装搜狗输入法命令如下：

```
sudo apt-get install -f
sudo dpkg -i sogoupinyin_2.0.0.0072_amd64.deb
```

deb文件名，要和自己下载的版本一致。

3、安装完毕，设置语言选项

到系统设置->语言支持（System->Language Support），将键盘输入法系统由默认的 iBus 设置为 fcitx。如下图：

![](http://images.iterate.site/blog/image/181017/8DD3c9A9K8.png?imageslim){ width=55% }

操作此步骤时，如果没有出现 fcitx 选项，说明你的机器还需要安装 fcitx 键盘输入法系统，直接看第二部分 fcitx 的安装。

4、注销，重新登陆。

  将键盘输入法系统改为 fcitx 后，一定要注销，而且操作顺序不能改。这时还不能马上使用，还要点击右上角的输入法图标，点击设置，进入设置界面，这个时候没有看到搜狗输入法，点击左下角的加号，然后注意先要去掉”只显示当前语言的输入法”前面那个勾，然后再搜索”sogo”，这个时候就看到 sogo pinyin了，接着添加就可以了，然后就可以切换输入法了。

![](http://images.iterate.site/blog/image/181017/FBihId5920.png?imageslim){ width=55% }

  注意: 安装搜狗输入法之前请先更换为国内的软件源，否则无法解决依赖问题。

二、安装 fcitx 键盘输入法系统

  sogou是基于 fcitx 的，而系统默认的键盘输入法系统是 iBus。Ubuntu 16.04默认是带有 fcitx 的，正常安装，如果有的话，按上面步骤即可完成；但有些版本的 Ubuntu，需要自己安装 fcitx，才能安装使用 sogou。

1、添加以下源

  sudo add-apt-repository ppa:fcitx-team/nightly

2、更新系统：sudo apt-get update

3、安装 fcitx：sudo apt-get install fcitx

4、安装 fcitx 的配置工具：sudo apt-get install fcitx-config-gtk

5、安装 fcitx 的 table-all软件包:sudo apt-get install fcitx-table-all

6、安装 im-switch切换工具：sudo apt-get install im-switch

  至此，fcitx键盘输入法系统就安装好了。第 5，6步需要按键“Y”确认安装。简单测试的方法就是在终端键入“fcitx”，有各种提示就对了。

  安装完 fcitx 后，再安装 sogou 即可。



---------------------
作者：ljheee
来源：CSDN
原文：https://blog.csdn.net/ljheee/article/details/52966456?utm_source=copy
版权声明：本文为博主原创文章，转载请附上博文链接！



- [Ubuntu 16.04安装 sogou 拼音输入法](https://blog.csdn.net/ljheee/article/details/52966456)
