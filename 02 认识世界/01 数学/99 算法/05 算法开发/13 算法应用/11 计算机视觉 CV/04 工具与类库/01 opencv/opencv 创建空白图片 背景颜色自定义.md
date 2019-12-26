---
title: opencv 创建空白图片 背景颜色自定义
toc: true
date: 2019-06-07
---
# 可以补充进来的



# python-opencv创建空白图片(背景颜色自定义)


在 python 使用 opencv 的过程中常常需要新建空白图片，官方没有直接的解决方案，使用如下方法即可轻松创建空白图片，背景颜色自定义。

```py
import numpy as np
import cv2

# 使用 Numpy 创建一张 A4(2105×1487) 纸
img = np.zeros((2105,1487,3), np.uint8)

# 使用白色填充图片区域，默认为黑色
img.fill(255)

cv2.imshow('image', img)
cv2.waitKey(0)
cv2.destroyAllWindows()
```





# 相关

- [python-opencv创建空白图片(背景颜色自定义)](https://wsonh.com/article/116.html)
