---
title: Tensorflow 的安装
toc: true
date: 2018-06-12 13:41:37
---
## 可以补充进来的

* 要补充 GPU 版本的安装和 linux 下的安装。



# ORIGIN


之前我在安装 tensorflow 的时候由于已经安装了 pycharm，而且电脑只有 CPU，因此使用的比较简单的方法，但是万一只能在 pip 或者 docker 情况下安装呢？因此记一下，


## 要点：




### 1.安装的方法


我自己的安装方法，（前提：已经安装了 pycharm）：

在安装好 Anaconda 之后，直接在 pycharm 中像普通的包一样在 Setting 的 Project Interpreter里面搜索安装就行。

别的安装情况：

参考：[使用 Pip, Docker, Virtualenv, Anaconda 或 源码编译的方法安装 TensorFlow.](http://wiki.jikexueyuan.com/project/tensorflow-zh/get_started/os_setup.html)


### 2.安装完之后的简单测试


```python
import tensorflow as tf

hello = tf.constant('hello , tensorflow')
sess = tf.Session()
print(sess.run(hello))

a = tf.constant(10)
b = tf.constant(32)
print(sess.run(a + b))
```

输出：


    b'hello , tensorflow'
    42





# 相关

- [TensorFlow在 windows 下的安装](https://blog.csdn.net/lxy_2011/article/details/79181990)
