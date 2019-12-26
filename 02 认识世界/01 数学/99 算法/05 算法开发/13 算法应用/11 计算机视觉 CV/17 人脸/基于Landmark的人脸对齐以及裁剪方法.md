---
title: 基于Landmark的人脸对齐以及裁剪方法
toc: true
date: 2018-07-30
---
# 基于 Landmark 的人脸对齐以及裁剪方法

利用 Landmarks 进行人脸对齐裁剪是人脸检测中重要的一个步骤。效果如下图所示：

<center>

![mark](http://images.iterate.site/blog/image/20190808/zqRhXSeBzC0l.png?imageslim)

</center>


基本思路为：

1. 人脸检测
    - 人脸的检测不必多说了，基本 Cascade 的方式已经很不错了，或者用基于 HOG/FHOG的 SVM/DPM等。这些在[OpenCV](http://www.opencv.org/)，[DLIB](http://dlib.net/)都有。
2. 在检测到的人脸上进行 Landmarks 检测，获得一系列的 Landmark 点
    - 对齐算法很多，特别是前几年人脸对齐获得了巨大的成功。
    - [One Millisecond Face Alignment with an Ensemble of Regression Trees by Vahid Kazemi and Josephine Sullivan, CVPR 2014](http://www.cv-foundation.org/openaccess/content_cvpr_2014/html/Kazemi_One_Millisecond_Face_2014_CVPR_paper.html)
    - [人脸对齐 SDM 原理----Supervised Descent Method and its Applications to Face Alignment](http://www.cnblogs.com/cv-pr/p/4797823.html)
3. 利用检测到的 Landmarks 和模板的 Landmarks，计算仿射矩阵 H；然后利用 H，直接计算得到对齐后的图像。

直接上代码：

```cpp
#include <opencv2/opencv.hpp>
using namespace cv;
//原始图像大小
#define IMAGE_WIDTH 640
#define IMAGE_HEIGHT 480
//输出的图像大小（裁剪后的）
#define IMAGE_WIDTH_STD 90
#define IMAGE_HEIGHT_STD 90

#define LANDMARK_SIZE 8//对齐点的个数
#define LANDMARK_SIZE_DOUBLE 16//对齐点个数的两倍

Point2f srcTri[LANDMARK_SIZE];//对齐点的 Point2f 数组，检测到的人脸对齐点，注意这里是基于原始图像的坐标点
Point2f destTri[LANDMARK_SIZE];//对齐点的 Point2f 数组，模板的 Landmarks，注意这是一个基于输出图像大小尺寸的坐标点
                               //对齐点的 double 数组
double template_landmark[LANDMARK_SIZE_DOUBLE] = {
    0.0792396913815, 0.339223741112, 0.0829219487236, 0.456955367943,
    0.0967927109165, 0.575648016728, 0.122141515615, 0.691921601066,
    0.168687863544, 0.800341263616, 0.239789390707, 0.895732504778,
    0.325662452515, 0.977068762493, 0.422318282013, 1.04329000149,
    0.531777802068, 1.06080371126, 0.641296298053, 1.03981924107,
    0.738105872266, 0.972268833998, 0.824444363295, 0.889624082279,
    0.894792677532, 0.792494155836, 0.939395486253, 0.681546643421,
    0.96111933829, 0.562238253072, 0.970579841181, 0.441758925744
};


int main()
{
    VideoCapture vcap;
    if (!vcap.open(0))
    {
        return 0;
    }

    for (int i = 0; i < LANDMARK_SIZE; i++)
    {
        srcTri[i] = Point2f(template_landmark[i * 2] * 90 + IMAGE_HEIGHT / 2, template_landmark[i * 2 + 1] * 90 + IMAGE_WIDTH / 2);
        destTri[i] = Point2f(template_landmark[i * 2] * IMAGE_HEIGHT_STD, template_landmark[i * 2 + 1] * IMAGE_WIDTH_STD);
    }
    //Mat warp_mat = getAffineTransform( srcTri, destTri );//使用仿射变换，计算 H 矩阵
    Mat warp_mat = cv::estimateRigidTransform(srcTri, destTri, false);//使用相似变换，不适合使用仿射变换，会导致图像变形
    Mat frame;
    Mat warp_frame(200, 200, CV_8UC3);
    while (1)
    {
        vcap >> frame;
        warpAffine(frame, warp_frame, warp_mat, warp_frame.size());//裁剪图像

        imshow("frame", frame);//显示原图像
        imshow("warp_frame", warp_frame);//显示裁剪后得到的图像

        waitKey(10);
    }
    return 0;
}
```


效果图：

<center>

![mark](http://images.iterate.site/blog/image/20190808/VxH84cb3gfj3.png?imageslim)

</center>

注意以上效果非真实的对齐裁剪的效果。实际的对齐裁剪可以做的很好。



# 相关

- [基于 Landmark 的人脸对齐以及裁剪方法](https://blog.csdn.net/chundian/article/details/60143600)


