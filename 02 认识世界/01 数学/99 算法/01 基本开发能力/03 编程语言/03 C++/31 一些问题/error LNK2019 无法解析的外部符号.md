---
title: error LNK2019 无法解析的外部符号
toc: true
date: 2019-10-19
---
# error LNK2019: 无法解析的外部符号 __imp_GetUserObjectInformationW，该符号在函数 OPENSSL_isservice 中被引用


关键是要知道有些API是可以在MSDN上查找对应的lib 和头文件和dll


```
错误 18 error LNK2019: 无法解析的外部符号 __imp_GetUserObjectInformationW，该符号在函数 OPENSSL_isservice 中被引用 E:\FileRecv\SimpleAuthenticator(1)\SimpleAuthenticator\SimpleAuthenticator\libeay64.lib(cryptlib.obj) SimpleAuthenticator
错误 19 error LNK2019: 无法解析的外部符号 __imp_GetProcessWindowStation，该符号在函数 OPENSSL_isservice 中被引用 E:\FileRecv\SimpleAuthenticator(1)\SimpleAuthenticator\SimpleAuthenticator\libeay64.lib(cryptlib.obj) SimpleAuthenticator
错误 20 error LNK2019: 无法解析的外部符号 __imp_GetDesktopWindow，该符号在函数 OPENSSL_isservice 中被引用 E:\FileRecv\SimpleAuthenticator(1)\SimpleAuthenticator\SimpleAuthenticator\libeay64.lib(cryptlib.obj) SimpleAuthenticator
错误 21 error LNK2019: 无法解析的外部符号 __imp_MessageBoxW，该符号在函数 OPENSSL_showfatal 中被引用 E:\FileRecv\SimpleAuthenticator(1)\SimpleAuthenticator\SimpleAuthenticator\libeay64.lib(cryptlib.obj) SimpleAuthenticator
错误 22 error LNK2019: 无法解析的外部符号 __imp_DeregisterEventSource，该符号在函数 OPENSSL_showfatal 中被引用 E:\FileRecv\SimpleAuthenticator(1)\SimpleAuthenticator\SimpleAuthenticator\libeay64.lib(cryptlib.obj) SimpleAuthenticator
错误 23 error LNK2019: 无法解析的外部符号 __imp_ReportEventW，该符号在函数 OPENSSL_showfatal 中被引用 E:\FileRecv\SimpleAuthenticator(1)\SimpleAuthenticator\SimpleAuthenticator\libeay64.lib(cryptlib.obj) SimpleAuthenticator
错误 24 error LNK2019: 无法解析的外部符号 __imp_RegisterEventSourceW，该符号在函数 OPENSSL_showfatal 中被引用 E:\FileRecv\SimpleAuthenticator(1)\SimpleAuthenticator\SimpleAuthenticator\libeay64.lib(cryptlib.obj) SimpleAuthenticator
错误 25 error LNK2019: 无法解析的外部符号 __imp_DeleteDC，该符号在函数 readscreen 中被引用 E:\FileRecv\SimpleAuthenticator(1)\SimpleAuthenticator\SimpleAuthenticator\libeay64.lib(rand_win.obj) SimpleAuthenticator
错误 26 error LNK2019: 无法解析的外部符号 __imp_DeleteObject，该符号在函数 readscreen 中被引用 E:\FileRecv\SimpleAuthenticator(1)\SimpleAuthenticator\SimpleAuthenticator\libeay64.lib(rand_win.obj) SimpleAuthenticator
错误 27 error LNK2019: 无法解析的外部符号 __imp_GetBitmapBits，该符号在函数 readscreen 中被引用 E:\FileRecv\SimpleAuthenticator(1)\SimpleAuthenticator\SimpleAuthenticator\libeay64.lib(rand_win.obj) SimpleAuthenticator
错误 28 error LNK2019: 无法解析的外部符号 __imp_BitBlt，该符号在函数 readscreen 中被引用 E:\FileRecv\SimpleAuthenticator(1)\SimpleAuthenticator\SimpleAuthenticator\libeay64.lib(rand_win.obj) SimpleAuthenticator
错误 29 error LNK2019: 无法解析的外部符号 __imp_GetObjectW，该符号在函数 readscreen 中被引用 E:\FileRecv\SimpleAuthenticator(1)\SimpleAuthenticator\SimpleAuthenticator\libeay64.lib(rand_win.obj) SimpleAuthenticator
错误 30 error LNK2019: 无法解析的外部符号 __imp_SelectObject，该符号在函数 readscreen 中被引用 E:\FileRecv\SimpleAuthenticator(1)\SimpleAuthenticator\SimpleAuthenticator\libeay64.lib(rand_win.obj) SimpleAuthenticator
错误 31 error LNK2019: 无法解析的外部符号 __imp_CreateCompatibleBitmap，该符号在函数 readscreen 中被引用 E:\FileRecv\SimpleAuthenticator(1)\SimpleAuthenticator\SimpleAuthenticator\libeay64.lib(rand_win.obj) SimpleAuthenticator
错误 32 error LNK2019: 无法解析的外部符号 __imp_GetDeviceCaps，该符号在函数 readscreen 中被引用 E:\FileRecv\SimpleAuthenticator(1)\SimpleAuthenticator\SimpleAuthenticator\libeay64.lib(rand_win.obj) SimpleAuthenticator
错误 33 error LNK2019: 无法解析的外部符号 __imp_CreateCompatibleDC，该符号在函数 readscreen 中被引用 E:\FileRecv\SimpleAuthenticator(1)\SimpleAuthenticator\SimpleAuthenticator\libeay64.lib(rand_win.obj) SimpleAuthenticator
错误 34 error LNK2019: 无法解析的外部符号 __imp_CreateDCW，该符号在函数 readscreen 中被引用 E:\FileRecv\SimpleAuthenticator(1)\SimpleAuthenticator\SimpleAuthenticator\libeay64.lib(rand_win.obj) SimpleAuthenticator
错误 35 error LNK1120: 17 个无法解析的外部命令 E:\FileRecv\SimpleAuthenticator(1)\SimpleAuthenticator\x64\Debug\SimpleAuthenticator.dll SimpleAuthenticator
```



