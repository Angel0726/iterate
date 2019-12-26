---
title: 02 使用 python 解释器
toc: true
date: 2019-06-30
---
# 使用 Python 解释器


## 调用解释器

The Python interpreter is usually installed as "/usr/local/bin/Python3.6" on those machines where it is available; putting "/usr/local/bin" in your Unix shell's search path makes it possible to start it by typing the command:<span style="color:red;">怎么把 `/usr/local/bin` 放到 Unix 的 shell 的 search path 里面的？</span>

```sh
Python3.6
```

就能运行了(注意：在 Unix 系统中，Python 3.x解释器默认安装后的执行文件并不叫作 "Python"，这样才不会与同时安装的 Python 2.x冲突。<span style="color:red;">是这样吗？需要确认下。</span>) 。安装时可以选择安装目录，所以解释器也可能在别的地方；
可以问问你身边的 Python 大牛，或者你的系统管理员。（比如
"/usr/local/Python" 也是比较常用的备选路径）

On Windows machines, the Python installation is usually placed in
"C:\Python36", though you can change this when you're running the
installer.  To add this directory to your path,  you can type the
following command into the command prompt in a DOS box:

```sh
set path=%path%;C:\Python36
```

<span style="color:red;">哇塞！之前不知道这个 windows 的环境变量的 path 中添加新的路径的时候可以这样添加。</span>

<span style="color:red;">但是为什么我通过 管理员的 cmd 执行这个之后，没有对系统变量进行修改呢？查了下好像这个只是修改的 cmd 里面的一个副本，那么要怎么使这个修改生效到整个系统变量里面呢？</span>

在主提示符中输入文件结束字符（在 Unix 系统中是 "Control-D"，Windows 系
统中是 "Control-Z"）就退出解释器并返回退出状态为 0。如果这样不管用，你
还可以写这个命令退出："quit()"。

<center>

