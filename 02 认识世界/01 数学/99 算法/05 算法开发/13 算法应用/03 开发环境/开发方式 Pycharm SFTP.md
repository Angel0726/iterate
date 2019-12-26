---
title: 开发方式 Pycharm SFTP
toc: true
date: 2019-10-01
---
PyCharm 利用SFTP远程炼丹


刚来现在这个团队的一段时间，几乎每天都在和同事安利PyCharm的SFTP功能。这种远程编辑功能几乎成了各个IDE和文本编辑器的标配，个人认为是比samba的那种文件夹共享的方式要好的。当然，Vim大神们可以掠过这段话。在PyCharm上配置SFTP并使用远程的解释器还是有一些配置工作需要做的，第一次接触的话会因为某些细节的遗漏而配置不成功。

总结一下SFTP至少有两点要注意：

- 配置远程连接信息。
- 设置本地和远程路径的映射。

除了SFTP的映射外，PyCharm还支持直接使用远程的解释器，这样就多了一步：

- 设置远程解释器。

## SFTP配置

配置过程如下：

- Flie->Setting->Build,Exception,Deployment->Deployment
- 点击加号Add Server，输入名字，type选择STFP。

![img](https://pic3.zhimg.com/80/v2-a04a374a3601d4a074b06c3ea7d2800e_hd.jpg)

- 在新建的配置界面中输入SFTP host、Port、Root path、User name、Password等。需要注意的是，root path是可以自动检测的，在输入了其他部分后，点击test sftp connection来确认地址和用户信息是否正确，之后点击autodetect就可以自动补全root path。

![img](https://pic2.zhimg.com/80/v2-87abc7b0486da5ea10f4399cb9f5adb9_hd.jpg)

接下来还需要配置Mapping，在配置界面上部导航栏进入mapping，选择localpath和deployment path。

![img](https://pic3.zhimg.com/80/v2-64dc1395b39da1069593a10083fc0afe_hd.jpg)

自此，SFTP就设置完毕了，总结一下就几步：

1. 打开配置界面。
2. 设置连接信息。
3. 设置文件夹映射。

接下来，再讲下如何设置远程解释器。

## 远程解释器配置

如果说SFTP是广大现代IDE和文本编辑器的标配，远程解释器可能就是PyCharm令人愉悦的独门绝技了。配置好了这个后，才真正做到了本地coding，远程执行，不用每次都ssh到服务器上，vim+命令行执行了。当然，实际情况比较难以做到完全不用ssh，这个暂且不提。远程解释器完整配置如下：

File->Settings->Project:<project name>->Project Interpreter。

在配置栏右端配置按钮处点击add新建，在最新的2018版本中，配置界面变成这样，我一般会create一个copy。

![img](https://pic3.zhimg.com/80/v2-8da7ac35fba58040e6da8af6e642718a_hd.jpg)

在新建了一个copy选项后方可选择下一步，这是需要设置远程解释器的路径和远程工程的执行路径。如果远程解释器是在虚拟环境中的，需要直接指定到虚拟环境的解释器。

![img](https://pic1.zhimg.com/80/v2-205806b30b4d40c898a2030732ab1070_hd.jpg)

点击完成即可，在这段时间，PyCharm会将解释器的环境做一个同步，并装上一些PyCharm需要的东西。

这时，新建一个Run/Debug Configurations，添加一个Python的配置文件，选择和本地和远程对应的py入口文件，并选定好相应的远程解释器，就可以远程执行脚本了。记得在编辑了本地的文件后需要同步一份到远程服务器上。

![img](https://pic4.zhimg.com/80/v2-42b83a7e553f662b4aaf78ce255a0db7_hd.jpg)

有时候我们ssh到远程时，执行脚本是带参数的，比如会通过参数去选择执行的GPU，就像这样：

```text
CUDA_VISIBLE_DEVICES='0' python demo.py --gpu=0
```

这时就需要在这个配置界面里指定，在Enviroment variablies里填写CUDA_VISIBLE_DEVICES='0'，在Paramters里填写--gpu=0。这里的配置和命令行只是个demo，不具有实际意义。



## 小结与预告。

这里的配置虽然繁琐但也并不复杂，不过现如今炼丹的团队应该都是用python，这个配置步骤估计还用的挺频繁的。另外，我想在下一篇文章中讲个黑科技，利用bash on windows搭配这个，可以完成在win平台完成一些Linux强依赖的深度学习框架的调试，由于篇幅所限，我在下一篇中给大家介绍一下这个技巧。如果之后还有时间，我把PyCharm的远程deBug的模块pydevd的配置也给大家过一下。





丁果：炼丹工具集-jupyter notebook 远程连接zhuanlan.zhihu.com![图标](https://pic2.zhimg.com/v2-865f80ef1e0b2e1bd68d48834e1c14a1_180x120.jpg)

python杂七杂八的使用经验zhuanlan.zhihu.com![图标](https://pic4.zhimg.com/4b70deef7_ipico.jpg)



# 相关

-[炼丹工具集-PyCharm 利用SFTP远程炼丹](https://zhuanlan.zhihu.com/p/37361332)
