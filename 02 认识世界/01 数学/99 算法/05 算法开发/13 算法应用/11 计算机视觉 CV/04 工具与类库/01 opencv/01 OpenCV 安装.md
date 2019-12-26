---
title: 01 OpenCV 安装
toc: true
date: 2018-08-12 17:06:45
---
# OpenCV 安装

## 缘由：

机器学习中对于图像的处理经常使用 opencv，而 opencv 的安装稍微有些麻烦，因此总结下。


## 要点：

### 1.如果只需要在 python 情况下使用 opencv


在 anaconda3 和 pycharm 都已经安装好之后，开始安装 opencv

下载地址是：[https://www.lfd.uci.edu/~gohlke/pythonlibs/](https://www.lfd.uci.edu/~gohlke/pythonlibs/)

地址里面是 python 的很多 unofficial 库，因此加载时间比较长，要等一会。


![](http://images.iterate.site/blog/image/180728/lkGF7d5C9I.png?imageslim){ width=55% }

然后在 pycharm 中查看自己的 python 版本和对应的 win32 还是 AMD64，我的是 AMD64，python 3.6.0   ，  因此选择下载   opencv_python-3.4.1-cp36-cp36m-win_amd64.whl


![](http://images.iterate.site/blog/image/180728/93AilD7B7E.png?imageslim){ width=55% }

下载之后把文件复制到 Anaconda3\Lib\site-packages 文件夹里面。
按 Win+R 输入 cmd 打开命令提示符窗口，进入到 Anaconda3\Lib\site-packages文件夹下
执行命令

pip install opencv_python-3.4.1-cp36-cp36m-win_amd64.whl


![](http://images.iterate.site/blog/image/180728/ejkI14kBEl.png?imageslim){ width=55% }

测试一下，输 python 进入 python，输入 import cv2回车，不报错就说明安装配置成功了

![](http://images.iterate.site/blog/image/180728/jbIdCGaH7H.png?imageslim){ width=55% }



### 2.要在 c++情况下使用 opencv （windows情况下）




#### 下载 opencv：


[下载地址](https://opencv.org/releases.html)

下载时候的注意：

1. 选择 3.xx版本的，但是选择的时候需要注意：3.10版本以上的 opencv 需要对应不同的 vc 版本，比如 3.4版本的 opencv 是对应 vc14 和 vc15 的 ，但是不对应的话会有什么问题不是很清楚。

2. 可以在点击 win pack 进行下载时弹出的页面上看到所下的 exe 的名字，上面有写对应的时多少的 vc 版本，（如：opencv-3.4.0-vc14_vc15.exe）

3. VS2013 对应的 vc 版本时 vc12 ，因此找了下好像之后 opencv3.10是没写多少 vc 版本，因此下的 opencv3.10。（[VS与 vc 版本的对应关系](http://blog.csdn.net/hellokandy/article/details/53379724)）。


下载好之后，是一个自解压的包，可以自由选择解压的地址。我选择： E:\14.Develop\OpenCV


#### 解压完之后就开始环境变量的配置：


环境变量的配置时的注意：

为了方便 github 上协作，因此 VS 里面的配置地址肯定不能写死。因此在环境变量中设定地址，然后在 VS 的配置中引用这个地址即可：

我在环境变量中新建了一个
变量名：opencvBuild
变量值：E:\14.Develop\OpenCV\opencv\build


#### 然后就是 VS 的配置：


新建一个 VS 控制台项目，
先打开配置管理器，创建 x64 的平台，因为 opencv3 以上只有 x64 的 build 了
然后在 试图-属性管理器中 双击出现的 Debug | x64 和 Release |64 在里面进行配置
如果你经常使用的话，可以在 Debug |64 打开后双击 Microsoft.Cpp.x64 进行设定，在这里设定的话，你新建一个新的工程的话包含目录和库目录 的地址也还是在的，但是这里要注意连接器的 lib 不要在这里写，因为 Microsoft.Cpp.x64 是不区分 debug 和 release 的，可以在 Debug | x64 和 Release |64里面写。

Vc++目录-包含目录中填写：

```
$(opencvBuild)\include
$(opencvBuild)\include\opencv
$(opencvBuild)\include\opencv2
```

库目录中填写：

```
$(opencvBuild)\x64\vc12\lib
```

连接器-输入-附加依赖项 中填写：(这个在每次新建项目的时候进行配置 ，不要追求一劳永逸的写在 Microsoft.Cpp.x64里面，因为带 d 的和不带 d 的实际上是不同的)
opencv_world310d.dll 或者 opencv_world310.dll


#### 然后检验：


```cpp
#include <cv.h>
#include <highgui.h>
using namespace std;
int main()
{
    IplImage * test;
    test = cvLoadImage("D:\\Sample_8.bmp");//图片路径
    cvNamedWindow("test_demo", 1);
    cvShowImage("test_demo", test);
    cvWaitKey(0);
    cvDestroyWindow("test_demo");
    cvReleaseImage(&test);
    return 0;
}
```




### 3.如果是在 linux 情况下使用 opencv







# 相关
