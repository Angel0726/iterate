---
title: tensorflow 安装新版本
toc: true
date: 2018-10-04
---
# 可以补充进来的

# 用 anaconda 安装最新的 TensorFlow 版本

**存在问题：**

一般从 anaconda 官网下载的 anaconda，查看 tensorflow 依然还是 1.2的版本，现在用 conda 更新 TensorFlow

**解决方法：**

1，打开 anaconda-prompt

2，查看 tensorflow 各个版本：（查看会发现有一大堆 TensorFlow 源，但是不能随便选，选择可以用查找命令定位）

```
anaconda search -t conda tensorflow
```

4，找到自己安装环境对应的最新 TensorFlow 后（可以在终端搜索 anaconda，定位到那一行），然后查看安装命令

```
anaconda show <USER/PACKAGE>
```

安装 anaconda/tensorflow具体操作命令：

```
anaconda show anaconda/tensorflow
```

5，第 4 步会提供一个下载地址，使用下面命令就可安装新版本 tensorflow

```
conda install --channel https://conda.anaconda.org/anaconda tensorflow
```


# 相关

- [已解决：用 anaconda 安装最新的 TensorFlow 版本](https://blog.csdn.net/qq_35203425/article/details/79965389)
