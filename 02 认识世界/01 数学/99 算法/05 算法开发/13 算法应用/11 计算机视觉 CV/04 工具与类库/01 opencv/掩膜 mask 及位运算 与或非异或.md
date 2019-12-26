---
title: 掩膜 mask 及位运算 与或非异或
toc: true
date: 2019-02-04
---
# 可以补充进来的

- 要进行重新整理，感觉这个地方还有点不是很清楚。



# 掩膜 mask 及位运算 与或非异或

## 问题引入

在[1.4.3裁剪](https://www.jianshu.com/p/0633ead5613c)一节，我们使用的是 numpy 数组切片功能实现图片区域的裁剪。
 那么，如果我们想要裁剪图像中任意形状的区域时，应该怎么办呢？
 答案是，使用掩膜(masking)。
 但是这一节我们先看一下掩膜的基础。图像的位运算。



![](http://images.iterate.site/blog/image/20190204/J6j1skzF9QVR.png?imageslim){ width=55% }


## 代码

```py
# 导入库
import numpy as np
import argparse
import cv2

# 构建参数解析器
ap = argparse.ArgumentParser()
ap.add_argument("-i", "--image", required=True, help="Path to the image")
args = vars(ap.parse_args())

# 加载猫的图像
image = cv2.imread(args["image"])
cv2.imshow("Cat", image)

# 创建矩形区域，填充白色 255
rectangle = np.zeros(image.shape[0:2], dtype = "uint8")
cv2.rectangle(rectangle, (25, 25), (275, 275), 255, -1)
cv2.imshow("Rectangle", rectangle)

# 创建圆形区域，填充白色 255
circle = np.zeros(image.shape[0:2], dtype = "uint8")
cv2.circle(circle, (150, 150), 150, 255, -1)
cv2.imshow("Circle", circle)

# 在此例（二值图像）中，以下的 0 表示黑色像素值 0, 1表示白色像素值 255
# 位与运算，与常识相同，有 0 则为 0, 均无 0 则为 1
bitwiseAnd = cv2.bitwise_and(rectangle, circle)
cv2.imshow("AND", bitwiseAnd)
cv2.waitKey(0)

# 或运算，有 1 则为 1, 全为 0 则为 0
bitwiseOr = cv2.bitwise_or(rectangle, circle)
cv2.imshow("OR", bitwiseOr)
cv2.waitKey(0)

# 非运算，非 0 为 1, 非 1 为 0
bitwiseNot = cv2.bitwise_not(circle)
cv2.imshow("NOT", bitwiseNot)
cv2.waitKey(0)

# 异或运算，不同为 1, 相同为 0
bitwiseXor = cv2.bitwise_xor(rectangle, circle)
cv2.imshow("XOR", bitwiseXor)
cv2.waitKey(0)
```

## 效果

运行脚本:


![](http://images.iterate.site/blog/image/20190204/nf6hssLjYi66.png?imageslim){ width=55% }


相信大家看到效果，再结合代码可以很容易理解。

## 裁剪

下面，我们利用 OR 结果（有点像猫的头像轮廓）把本课的主题图片中的猫的头像剪切出来。
 我们需要修改一下，矩形区域的大小，去掉下边的两个角。

```py
cv2.rectangle(rectangle, (25, 25), (275, 220), 255, -1)
```

最终调整后的代码如下：

```py
# 导入库
import numpy as np
import argparse
import cv2

# 构建参数解析器
ap = argparse.ArgumentParser()
ap.add_argument("-i", "--image", required=True, help="Path to the image")
args = vars(ap.parse_args())

# 加载猫的图像
image = cv2.imread(args["image"])
cv2.imshow("Cat", image)

# 创建矩形区域，填充白色 255
rectangle = np.zeros(image.shape[:2], dtype = "uint8")
cv2.rectangle(rectangle, (380, 100), (575, 200), 255, -1)
cv2.imshow("Rectangle", rectangle)

# 创建圆形区域，填充白色 255
circle = np.zeros(image.shape[:2], dtype = "uint8")
cv2.circle(circle, (475, 180), 105, 255, -1)
cv2.imshow("Circle", circle)

# 或运算
bitwiseOr = cv2.bitwise_or(rectangle, circle)
cv2.imshow("OR", bitwiseOr)
cv2.waitKey(0)

mask = bitwiseOr
cv2.imshow("Mask", mask)

# Apply out mask -- notice how only the person in the image is cropped out
masked = cv2.bitwise_and(image, image, mask=mask)
cv2.imshow("Mask Applied to Image", masked)
cv2.waitKey(0)
```

运行得到
 得到：

![](http://images.iterate.site/blog/image/20190204/pit7Ki5IiOO0.png?imageslim){ width=55% }


![](http://images.iterate.site/blog/image/20190204/SdIocl3PcPVs.png?imageslim){ width=55% }


我们“近似”得到了猫的头像。

## 总结

1. 与或非异或运算与我们的常识类似。
2. 掩膜操作就是两幅图像(numpy数组)的位运算操作。



# 原文及引用

- [小强学 python+OpenCV之－1.4.4掩膜 mask 及位运算（与、或、非、异或）](https://www.jianshu.com/p/53353300a9e4)
