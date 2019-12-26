---
title: 01 Pip 常用指令
toc: true
date: 2019-10-13
---
# 可以补充进来的

- 补充一些例子


# pip 常用指令

基本的命令解释，如下图：


<center>

![mark](http://images.iterate.site/blog/image/20191011/hVuvLhwOWgq1.png?imageslim)

</center>

```py
# 安装 pip
sudo easy_install pip
# 升级pip
pip install -U pip



# 列出已安装的包
pip freeze 或者 pip list

# 在线安装 通过使用== >= <= > <来指定版本，不写则安装最新版
pip install <包名>
# 安装本地安装包
pip install <目录>/<文件名>
# 搜索并安装本地安装包
pip install --no-index -f=<目录>/ <包名>


# 卸载包
pip uninstall <包名>

# 升级包
pip install -U <包名>


# 显示包所在的目录
pip show -f <包名>

# 搜索包
pip search <搜索关键字>


# 查询可升级的包
pip list -o


# 下载包而不安装
pip install <包名> -d <目录>

# 打包
pip wheel <包名>
```

<span style="color:red;">这个 pip wheel 会打包成什么？</span>



# 相关

- [pip常用命令（转载）](https://www.cnblogs.com/xueweihan/p/4981704.html)
