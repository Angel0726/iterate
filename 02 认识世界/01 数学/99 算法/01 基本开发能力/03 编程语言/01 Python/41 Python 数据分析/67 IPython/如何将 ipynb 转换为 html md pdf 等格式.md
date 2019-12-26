---
title: 如何将 ipynb 转换为 html md pdf 等格式
toc: true
date: 2019-06-27
---
# 如何将 ipynb 转换为 html md pdf 等格式

jupyter notebook 的后缀名是 .ipynb 的文件如何转换成 html，md，pdf 等格式呢？

## ipynb转为 html 格式

在 Ubuntu 命令行输入：

```
jupyter nbconvert --to html notebook.ipynb
```

另外，jupyter 提供了一些命令，可以对生成的 html 格式进行配置：

```
jupyter nbconvert --to html --template full notebook.ipynb
```

这是默认配置，提供完整的静态 html 格式，交互性更强。

```
jupyter nbconvert --to html --template basic notebook.ipynb
```


简化的 html，用于嵌入网页、博客等，这不包括 html 标题。

## ipynb转换为 md 格式

在 Ubuntu 命令行输入：

```
jupyter nbconvert --to md notebook.ipynb
```

简单的 Markdown 格式输出，cell单元不受影响，代码 cell 缩进 4 个空格。


Jupyter 可以批量转换 ：

```
jupyter nbconvert --to markdown 1.*.ipynb
```

将以 `1.` 开头的 ipynb 文件转化成了 md 格式。

<span style="color:red;">嗯，批量转换。</span>

## ipynb转换为 tex 格式

在 Ubuntu 命令行输入：

```
jupyter nbconvert --to letex notebook.ipynb
```

Letex 导出格式，生成后缀名为 NOTEBOOK_NAME.tex 的文件。jupyter 提供的额外模板配置为：

```
jupyter nbconvert --to letex -template article notebook.ipynb
```

这是默认配置，Latex 文章。

```
jupyter nbconvert --to letex -template report notebook.ipynb
```

Latex报告，提供目录和章节。

```
jupyter nbconvert --to letex -template basic notebook.ipynb
```

最基本的 Latex 输出，经常用来自定义配置。

## iPython 转换为 pdf 格式

在 Ubuntu 命令行输入：

```
jupyter nbconvert --to pdf notebook.ipynb
```

转换为 pdf 格式分模板配置与 latex 配置是一样的。但是直接转换为 pdf 格式经常会出现下列错误：

<center>

![](http://images.iterate.site/blog/image/20190627/XmX8LIJgz7Pw.png?imageslim){ width=55% }
</center>

该错误提示没有安装 xelatex。所以，我们需要提前安装 xelatex，方法是安装 texLive 套装：

```bash
sudo apt-get install texlive-full
```

texlive-full 的安装包有点大，约 1G 多。

简单的转换方法

ipynb 转换为 html、md、pdf 等格式，还有另一种更简单的方法：在 jupyter notebook 中，选择 File->Download as，直接选择需要转换的格式就可以了。需要注意的是，转换为 pdf 格式之前，同样要保证已经安装了 xelatex。

<center>

![](http://images.iterate.site/blog/image/20190627/1vrqX4dsj80H.png?imageslim){ width=55% }
</center>


# 相关


- [如何将 ipynb 转换为 html，md，pdf等格式](https://blog.csdn.net/red_stone1/article/details/73380517)
- [Converting notebooks to other formats](https://iPython.org/iPython-doc/3/notebook/nbconvert.html)
- [Markdown+Pandoc 最佳写作拍档 (mailp.in)](http://www.tuicool.com/articles/zQrQbaU)
