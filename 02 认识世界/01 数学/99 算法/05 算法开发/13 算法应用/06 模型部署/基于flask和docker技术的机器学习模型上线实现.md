---
title: 基于flask和docker技术的机器学习模型上线实现
toc: true
date: 2019-11-17
---
# 可以补充进来的

- 文章不全，拆分到 docker 安装里面。

# 基于flask和docker技术的机器学习模型上线实现

对模型进行上线，以便可以进行Web展示。


这里涉及技术路线选择问题。选择主要基于两点考虑：

1. 方便调用机器学习模型，搭建过程简单，不必过分受限于复杂的技术；
2. 搭建好的模型便于部署和扩展。


基于上述考虑，我选择了两种技术：flask和docker。

1. flask技术是基于Python的微服务器框架，可以方便实现 restful api调用。同时，Python有很多机器学习开源社区，提供很多学习资源，常见的工具有scikit-learn，方便后续机器学习模型搭建；
2. docker是基于容器的虚拟化技术，可以方便部署运行环境，避免不同平台（PC，笔记本等）配置差异带来的运行问题。只需要简单的从 docker hub 上 pull 或者 push 就可以实现运行环境的上传或者部署。

本博客分为三部分：

1. docker，virtual machine环境搭建；
2. 基于flask和docker容器技术的微服务器搭建；
3. 机器学习模型上线实现；

下面简单介绍docker，virtual machine环境搭建。本电脑为win7 professional系统，在window环境下搭建系统，推荐使用docker toolbox，提供了安装工具箱。

安装好之后，电脑桌面上出现：

```
Docker Quickstart Terminal
Oracle VM VirtualBox
Kitematic (Alpha)
```

在C盘中出现文件夹：

<center>

![mark](http://images.iterate.site/blog/image/20191105/bl6riNjlpObE.png?imageslim)

</center>


其中，目录

```
C:\Users\LiuJiankang\.docker\machine\machines\default
```

存放 disk 文件，将 dockerfile 或者 docker-compose 文件放置这个路径下，可以方便执行该文件从 docker hub 上pull镜像，从而显现环境搭建过程。

环境搭建好之后，下面简要对上述三个软件进行介绍。先从Docker Quickstart Terminal 开始。

## 一、Docker Quickstart Terminal

<center>

![mark](http://images.iterate.site/blog/image/20191105/tciORXlHXYWQ.png?imageslim)

</center>

Docker Quickstart Terminal提供了虚拟linux执行环境，可以执行常见的Linux指令，例如，当定位到dockerfile或者docker-compose文件位置时（本机），需要指令有：

```
cd \
cd Users
cd ~
cd \.docker\machine\machines\default
```

想要查看正在运行容器时，执行指令：


```
docker ps
```

有时，需要进入容器内对容器进行操作，在执行上述指令后，知道正在运行容器的容器号，可以执行下述指令进入容器

```
docker exec  -it bc2a50a92b21  /bin/bash
```

当需要对指令进行copy或者粘贴的时候，可以通过以下方式：

<center>

![mark](http://images.iterate.site/blog/image/20191105/vi8C9leu7DQG.png?imageslim)

</center>

点击标注的位置-》编辑即可出现相关操作。


## 二 、 Oracle VM VirtualBox

下面介绍Oracle VM VirtualBox，Oracle VM VirtualBox执行默认操作就可以了，一般不需要变动。

## 三 、 Kitematic (Alpha)

在初始运行时，需要注册docker hub账户，当然，也可以skip。注册账户后，可以对镜像的自我管理，上传自己的镜像。

下面是Kitematic (Alpha)功能介绍，如图：

<center>

![mark](http://images.iterate.site/blog/image/20191105/Kpg89AS1EbLj.png?imageslim)

</center>


1：镜像，现在下载的镜像。
2：点击该按钮，可以根据需要下载库中的镜像，例如，1中的镜像可以通过搜索scikit-learn找到，安装非常简单。
3：对镜像进行操作，例如stop ,restart等，在1中，点击×号可以移除镜像。
4：日志，可以监测相应容器运行是否正常，根据日志检查错误。
5：volume挂载，点击可以找到本机目录下文件位置，通过该位置添加文件，可以挂载代码或者数据，并且可以做到热启动，相关的改变可以实时反映在容器上。
6：Docker CLI，负责指令执行，功能和Docker Quickstart Terminal 几乎相同。


总结：

本篇博客总结了安装过程，对三个软件即Docker Quickstart Terminal， Oracle VM VirtualBox，Kitematic (Alpha)进行了简要介绍，为下文安装镜像打好基础。


# 相关

- [基于flask和docker技术的机器学习模型上线实现](https://blog.csdn.net/kangkang13/article/details/79235039)
