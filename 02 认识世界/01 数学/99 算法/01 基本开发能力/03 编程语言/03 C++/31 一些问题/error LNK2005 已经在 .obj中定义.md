---
title: error LNK2005 已经在 .obj中定义
toc: true
date: 2019-10-19
---
# error LNK2005: 已经在*.obj中定义




编程中经常能遇到LNK2005错误——重复定义错误，其实LNK2005错误并不是一个很难解决的错误，弄清楚它形成的原因，就可以轻松解决它了。

**造成LNK2005错误主要有以下几种情况：**


## 重复定义全局变量。可能存在两种情况：


### 对于一些初学编程的程序员，有时候会以为需要使用全局变量的地方就可以使用定义申明一下。其实这是错误的，全局变量是针对整个工程的。

正确的应该是在一个CPP文件中定义如下：

```
int   g_Test;
```

那么在使用的CPP文件中就应该使用：

```
extern   int   g_Test
```

即可，如果还是使用int   g_Test，那么就会产生LNK2005错误，一般错误错误信息类似：

```
*.obj   error   LNK2005   int   book   c？   already   defined   in   *.obj
```

切记的就是不能给变量赋值否则还是会有LNK2005错误。

这里需要的是“声明”，不是“定义”！根据C++标准的规定，一个变量是声明，必须同时满足两个条件，否则就是定义：



1. 声明必须使用extern关键字
2. 不能给变量赋初值

所以，下面的是声明:

```
*extern   int   a;  　　
```

下面的是定义

```
*int   a;
int   a   =   0;
extern   int   a   =0;
```



### 对于那么编程不是那么严谨的程序员，总是在需要使用变量的文件中随意定义一个全局变量，并且对于变量名也不予考虑，这也往往容易造成变量名重复，而造成LNK2005错误。

## 头文件的包含重复。

往往需要包含的头文件中含有变量、函数、类的定义，在其它使用的地方又不得不多次包含之，如果头文件中没有相关的宏等防止重复链接的措施，那么就会产生LNK2005错误。解决办法是在需要包含的头文件中做类似的处理：

```
#ifndef   MY_H_FILE       //如果没有定义这个宏
#define   MY_H_FILE       //定义这个宏
…….       //头文件主体内容
…….
#endif
```

上面是使用宏来做的，也可以使用预编译来做，在头文件中加入：


```
#pragma   once
//头文件主体
```


## 使用第三方的库造成的。

　　这种情况主要是C运行期函数库和MFC的库冲突造成的。具体的办法就是将那个提示出错的库放到另外一个库的前面。另外选择不同的C函数库，可能会引起这个错误。微软和C有两种C运行期函数库，一种是普通的函数库：LIBC.LIB，不支持多线程。另外一种是支持多线程的：msvcrt.lib。如果一个工程里，这两种函数库混合使用，可能会引起这个错误，一般情况下它需要MFC的库先于C运行期函数库被链接，因此建议使用支持多线程的msvcrt.lib。所以在使用第三方的库之前首先要知道它链接的是什么库，否则就可能造成LNK2005错误。如果不得不使用第三方的库，可以尝试按下面所说的方法修改，但不能保证一定能解决问题，前两种方法是微软提供的：

  A、选择VC菜单Project->Settings->Link->Catagory选择Input，再在Ignore   libraries   的Edit栏中填入你需要忽略的库，如：Nafxcwd.lib;Libcmtd.lib。然后在Object/library   Modules的Edit栏中填入正确的库的顺序，这里需要你能确定什么是正确的顺序，呵呵，God   bless   you！
  B、选择VC菜单Project->Settings->Link页，然后在Project   Options的Edit栏中输入/verbose:lib，这样就可以在编译链接程序过程中在输出窗口看到链接的顺序了。
  C、选择VC菜单Project->Settings->C/C++页，Catagory选择Code   Generation后再在User   Runtime   libraray中选择MultiThread   DLL等其他库，逐一尝试。



# 相关

- [error:LNK2005 已经在*.obj中定义](https://www.cnblogs.com/MuyouSome/p/3332699.html)
