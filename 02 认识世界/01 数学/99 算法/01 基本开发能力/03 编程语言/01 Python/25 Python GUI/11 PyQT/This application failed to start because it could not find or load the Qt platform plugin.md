---
title: This application failed to start because it could not find or load the Qt platform plugin
toc: true
date: 2019-06-15
---
# 可以补充进来的

- 这个问题之前就遇到过，忘记记了，又遇到了。

# 安装 PyQt5 并解决 This application failed to start because it could not find or load the Qt platform plugin"

这个问题经常遇到，尤其是直接把 envs 里面的环境拷贝到另一台电脑的时候，matplotlib 也要依赖于这个 才能拿显示出来。

要怎么解决呢？

其实就是：

```
activate env_name_xxxx
pip install Python-qt5
```

但是这个 Python-qt5 下载的非常慢，大概 40M，这时候，console 中会显示它的下载地址，直接复制到浏览器中进行下载，是一个 `.zip` 文件，下载完后，直接安装：

```
pip install path/to/zip.zip
```

安装完之后就可以了。



# 相关

- [安装 PyQt5 并解决 This application failed to start because it could not find or load the Qt platform plugin](https://blog.csdn.net/lt2635996510/article/details/85393691)
