---
title: 06 requirements.txt 文件使用
toc: true
date: 2019-11-17
---


# requirements.txt 文件使用


- 可以导出当前安装包到 requirements.txt 文本中。这样别人如果使用你的程序，安装环境的时候子需要把 requirements.txt 安装进来即可。




```py
# 导出
pip freeze > requirements.txt




# 导入
(sudo) pip install –r requirements.txt


# 下载包而不安装
pip install -d <目录> -r requirements.txt


# 卸载
pip uninstall -r requirements.txt
```


<span style="color:red;">下载包而不安装 这个挺有用的。</span>


## 使用方式


- 首先你需要一台可以连接其他 pip 源的电脑，通常也就是你自己的开发环境，并且安装了 pip.
- `pip install pip2pi`
- 用 pip freeze 在你的开发环境上 制作一个 requirements 文件：`pip freeze > requirements.txt`
- 手动更新下 requirements.txt 文件，只留一行：pyecharts==0.4.1
- 建立一个 pacakges 文件夹，作为存放本地源的路径
- 假设你的 packages 和 requirements.txt 都在 `c:\` 下
- 执行：`pip2pi package --no-binary :all: -r requirements.txt`，取得所有需要的包
- 执行：`pip2tgz packages -r requirements.txt`，取得所有需要的 wheel
- 用 u 盘把 packages 和 requirements.txt 拷贝到内网
- 内网执行：`pip install --no-index --find-links=packages -r requirements.txt`

上面这个在执行到 `pip2pi package --no-binary :all: -r requirements.txt` 的时候有问题：`module 'pip' has no attribute 'main'`

上面这个问题一直没有得到解决。



# 相关


- [在无法连外网的服务器上安装 Python 包（conda）](http://www.meteoboy.com/conda-without-internet.html)
- [求助，如何在不能联网的情况下，在 anaconda 中安装已经下载好的 pyecharts？](http://wenda.chinahadoop.cn/question/10092)
- [在离线环境下怎么通过 Anaconda 安装 theano？](https://www.zhihu.com/question/45987778/answer/147733232)
- [由于公司电脑不能联网，如何离线安装 anaconda，以及 Python 的一些工具包，谢谢。](https://ask.julyedu.com/question/7498)
- [pip2pi](https://github.com/wolever/pip2pi)
- [Anaconda 离线安装 Python 包方法](https://blog.csdn.net/u012318074/article/details/77222601)
- [module 'pip' has no attribute 'main'](https://blog.csdn.net/yup1212/article/details/80047326)
