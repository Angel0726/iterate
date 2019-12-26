---
title: Tensorflow与 CUDA 和 cuDNN 之间的关系
toc: true
date: 2019-11-17
---
# Tensorflow 与 CUDA 和 cuDNN 之间的关系

## 首先，明确几个概念


### NVIDIA的显卡驱动程序和CUDA

NVIDIA的显卡驱动程序和CUDA完全是两个不同的概念。

- CUDA是NVIDIA推出的用于自家GPU的并行计算框架，也就是说CUDA只能在NVIDIA的GPU上运行，而且只有当要解决的计算问题是可以大量并行计算的时候才能发挥CUDA的作用。CUDA的本质是一个工具包（ToolKit）。


显卡驱动的安装：

当我们使用一台电脑的时候默认的已经安装了NVIDIA的显卡驱动，因为没有显卡驱动根本用不了显卡嘛，但是这个时候我们是没有CUDA可以用的，我们可以更新我们的驱动，更新链接为：

https://www.nvidia.com/Download/index.aspx?lang=en-us

在这个里面可以根据自己的显卡类型选择最新的驱动程序。显卡驱动程序当前大小大概500多M。


CUDA ToolKit的安装：

CUDA的下载地址为：https://developer.nvidia.com/cuda-downloads

当我们选择离线安装，当我们选定相对应的版本之后，下载的时候发现这个地方的文件大小大概在2G左右，Linux系统下面我们选择runfile(local) 完整安装包从本地安装，或者是选择windows的本地安装。**CUDA Toolkit本地安装包时内含特定版本Nvidia显卡驱动的，所以只选择下载CUDA Toolkit就足够了，如果想安装其他版本的显卡驱动就下载相应版本即可。**

所以，NVIDIA显卡驱动和CUDA工具包本身是不具有捆绑关系的，也不是一一对应的关系，只不过是离线安装的CUDA工具包会默认携带与之匹配的最新的驱动程序。

注意事项：NVIDIA的显卡驱动器与CUDA并不是一一对应的哦，CUDA本质上只是一个工具包而已，所以我可以在同一个设备上安装很多个不同版本的CUDA工具包，比如我的电脑上同事安装了 CUDA 9.0、CUDA 9.2、CUDA 10.0三个版本。一般情况下，我只需要安装最新版本的显卡驱动，然后根据自己的选择选择不同CUDA工具包就可以了，但是由于使用离线的CUDA总是会捆绑CUDA和驱动程序，**所以在使用多个CUDA的时候就不要选择离线安装的CUDA了**，否则每次都会安装不同的显卡驱动，这不太好，我们直接安装一个最新版的显卡驱动，然后在线安装不同版本的CUDA即可。

总结：CUDA和显卡驱动是没有一一对应的。


### cuDNN

cuDNN是一个SDK，是一个专门用于神经网络的加速包，注意，它跟我们的CUDA没有一一对应的关系，即每一个版本的CUDA可能有好几个版本的cuDNN与之对应，但一般有一个最新版本的cuDNN版本与CUDA对应更好。

总结：cuDNN与CUDA没有一一对应的关系


### CUDA 工具包附带的 CUPTI

CUPTI，即CUDA Profiling Tools Interface (CUPTI)。在CUDA分析工具接口（CUPTI）能够分析和跟踪靶向CUDA应用程序的工具的创建。CUPTI提供以下API：

- Activity API，
- Callback API，
- 事件API，
- Metric API
- Profiler API


使用这些API，您可以开发分析工具，深入了解CUDA应用程序的CPU和GPU行为。CUPTI作为CUDA支持的所有平台上的动态库提供。请参阅CUPTI文档。


## 然后我们看看 Tensorflow 与 CUDA 和 cuDNN 的对应关系

### 对应表格

相应的网址为：

https://www.tensorflow.org/install/source#common_installation_problems

https://www.tensorflow.org/install/source_windows