![](http://images.iterate.site/blog/image/20190630/farplXXTuFDN.png?imageslim){ width=55% }

</center>

<span style="color:red;">真的不错，之前我都是使用的 quit() 嗯，虽然知道 Ctl+Z 但是一直么有怎么用过。嗯，Ctl+D 可以在 Unix 上使用。好的。</span>


解释器的行编辑功能也包括交互式编辑，在支持 readline 的系统中，可以回看
历史命令，也有 "Tab" 代码补全功能。要想快速检查是否支持行编辑，在出现
提示符后，按键盘 "Control-P"。如果它“哔”了一声，它就是支持行编辑的；关
于按键的详细介绍请看附录 交互式编辑和编辑历史。如果什么都没发生，或者
显示出 "^P"，那么就不支持行编辑功能；你只能用退格（"Backspace"）键从当
前行中删除字符。<span style="color:red;">好像 Ctl+P 没有效果吧？</span>

解释器运行的时候有点像 Unix 命令行：在一个标准输入 tty 设备上调用，它
能交互式地读取和执行命令；调用时提供文件名参数，或者有个文件重定向到标
准输入的话，它就会读取和执行文件中的 *脚本*。<span style="color:red;">嗯，tty 是什么来着忘记了。</span>

另一种启动解释器的方式是 "Python -c command [arg] ..."，其中 *command*
要换成想执行的指令，就像命令行的 "-c" 选项。由于 Python 代码中经常会包
含对终端来说比较特殊的字符，通常情况下都建议用英文单引号把 *command*
括起来。<span style="color:red;">嗯，很少使用这种写法。</span> <span style="color:red;">为什么我尝试了 `Python -c 'print("aaa")'` 之后没有反应呢？是要怎么写？</span>

有些 Python 模块也可以作为脚本使用。可以这样输入："Python -m module [arg] ..."，这会执行 *module* 的源文件，就跟你在命令行把路径写全了一样。<span style="color:red;">这是什么意思？执行 module 吗？还是说执行 module 里面的 main 函数？哪些 modules 需要这样来执行？</span>

在运行脚本的时候，有时可能也会需要在运行后进入交互模式。这种时候在文件
参数前，加上选项 "-i" 就可以了。

<center>

![](http://images.iterate.site/blog/image/20190630/U74pu22OQovJ.png?imageslim){ width=55% }
</center>

<span style="color:red;">是的，是可以的。之前没有这么用过，真不错。</span>


关于所有的命令行选项，请参考 命令行与环境。


### 传入参数

如果可能的话，解释器会读取命令行参数，转化为字符串列表存入 "sys" 模块
中的 "argv" 变量中。执行命令 "import sys" 你可以导入这个模块并访问这个
列表。这个列表最少也会有一个元素；如果没有给定输入参数，"sys.argv[0]"
就是个空字符串。如果脚本名是标准输入，"sys.argv[0]" 就是 "'-'"。使用
"-c" *command* 时，"sys.argv[0]" 就会是 "'-c'"。如果使用选项 "-m"
*module*，"sys.argv[0]" 就是包含目录的模块全名。在 "-c" *command* 或
"-m" *module* 之后的选项不会被解释器处理，而会直接留在 "sys.argv" 中给
命令或模块来处理。 <span style="color:red;">嗯，参数留给 sys.argv 来处理。</span>


### 交互模式

在终端（tty）输入并执行指令时，我们说解释器是运行在 *交互模式（
interactive mode）*。在这种模式中，它会显示 *主提示符（primary prompt
）*，提示输入下一条指令，通常用三个大于号（">>>"）表示；连续输入行的时
候，它会显示 *次要提示符*，默认是三个点（"..."）。进入解释器时，它会先
显示欢迎信息、版本信息、版权声明，然后就会出现提示符：

```py
$ Python3.6
Python 3.6 (default, Sep 16 2015, 09:25:04)
[GCC 4.8.2] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>>
```

多行指令需要在连续的多行中输入。比如，以 "if" 为例：

```
>>> the_world_is_flat = True
>>> if the_world_is_flat:
...     print("Be careful not to fall off!")
...
```

Be careful not to fall off(减少)!

有关交互模式的更多内容，请参考 交互模式。


## 解释器的运行环境


### 源文件的字符编码

默认情况下，Python 源码文件以 UTF-8 编码方式处理。在这种编码方式中，世
界上大多数语言的字符都可以同时用于字符串字面值、变量或函数名称以及注释
中——尽管标准库中只用常规的 ASCII 字符作为变量或函数名，而且任何可移植
的代码都应该遵守此约定。<span style="color:red;">嗯，公开的最好全使用 ASCII 字符</span>要正确显示这些字符，你的编辑器必须能识别 UTF-8
编码，而且必须使用能支持打开的文件中所有字符的字体。

如果不使用默认编码，要声明文件所使用的编码，文件的 *第一* 行要写成特殊
的注释。语法如下所示：

```py
# -*- coding: encoding -*-
```

其中 *encoding* 可以是 Python 支持的任意一种 "codecs"。<span style="color:red;">嗯，很少使用这个，不过放到 linux 系统的时候最好通过这句话明确是 utf-8 的。不过也不是特别清楚为什么有的时候不加这句话也行。</span>

比如，要声明使用 Windows-1252 编码，你的源码文件要写成：

```py
# -*- coding: cp1252 -*-
```

关于 *第一行* 规则的一种例外情况是，源码以 UNIX "shebang" 行 开头。这
种情况下，编码声明就要写在文件的第二行。例如：<span style="color:red;">什么是 UNIX shebang line ？而且，如果我的 Python 的路径不是 `/usr/bin/env` 我需要写一个正确的路径吗？而且如果是这样写死的话，想换个虚拟环境不是很麻烦？换到别的电脑上也很麻烦吧？</span>

```py
#!/usr/bin/env Python3
# -*- coding: cp1252 -*-
```



# 相关

- [Python docs](https://docs.Python.org/zh-cn/3.6/)
