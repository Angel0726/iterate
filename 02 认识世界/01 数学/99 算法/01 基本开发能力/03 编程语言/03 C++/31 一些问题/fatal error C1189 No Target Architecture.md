---
title: fatal error C1189 No Target Architecture
toc: true
date: 2019-10-19
---
# fatal error C1189: #error : “No Target Architecture”

在编译程序的时候发现报这个错误，在网上看到很多文章，说设置include路径，lib目录等等，都没有解决。最后调整了以下include文件的顺序，问题解决了。例如

从头文件a.h中截取的一段


```
typedef struct
{
   DWORD Data1;
   WORD Data2;
   WORD Data3;
   BYTE Data4[ 8 ];
} GUID;
```

然后在b.cpp文件里面引用

```
#include <a.h>
#include <Windows.h>
```

这样编译会报错

程序报错：error C2146: 语法错误 : 缺少“;”

其原因是在a.h文件中 DWORD未定义，在a.h文件中引用minwindef.h再编译就会报错 fatal error C1189: #error : “No Target Architecture”


```
#include <minwindef.h>
typedef struct
{
  DWORD Data1;
  WORD Data2;
  WORD Data3;
  BYTE Data4[ 8 ];
} GUID;
```

再引用windows.h

```
#include <windows.h>
#include <minwindef.h>
typedef struct
{
  DWORD Data1;
  WORD Data2;
  WORD Data3;
  BYTE Data4[ 8 ];
} GUID;
```

然后就可以正常编译了，其实不用这么复杂，直接在b.cpp文件中调整下引用文件的顺序就可以了，如下

```
#include <Windows.h>



#include <a.h>
```

# 相关
