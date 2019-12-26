---
title: 06 PyCharm 常用设置
toc: true
date: 2019-11-17
---

# PyCharm 中的设置

PyCharm 中的设置是可以导入和导出的，file>export settings可以保存当前PyCharm中的设置为jar文件，重装时可以直接import settings>jar文件，就不用重复配置了。<span style="color:red;">确认下。</span>


## 去掉波浪线


刚开始使用的时候，PyCharm 上是由很多波浪线的。


可以这样去掉：

`Editor->Color Scheme->General` 然后找 `Errors and Warnings->Typo` ，将 `effects` 去除勾选，然后再找 `Weak Warning`，将 `effects` 去除勾选。

# PyCharm 中将 tab 键设置成 4 个空格


`File->Setting->Editor->Code Style->Python`

![](http://images.iterate.site/blog/image/181017/BLI87hc3K9.png?imageslim){ width=55% }



## file -> Setting ->Editor

1. 设置Python自动引入包，要先在 >general > autoimport -> Python :show popup

快捷键：Alt + Enter: 自动添加包

2. “代码自动完成”时间延时设置

> Code Completion   -> Auto code completion in (ms):0  -> Autopopup in (ms):500

3. PyCharm中默认是不能用Ctrl+滚轮改变字体大小的，可以在 >Mouse中设置

4. 显示“行号”与“空白字符”

> Appearance  -> 勾选“Show line numbers”、“Show whitespaces”、“Show method separators”

5. 设置编辑器“颜色与字体”主题

> Colors & Fonts -> Scheme name -> 选择"monokai"“Darcula”

说明：先选择“monokai”，再“Save As”为"monokai-pipi"，因为默认的主题是“只读的”，一些字体大小颜色什么的都不能修改，拷贝一份后方可修改！

修改字体大小

> Colors & Fonts -> Font -> Size -> 设置为“14”

6. 设置缩进符为制表符“Tab”

File -> Default Settings -> Code Style

-> General -> 勾选“Use tab character”

-> Python -> 勾选“Use tab character”

-> 其他的语言代码同理设置

7. 去掉默认折叠

> Code Folding -> Collapse by default -> 全部去掉勾选

8. PyCharm默认是自动保存的，习惯自己按ctrl + s  的可以进行如下设置：

> General -> Synchronization -> Save files on frame deactivation  和 Save files automatically if application is idle for .. sec 的勾去掉

> Editor Tabs -> Mark modified tabs with asterisk 打上勾

9.>file and code template>Python scripts

```
#!/usr/bin/env Python
```

```
# -*- coding: utf-8 -*-
```

```
__title__ = '$Package_name'
__author__ = '$USER'
__mtime__ = '$DATE'

# code is far away from bugs with the god animal protecting

    I love animals. They taste delicious.
              ┏┓      ┏┓
            ┏┛┻━━━┛┻┓
            ┃      ☃      ┃
            ┃  ┳┛  ┗┳  ┃
            ┃      ┻      ┃
            ┗━┓      ┏━┛
                ┃      ┗━━━┓
                ┃  神兽保佑    ┣┓
                ┃　永无BUG！   ┏┛
                ┗┓┓┏━┳┓┏┛
                  ┃┫┫  ┃┫┫
                  ┗┻┛  ┗┻┛
```

10. Python文件默认编码

File Encodings> IDE Encoding: UTF-8;Project Encoding: UTF-8;

11. 代码自动整理设置


<center>

![mark](http://images.iterate.site/blog/image/20191109/wd9RMfmKw38f.png?imageslim)

</center>

这里line breaks去掉√，否则bar, 和baz会分开在不同行，不好看。


## File -> Settings -> appearance

1. 修改IDE快捷键方案

> Keymap

execute selection in console : add keymap > ctrl + enter

系统自带了好几种快捷键方案，下拉框中有如“defaul”,“Visual Studio”，在查找Bug时非常有用，“NetBeans 6.5”，“Default for GNOME”等等可选项，

2. 设置IDE皮肤主题

> Theme -> 选择“Alloy.IDEA Theme”

 或者在setting中搜索theme可以改变主题，所有配色统一改变

File > settings > build.excution

每次打开Python控制台时自动执行代码

> console > pyconsole

```
import sys # print('Python %s on %s' % (sys.version, sys.platform)) sys.path.extend([WORKING_DIR_AND_Python_PATHS]) import os print('current workdirectory : ', os.getcwd() ) import numpy as np import scipy as sp import matplotlib as mpl
```

如果安装了iPython，则在pyconsole中使用更强大的iPython

> console

选中use iPython if available

这样每次打开pyconsole就会打开iPython

Note: 在virtualenv中安装iPython: (ubuntu_env) pika:/media/pika/files/mine/Python_workspace/ubuntu_env$pip install iPython

# 相关

- [PyCharm快捷键、常用设置、配置管理](https://blog.csdn.net/pipisorry/article/details/39909057)
- [消除 PyCharm 中满屏的波浪线](https://blog.csdn.net/gedongya/article/details/52300135)
- [PyCharm中将 tab 键设置成 4 个空格](https://blog.csdn.net/u011731378/article/details/77637339)
