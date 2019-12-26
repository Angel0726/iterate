---
title: Linux_Package has no installation candidate
toc: true
date: 2019-10-18
---
# Linux_Package has no installation candidate

今天在安装软件的时候出现了Package has no installation candidate的问题，如：

\# apt-get install <packagename>

Reading package lists... Done
Building dependency tree... Done
Package aptitude is not available, but is referred to by another package.
This may mean that the package is missing, has been obsoleted, or
is only available from another source
E: Package <packagename> has no installation candidate

解决方法如下：

\# apt-get update
\# apt-get upgrade
\# apt-get install <packagename>

这样就可以正常使用apt-get了～


有些模块之间具有依赖性, 若make test过程中,产生异常可于make install后,重新执行perl Makefile.PL命令,此时可看到安装异常的原因.若具有模块依赖,则会提示需要安装相应模块.

当perl的必须模块以及数据库的DBD都安装成功后,再次执行./checksetup.pl文件,查看perl模块的安装情况,若必须的perl模块都安装成功后,则会提示编辑/bugzilla/目录下刚生成的的localconfig文件, 使用vi编辑该文件,修改该文件中的2个参数的值:

a. $index.html='0' 改为 $index.html='1', 这样会生成一个index.html文件,该文件指向index.cgi.
b. 把$db_pass=''的空字符改为你当初创建bugs用户时为其分配的密码.

保存修改后退出,再次执行./checksetup.pl文件,此时将创建bugs数据库以及数据库中的表格,同时提示输入管理员的用户名, 真实姓名, 口令是什么. 自此bugzilla的配置完成.

注:提示输入管理员的用户必须使用邮箱名称,如：test@163.com, 这是bugzilla的默认规定.

最后使用浏览器打开bugzilla地址,进入第一次登陆界面.

如果出现提示没有权限访问bugzilla的话，则说明bugzilla目录权限需要重新设置,可使用如下命令修改目录权限: chown -R apache.apche <Bugzilla目录名>,然后重新访问就能够了.



# 相关

- [安装过程中出现的问题](https://wenku.baidu.com/view/e50a228d55270722192ef7af.html)
