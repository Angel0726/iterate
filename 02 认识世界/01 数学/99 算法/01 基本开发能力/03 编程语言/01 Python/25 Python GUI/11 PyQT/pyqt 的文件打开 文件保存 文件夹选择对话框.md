---
title: pyqt 的文件打开 文件保存 文件夹选择对话框
toc: true
date: 2019-02-02
---
首先导入 pyqt4 模块：


```py
import PyQt4.QtCore,PyQt4.QtGui
```


获取文件路径对话框：

```py
file_name = QFileDialog.getOpenFileName(self,"open file dialog","C:\Users\Administrator\Desktop","Txt files(*.txt)")
​##"open file Dialog "为文件对话框的标题，第三个是打开的默认路径，第四个是文件类型过滤器
```



这样，`file_name` 就保存了刚刚选择的文件的绝对路径。

保存文件对话框：

```py
file_path =  QFileDialog.getSaveFileName(self,"save file","C:\Users\Administrator\Desktop" ,"xj3dp files (*.xj3dp);;all files(*.*)")
```

`file_path` 即为文件即将保存的绝对路径。形参中的第二个为对话框标题，第三个为打开后的默认给路径，第四个为文件类型过滤器
选择文件夹对话框：

```py
dir_path = QFileDialog.getExistingDirectory(self,"choose directory","C:\Users\Administrator\Desktop")
```

`dir_path` 即为选择的文件夹的绝对路径，第二形参为对话框标题，第三个为对话框打开后默认的路径。
以上返回的都是`QString`类型的对象，若想不出现编码问题，建议用如下语句将 `QString` 转换为 `Python` 的 `string` 对象：


```py
str = unicode(your_path.toUtf8(), 'utf-8', 'ignore')
```




# 原文及引用

- [Python qt(pyqt)的文件打开、文件保存、文件夹选择对话框](https://blog.csdn.net/jirryzhang/article/details/59088964)