版本	Python 版本	编译器	编译工具	cuDNN	CUDA
tensorflow_gpu-2.0.0-alpha0	2.7、3.3-3.6	GCC 4.8	Bazel 0.19.2	7.4.1以及更高版本	CUDA 10.0 (需要 410.x 或更高版本)
tensorflow_gpu-1.13.0	2.7、3.3-3.6	GCC 4.8	Bazel 0.19.2	7.4	10.0
tensorflow_gpu-1.12.0	2.7、3.3-3.6	GCC 4.8	Bazel 0.15.0	7	9
tensorflow_gpu-1.11.0	2.7、3.3-3.6	GCC 4.8	Bazel 0.15.0	7	9
tensorflow_gpu-1.10.0	2.7、3.3-3.6	GCC 4.8	Bazel 0.15.0	7	9
tensorflow_gpu-1.9.0	2.7、3.3-3.6	GCC 4.8	Bazel 0.11.0	7	9
tensorflow_gpu-1.8.0	2.7、3.3-3.6	GCC 4.8	Bazel 0.10.0	7	9
tensorflow_gpu-1.7.0	2.7、3.3-3.6	GCC 4.8	Bazel 0.9.0	7	9
tensorflow_gpu-1.6.0	2.7、3.3-3.6	GCC 4.8	Bazel 0.9.0	7	9
tensorflow_gpu-1.5.0	2.7、3.3-3.6	GCC 4.8	Bazel 0.8.0	7	9
tensorflow_gpu-1.4.0	2.7、3.3-3.6	GCC 4.8	Bazel 0.5.4	6	8
tensorflow_gpu-1.3.0	2.7、3.3-3.6	GCC 4.8	Bazel 0.4.5	6	8
tensorflow_gpu-1.2.0	2.7、3.3-3.6	GCC 4.8	Bazel 0.4.5	5.1	8
tensorflow_gpu-1.1.0	2.7、3.3-3.6	GCC 4.8	Bazel 0.4.2	5.1	8
tensorflow_gpu-1.0.0	2.7、3.3-3.6	GCC 4.8	Bazel 0.4.2	5.1	8


现在NVIDIA的显卡驱动程序已经更新到 10.1版本，最新的支持CUDA 10.1版本的cuDNN为7.5.0

### CUDA的命名规则

下面以几个例子来说

（1）CUDA 9.2

CUDA  9.2.148

（2）CUDA 10.0

CUDA 10.0.130.411.31（后面的411.31对应更具体的版本号）

（3）CUDA 10.1

CUDA 10.1.105.418.96（后面的418.96对应更具体的版本号）

更多详细的请参考如下官网：

https://developer.nvidia.com/cuda-toolkit-archive

### 如何查看自己所安装的CUDA的版本：

1. 直接在NVIDIA的控制面板里面查看NVCUDA.DLL的版本。

注意：这里网上有很多说法是错误的，这个版本并不能绝对说明自己所安装的CUDA工具包一定这个版本

2. 通过命令查看：nvcc -V 或者是nvcc --version都可以，但前提是添加了环境变量

3. 直接通过文件查看，这里分为Linux和windows两种情况

在windows平台下，可以直接进入CUDA的安装目录，比如我的是：

`C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v9.2`   里面有一个version.txt的文本文件，直接打开即可，也可以使用命令，即

首先进入到安装目录，然后执行：`type version.txt` 即可查看

在Linux平台下：

同windows类似，进入到安装目录，然后执行  `cat version.txt` 命令

### 如何查看自己的cuDNN的版本

因为cuDNN本质上就是一个C语言的H头文件，

（1）在windows平台下：

直接进入安装目录：`C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v9.2\include`  之下，然后找到

`cudnn.h` 的头文件，直接到开查看，在最开始的部分会有如下定义：

```
#define CUDNN_MAJOR 7
#define CUDNN_MINOR 5
#define CUDNN_PATCHLEVEL 0

#define CUDNN_VERSION (CUDNN_MAJOR * 1000 + CUDNN_MINOR * 100 + CUDNN_PATCHLEVEL)
```

即7500，也就是cudnn的版本为7.5.0版本；

（2）在Linux下当然也可以直接查看，但是通过命令更简单，进入到安装目录，执行如下命令：

`cat /usr/local/cuda/include/cudnn.h | grep CUDNN_MAJOR -A 2`  即可查询

即5005，即5.0.5版本的cudnn。



## CUDA与相对应的Cudnn对应关系

Download cuDNN v7.4.2 (Dec 14, 2018), for CUDA 10.0

