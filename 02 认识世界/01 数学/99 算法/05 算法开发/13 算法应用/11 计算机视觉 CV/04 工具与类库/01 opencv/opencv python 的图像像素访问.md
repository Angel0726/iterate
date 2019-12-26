---
title: opencv python 的图像像素访问
toc: true
date: 2019-02-18
---
# opencv python的图像像素访问



一、按照多维数组访问

同 python 中 numpy 的 ndarray 访问元素一样：img[a,b,c]

1.灰度图(单通道)

```py
img[i,j]  ###i = 行， j = 列
```

2.彩色图像：Opencv下为 BGR，0,1,2表示通道数

```py
img[j,i,0]= 255
img[j,i,1]= 255
img[j,i,2]= 255
```

3.numpy中的矩阵访问方法（建议使用）

array.item()和 array.itemset函数：

```py
import cv2
import numpy as np
img=cv2.imread('/home/duan/workspace/opencv/images/roi.jpg')
print img.item(10,10,2)
img.itemset((10,10,2),100)
print img.item(10,10,2)
## 50
## 100
```


二、使用 Opencv 自带的函数

```py
cv2.GetCol(img, 0): 返回第一列的像素
cv2.GetCols(img, 0, 10): 返回前 10 列
cv2.GetRow(img, 0): 返回第一行
cv2.GetRows(img, 0, 10): 返回前 10 行
```

建议使用第一种方法，用的多

三、代码示例

常见的椒盐现象：(还存在 BGR 与 RGB 的问题)

```py
import cv2
import numpy as np
import matplotlib.pyplot as plt
def salt(img, n):
​    for k in range(n):
​        i = int(np.random.random() * img.shape[0]);
​        j = int(np.random.random() * img.shape[1]);
​        if img.ndim == 2:
​            img[i,j] = 255
​        elif img.ndim == 3:
​            img[i,j,0]= 255
​            img[i,j,1]= 255
​            img[i,j,2]= 255
​    return img
if __name__ == '__main__':
   img = cv2.imread("scene.jpg")
   saltImage = salt(img, 500)
   plt.imshow(saltImage)
   plt.show()
   #cv2.imshow("Salt", saltImage)
   #cv2.waitKey(0)
   #cv2.destroyAllWindows()
```


图 1 椒盐现象




# 原文及引用

- [Opencv-python的图像像素访问](https://blog.csdn.net/lsforever/article/details/82851093)