参考MSDN：https://msdn.microsoft.com/en-us/library/ms683238(VS.85).aspx

## Requirements

|                              |                                                                          |
| ---------------------------- | ------------------------------------------------------------------------ |
| **Minimum supported client** | Windows 2000 Professional [desktop apps only]                            |
| **Minimum supported server** | Windows 2000 Server [desktop apps only]                                  |
| **Target Platform**          | Windows                                                                  |
| **Header**                   | winuser.h (include Windows.h)                                            |
| **Library**                  | User32.lib                                                               |
| **DLL**                      | User32.dll                                                               |
| Unicode and ANSI names       | GetUserObjectInformationW (Unicode) and GetUserObjectInformationA (ANSI) |


我们要找的就是Library：User32.lib，如果想以静态引用的方式使用GetUserObjectInformation API，就需要将 User32.lib 加入链接器输入的附加依赖项，操作方法如下图：
在解决方案资源管理器中选中对应的项目

<center>

![mark](http://images.iterate.site/blog/image/20191019/aGkyd47Qxe26.png?imageslim)

</center>


点击项目菜单》属性，或者是直接在项目上单击鼠标右键》属性

<center>

![mark](http://images.iterate.site/blog/image/20191019/hXhWyQL38Y1i.png?imageslim)

</center>

展开配置属性》链接器，选中输入，点击附加依赖项右侧的下拉框，在弹出的菜单中点击编辑

<center>

![mark](http://images.iterate.site/blog/image/20191019/48hlfvFuEpVu.png?imageslim)

</center>

输入附加依赖项，点击确定。


<center>

![mark](http://images.iterate.site/blog/image/20191019/B8OjKF6WlSCk.png?imageslim)

</center>


# 相关

- [error LNK2019: 无法解析的外部符号 __imp_GetUserObjectInformationW，该符号在函数 OPENSSL_isservice 中被引用](https://blog.csdn.net/testcs_dn/article/details/46276865)
