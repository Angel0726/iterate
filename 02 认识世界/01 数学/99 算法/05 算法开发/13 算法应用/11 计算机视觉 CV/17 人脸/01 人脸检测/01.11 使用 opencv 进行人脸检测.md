---
title: 01.11 使用 opencv 进行人脸检测
toc: true
date: 2019-10-13
---
# 使用 opencv 进行人脸检测


```py
import cv2.cv as cv

img = cv.LoadImage("friend1.jpg");

image_size = cv.GetSize(img)#获取图片的大小
greyscale = cv.CreateImage(image_size, 8, 1)#建立一个相同大小的灰度图像
cv.CvtColor(img, greyscale, cv.CV_BGR2GRAY)#将获取的彩色图像，转换成灰度图像
storage = cv.CreateMemStorage(0)#创建一个内存空间，人脸检测是要利用，具体作用不清楚

cv.EqualizeHist(greyscale, greyscale)#将灰度图像直方图均衡化，貌似可以使灰度图像信息量减少，加快检测速度
# detect objects
cascade = cv.Load('haarcascade_frontalface_alt2.xml')#加载Intel公司的训练库

#检测图片中的人脸，并返回一个包含了人脸信息的对象faces
faces = cv.HaarDetectObjects(greyscale, cascade, storage, 1.2, 2,
                                     cv.CV_HAAR_DO_CANNY_PRUNING,
                                     (50, 50))

#获得人脸所在位置的数据
j=0 #记录个数
for (x,y,w,h),n in faces:
    j+=1
    cv.SetImageROI(img,(x,y,w,h))#获取头像的区域
    cv.SaveImage("face"+str(j)+".jpg",img);#保存下来
```


效果如下。


![mark](http://images.iterate.site/blog/image/20191011/FACpKiHsGFPL.png?imageslim)


![mark](http://images.iterate.site/blog/image/20191011/pMWyRIuTOV5J.png?imageslim)




# 相关

- [从图像中检测人脸，并将人脸提取出来](https://blog.csdn.net/jkhere/article/details/8629627)
