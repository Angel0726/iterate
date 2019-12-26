---
title: import cv2 出现 DLL load failed 找不到指定的模块
toc: true
date: 2019-04-22
---


# import cv2 出现 DLL load failed 找不到指定的模块

当

```
import cv2
```

的时候，提示：


```
DLL load failed: 找不到指定的模块。
```

真正的方法：

```
activate tensorflow
pip install opencv_python
```


这样就可以了。其他的什么安装 c++ 运行环境都是不行的，解决不了这个问题。




# 相关