Download cuDNN v7.4.2 (Dec 14, 2018), for CUDA 9.2

Download cuDNN v7.4.2 (Dec 14, 2018), for CUDA 9.0

Download cuDNN v7.4.1 (Nov 8, 2018), for CUDA 10.0

Download cuDNN v7.4.1 (Nov 8, 2018), for CUDA 9.2

Download cuDNN v7.4.1 (Nov 8, 2018), for CUDA 9.0

Download cuDNN v7.3.1 (Sept 28, 2018), for CUDA 10.0

Download cuDNN v7.3.1 (Sept 28, 2018), for CUDA 9.2

Download cuDNN v7.3.1 (Sept 28, 2018), for CUDA 9.0

Download cuDNN v7.3.0 (Sept 19, 2018), for CUDA 10.0

Download cuDNN v7.3.0 (Sept 19, 2018), for CUDA 9.0

Download cuDNN v7.2.1 (August 7, 2018), for CUDA 9.2

Download cuDNN v7.1.4 (May 16, 2018), for CUDA 9.2

Download cuDNN v7.1.4 (May 16, 2018), for CUDA 9.0

Download cuDNN v7.1.4 (May 16, 2018), for CUDA 8.0

Download cuDNN v7.1.3 (April 17, 2018), for CUDA 9.1

Download cuDNN v7.1.3 (April 17, 2018), for CUDA 9.0

Download cuDNN v7.1.3 (April 17, 2018), for CUDA 8.0

Download cuDNN v7.1.2 (Mar 21, 2018), for CUDA 9.1 & 9.2

Download cuDNN v7.1.2 (Mar 21, 2018), for CUDA 9.0

Download cuDNN v7.0.5 (Dec 11, 2017), for CUDA 9.1

Download cuDNN v7.0.5 (Dec 5, 2017), for CUDA 9.0

Download cuDNN v7.0.5 (Dec 5, 2017), for CUDA 8.0

Download cuDNN v7.0.4 (Nov 13, 2017), for CUDA 9.0

Download cuDNN v6.0 (April 27, 2017), for CUDA 8.0

Download cuDNN v6.0 (April 27, 2017), for CUDA 7.5

Download cuDNN v5.1 (Jan 20, 2017), for CUDA 8.0

Download cuDNN v5.1 (Jan 20, 2017), for CUDA 7.5

Download cuDNN v5 (May 27, 2016), for CUDA 8.0

Download cuDNN v5 (May 12, 2016), for CUDA 7.5

Download cuDNN v4 (Feb 10, 2016), for CUDA 7.0 and later.

Download cuDNN v3 (September 8, 2015), for CUDA 7.0 and later.

Download cuDNN v2 (March 17,2015), for CUDA 6.5 and later.

Download cuDNN v1 (cuDNN 6.5 R1)

## NVIDIA显卡以及对应的显卡驱动的对应关系

由于NVIDIA存在多个系列的显卡类型，把这里仅仅显示出GeForce系列的显卡以及各个显卡的计算能力（compute capability），详情可以参考官网链接：

https://developer.nvidia.com/cuda-gpus

（1）GeForce Desktop Products

