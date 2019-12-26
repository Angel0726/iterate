---
title: 03 Pytorch 安装
toc: true
date: 2019-06-27
---
# Pytorch 安装



```bash
#pytorch为环境名，这里创建 python3.6版。
conda create -n pytorch python=3.6
# 切换到 pytorch 环境
activate pytorch
# ***以下为 1.0版本安装***
#安装 GPU 版本，根据 cuda 版本选择 cuda80，cuda92，如果 cuda 是 9.0版，则不需要
#直接 conda install pytorch -c pytorch 即可
# win下查看 cuda 版本命令 nvcc -V
conda install pytorch cuda92 -c pytorch
# cpu版本使用
# conda install pytorch-cpu -c pytorch

# torchvision 是 torch 提供的计算机视觉工具包，后面介绍
pip install torchvision


# *** 官方更新了 1.01 所以安装方式也有小的变更
# torchversion提供了 conda 的安装包，可以用 conda 直接安装了
# cuda支持也升级到了 10.0
# 安装方式如下：
# cpu版本
conda install pytorch-cpu torchvision-cpu -c pytorch
# GPU版
conda install pytorch torchvision cudatoolkit=10.0 -c pytorch
# cudatoolkit后跟着相应的 cuda 版本
# 目前测试 8.0、9.0、9.1、9.2、10.0都可安装成功
```

验证输入 python 进入：

```python
import torch
torch.__version__
# 得到结果'0.4.1'
```



## 问题解决

### 1 启动 python 提示编码错误

删除 .python_history [来源](http://tantai.org/posts/install-keras-pytorch-jupyter-notebook-Anaconda-window-10-cpu/)

### 2 默认目录设置不起效

打开快捷方式，看看快捷方式是否跟这个截图一样，如果是则删除 `%USERPROFILE%` ，该参数会覆盖掉 notebook_dir 设置，导致配置不起效：

<center>

![](http://images.iterate.site/blog/image/20190627/2rnFMX0s8e0f.png?imageslim){ width=55% }

</center>




# 相关

- [pytorch-handbook](https://github.com/zergtant/pytorch-handbook)
