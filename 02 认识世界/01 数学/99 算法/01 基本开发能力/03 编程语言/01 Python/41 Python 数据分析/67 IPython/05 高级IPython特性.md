---
title: 05 高级IPython特性
toc: true
date: 2018-08-03 11:40:38
---

# B.5 Advanced IPython Features（高级 IPython 特性）

# 1 Making Your Own Classes IPython-Friendly（让自己的类 IPython 友好）

iPython在命令行形式上能很好的展示 Python 的各种对象，看起来很方便。但是用户自己定义的类就不能保证了，所以我们自己应该保证输出的效果。假设我们有一个简单的类：


```Python
class Message:
    def __init__(self, msg):
        self.msg = msg
```

如果按上面这么写，我们会对输出的效果很失望：


```Python
x = Message('I have a secret')
```


```Python
x
```




    <__main__.Message at 0x104ad1668>



iPython中返回的字符串是经过`__repr__`魔法函数处理过的（`output=repr(obj)`），然后才打印出来。因此，我们最好添加一个`__repr__`方法：


```Python
class Message:
    def __init__(self, msg):
        self.msg = msg
    def __repr__(self):
        return 'Message: %s' % self.msg
```


```Python
x = Message('I have a secret')
```


```Python
x
```




    Message: I have a secret



# 2 Profiles and Configuration（配置文件和配置）

iPython和 jupyter 的一些外观（颜色，提示符，空格等），都是通过配置文件来设定的。通过配置文件我们可以设置下面一些参数：

- 颜色
- 改变输入和输出的提示符
- 每次打开 iPython 的时候可以执行一段代码，导入某些库
- 开启 iPython 扩展，比如 line_profiler中的%lprun
- 开启 jupyter 扩展
- 定义自己的魔法函数或别名

iPython shell的设置文件为`iPython_config.py`，通常位于主目录下的`.iPython/`文件夹内。每次打开 iPython 的时候，会默认加载`profile_default`中的`default`文件。在作者的 linux 系统下，默认的 iPython 配置文件为：

`/home/wesm/.iPython/profile_default/iPython_config.py`

初始化的话，可以在终端运行：

`iPython profile create`

如果我们有一个 iPython 配置是针对某个项目的，我们可以新建一个配置文件：

    iPython profile create secret_project

新创建的配置文件在 profile_secret_project目录下，我们可以按需要更改配置文件。然后按下面的方式启动 iPython：

![](http://images.iterate.site/blog/image/180803/l6hfm4Icj9.png?imageslim){ width=55% }

创建 jupyter 的配置文件：

    jupyter notebook --generate-config

配置文件会保存到主目录下的`.jupyter/jupyter_notebook_config.py`。按需求更改配置文件后，可以重命名：

    mv ~/.jupyter/jupyter_notebook_config.py ~/.jupyter/my_custom_config.py

启动 jupyter 的时候，添加对应的`--config`参数：

    jupyter notebook --config=~/.jupyter/my_custom_config.py


