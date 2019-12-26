---
title: 03 Pip 镜像源
toc: true
date: 2019-11-17
---

## pip 镜像源

国内 pypi 镜像：

- 阿里：`https://mirrors.aliyun.com/pypi/simple`
- 中国科学技术大学：<http://pypi.mirrors.ustc.edu.cn/simple/>

指定单次安装源：

```py
pip install <包名> -i https://mirrors.aliyun.com/pypi/simple
```

指定全局安装源：

在unix和macos，配置文件为：`$HOME/.pip/pip.conf`
在windows上，配置文件为：`%HOME%\pip\pip.ini`

```
[global]
timeout = 6000
  index-url = https://mirrors.aliyun.com/pypi/simple
```
