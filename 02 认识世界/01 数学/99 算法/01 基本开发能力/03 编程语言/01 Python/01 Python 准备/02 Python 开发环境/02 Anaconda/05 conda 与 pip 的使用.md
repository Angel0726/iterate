---
title: 05 conda 与 pip 的使用
toc: true
date: 2018-06-14 14:54:45
---
# 可以补充进来的


# conda 与 pip 的使用

可以使用 conda 和 pip 两种工具进行库的下载和更新：

```
conda install package_name
```

但有时候一些库不在 Anaconda 的服务器上，上面的命令会失败。这个时候我们可以使用 pip（pip是一个 Python 的包管理工具）：

```
pip install package_name
```

conda更新：

```
conda update package_name
```

pip更新：

```
pip install --upgrade package_name
```

这两个下载方式都可以用，不会冲突的。**不过不要使用 pip 来更新用 conda 下载的包，这会导致库之间的依赖出现问题。** 所以在使用 Anaconda 的时候，最好先尝试使用 conda 来更新，不行的话再使用 pip。
