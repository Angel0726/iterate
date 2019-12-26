---
title: 06 PyCharm 路径与导入包
toc: true
date: 2019-11-17
---


# Python 导入包出现的问题

## 多个文件之间有相互依赖的关系

cannot find declaration to go to||CTRL+也不起作用
同目录下，当多个文件之间有相互依赖的关系的时候，import无法识别自己写的模块，PyCharm中提示No Module；PyCharm import不能转到。

如from models.base_model import BaseModel

解决步骤：

(1). 打开File--> Setting—> 打开 Console下的Python Console，把选项（Add source roots to PythonPAT）点击勾选上

(2). 右键点击自己的工作空间文件夹（对应就是models的上级目录，假设是src），找到Mark Directory as 选择Source Root！

- [import时无法识别自己写的模块](https://blog.csdn.net/enter89/article/details/79960230)

## pycharm中进行python包管理


PyCharm中的项目中可以包含package、目录（目录名可以有空格）、等等

目录的某个包中的某个py文件要调用另一个py文件中的函数，首先要将目录设置为source root，这样才能从包中至上至上正确引入函数，否则怎么引入都出错：

SystemError: Parent module '' not loaded, cannot perform relative import

Note:目录 > 右键 > make directory as > source root

## Python脚本解释路径

ctrl + shift + f10 / f10 执行Python脚本时

当前工作目录cwd为run/debug configurations 中的working directory

可在edit configurations > project or defaults中配置

## console执行路径和当前工作目录

Python console中执行时

cwd为File > settings > build.excution > console > pyconsole中的working directory

并可在其中配置

## PyCharm配置os.environ环境

PyCharm中os.environ不能读取到terminal中的系统环境变量

PyCharm中os.environ不能读取.bashrc参数

使用PyCharm，无论在Python console还是在module中使用os.environ返回的dict中都没有~/.bashrc中的设置的变量，但是有/etc/profile中的变量配置。然而在terminal中使用Python，os.environ却可以获取~/.bashrc的内容。

### 解决方法1：

在~/.bashrc中设置的系统环境只能在terminal shell下运行spark程序才有效，因为.bashrc is only read for interactive shells.

如果要在当前用户整个系统中都有效（包括PyCharm等等IDE），就应该将系统环境变量设置在~/.profile文件中。如果是设置所有用户整个系统，修改/etc/profile或者/etc/environment吧。

如SPARK_HOME的设置[Spark：相关错误总结 ]

### 解决方法2：在代码中设置，这样不管环境有没有问题了

```
# spark environment settings

import sys, os
os.environ['SPARK_HOME'] = conf.get(SECTION, 'SPARK_HOME')
sys.path.append(os.path.join(conf.get(SECTION, 'SPARK_HOME'), 'Python'))
os.environ["PYSPARK_Python"] = conf.get(SECTION, 'PYSPARK_Python')
os.environ['SPARK_LOCAL_IP'] = conf.get(SECTION, 'SPARK_LOCAL_IP')
os.environ['JAVA_HOME'] = conf.get(SECTION, 'JAVA_HOME')
os.environ['PythonPATH'] = '$SPARK_HOME/Python/lib/py4j-0.10.3-src.zip:$PythonPATH'
```

## PyCharm配置第三方库代码自动提示

参考 [Spark安装和配置](https://blog.csdn.net/pipisorry/article/details/50924395)

# 相关

- [PyCharm快捷键、常用设置、配置管理](https://blog.csdn.net/pipisorry/article/details/39909057)