GPU	Compute Capability
NVIDIA TITAN RTX	7.5
Geforce RTX 2080 Ti	7.5
Geforce RTX 2080	7.5
Geforce RTX 2070	7.5
Geforce RTX 2060	7.5
NVIDIA TITAN V	7.0
NVIDIA TITAN Xp	6.1
NVIDIA TITAN X	6.1
GeForce GTX 1080 Ti	6.1
GeForce GTX 1080	6.1
GeForce GTX 1070	6.1
GeForce GTX 1060	6.1
GeForce GTX 1050	6.1
GeForce GTX TITAN X	5.2
GeForce GTX TITAN Z	3.5
GeForce GTX TITAN Black	3.5
GeForce GTX TITAN	3.5
GeForce GTX 980 Ti	5.2
GeForce GTX 980	5.2
GeForce GTX 970	5.2
GeForce GTX 960	5.2
GeForce GTX 950	5.2
GeForce GTX 780 Ti	3.5
GeForce GTX 780	3.5
GeForce GTX 770	3.0
GeForce GTX 760	3.0
GeForce GTX 750 Ti	5.0
GeForce GTX 750	5.0
GeForce GTX 690	3.0
GeForce GTX 680	3.0
GeForce GTX 670	3.0
GeForce GTX 660 Ti	3.0
GeForce GTX 660	3.0
GeForce GTX 650 Ti BOOST	3.0
GeForce GTX 650 Ti	3.0
GeForce GTX 650	3.0
GeForce GTX 560 Ti	2.1
GeForce GTX 550 Ti	2.1
GeForce GTX 460	2.1
GeForce GTS 450	2.1
GeForce GTS 450*	2.1
GeForce GTX 590	2.0
GeForce GTX 580	2.0
GeForce GTX 570	2.0
GeForce GTX 480	2.0
GeForce GTX 470	2.0
GeForce GTX 465	2.0
GeForce GT 740	3.0
GeForce GT 730	3.5
GeForce GT 730 DDR3,128bit	2.1
GeForce GT 720	3.5
GeForce GT 705*	3.5
GeForce GT 640 (GDDR5)	3.5
GeForce GT 640 (GDDR3)	2.1
GeForce GT 630	2.1
GeForce GT 620	2.1
GeForce GT 610	2.1
GeForce GT 520	2.1
GeForce GT 440	2.1
GeForce GT 440*	2.1
GeForce GT 430	2.1
GeForce GT 430*	2.1
（2）GeForce Notebook Products（笔记本电脑）

GPU	Compute Capability
Geforce RTX 2080	7.5
Geforce RTX 2070	7.5
Geforce RTX 2060	7.5
GeForce GTX 1080	6.1
GeForce GTX 1070	6.1
GeForce GTX 1060	6.1
GeForce GTX 980	5.2
GeForce GTX 980M	5.2
GeForce GTX 970M	5.2
GeForce GTX 965M	5.2
GeForce GTX 960M	5.0
GeForce GTX 950M	5.0
GeForce 940M	5.0
GeForce 930M	5.0
GeForce 920M	3.5
GeForce 910M	5.2
GeForce GTX 880M	3.0
GeForce GTX 870M	3.0
GeForce GTX 860M	3.0/5.0(**)
GeForce GTX 850M	5.0
GeForce 840M	5.0
GeForce 830M	5.0
GeForce 820M	2.1
GeForce 800M	2.1
GeForce GTX 780M	3.0
GeForce GTX 770M	3.0
GeForce GTX 765M	3.0
GeForce GTX 760M	3.0
GeForce GTX 680MX	3.0
GeForce GTX 680M	3.0
GeForce GTX 675MX	3.0
GeForce GTX 675M	2.1
GeForce GTX 670MX	3.0
GeForce GTX 670M	2.1
GeForce GTX 660M	3.0
GeForce GT 755M	3.0
GeForce GT 750M	3.0
GeForce GT 650M	3.0
GeForce GT 745M	3.0
GeForce GT 645M	3.0
GeForce GT 740M	3.0
GeForce GT 730M	3.0
GeForce GT 640M	3.0
GeForce GT 640M LE	3.0
GeForce GT 735M	3.0
GeForce GT 635M	2.1
GeForce GT 730M	3.0
GeForce GT 630M	2.1
GeForce GT 625M	2.1
GeForce GT 720M	2.1
GeForce GT 620M	2.1
GeForce 710M	2.1
GeForce 705M	2.1
GeForce 610M	2.1
GeForce GTX 580M	2.1
GeForce GTX 570M	2.1
GeForce GTX 560M	2.1
GeForce GT 555M	2.1
GeForce GT 550M	2.1
GeForce GT 540M	2.1
GeForce GT 525M	2.1
GeForce GT 520MX	2.1
GeForce GT 520M	2.1
GeForce GTX 485M	2.1
GeForce GTX 470M	2.1
GeForce GTX 460M	2.1
GeForce GT 445M	2.1
GeForce GT 435M	2.1
GeForce GT 420M	2.1
GeForce GT 415M	2.1
GeForce GTX 480M	2.0
GeForce 710M	2.1
GeForce 410M	2.1



# 相关

- [tensorflow各个版本的CUDA以及Cudnn版本对应关系](https://blog.csdn.net/qq_27825451/article/details/89082978)
