---
title: OpenCV实战 八种目标跟踪算法
toc: true
date: 2019-11-17
---
# 目标跟踪

object tracking

讨论OpenCV上八种不同的目标追踪算法。

虽然我们熟知的的质心追踪器表现得很好，但它需要我们在输入的视频上的每一帧运行一个目标探测器。对大多数环境来说，在每帧上进行检测非常耗费计算力。

所以，我们想应用一种一次性的目标检测方法，然后在之后的帧上都能进行目标追踪，使这一任务更加快速、更高效。

这里的问题是：**OpenCV能帮我们达到这种目标追踪的目的吗？**

答案是肯定的。

## OpenCV目标追踪

首先，我们会大致介绍八种建立在OpenCV上的目标跟踪算法。之后我会讲解如何利用这些算法进行实时目标追踪。最后，我们会比较各个OpenCV目标追踪的效果，总结各种方法能够适应的环境。

## 八种OpenCV目标追踪安装

*![img](https://mmbiz.qpic.cn/mmbiz_gif/hq0PKaHicMTFjtkp7uicm0jlMNJYITbthAQVzRRXZiacNpx5osdGWWPnCvXLPQSgInic5aaAYu6Gf5nCVPk5Ck6BAQ/640?tp=webp&wxfrom=5&wx_lazy=1)*

*无人机拍摄的视频，用MedianFlow进行目标追踪*

你可能会惊讶OpenCV竟然有八种不同的目标追踪工具，他们都可以运用到计算机视觉领域中。

这八种工具包括：

- **BOOSTING Tracker**：和Haar cascades（AdaBoost）背后所用的机器学习算法相同，但是距其诞生已有十多年了。这一追踪器速度较慢，并且表现不好，但是作为元老还是有必要提及的。（最低支持OpenCV 3.0.0）
- **MIL Tracker**：比上一个追踪器更精确，但是失败率比较高。（最低支持OpenCV 3.0.0）
- **KCF Tracker**：比BOOSTING和MIL都快，但是在有遮挡的情况下表现不佳。（最低支持OpenCV 3.1.0）
- **CSRT Tracker**：比KCF稍精确，但速度不如后者。（最低支持OpenCV 3.4.2）
- **MedianFlow Tracker**：在报错方面表现得很好，但是对于快速跳动或快速移动的物体，模型会失效。（最低支持OpenCV 3.0.0）
- **TLD Tracker**：我不确定是不是OpenCV和TLD有什么不兼容的问题，但是TLD的误报非常多，所以不推荐。（最低支持OpenCV 3.0.0）
- **MOSSE Tracker**：速度真心快，但是不如CSRT和KCF的准确率那么高，如果追求速度选它准没错。（最低支持OpenCV 3.4.1）
- **GOTURN Tracker**：这是OpenCV中唯一一深度学习为基础的目标检测器。它需要额外的模型才能运行，本文不详细讲解。（最低支持OpenCV 3.2.0）

我个人的建议：

- 如果追求高准确度，又能忍受慢一些的速度，那么就用CSRT
- 如果对准确度的要求不苛刻，想追求速度，那么就选KCF
- 纯粹想节省时间就用MOSSE

从OpenCV 3开始，目标检测器得到了快速发展，下表总结了不同版本的OpenCV中可食用的追踪器：

![img](https://mmbiz.qpic.cn/mmbiz_jpg/hq0PKaHicMTFjtkp7uicm0jlMNJYITbthA5ctbLyib3ZuoUoGjzHOrbCkNU69kZy8IjtpzwMxJcwyjAiaauNJkGGibw/640?tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



## 开始动手

想要用OpenCV进行目标追踪，首先打开一个新文件，将它命名为opencv_object_tracker.py，然后插入以下代码：

![img](https://mmbiz.qpic.cn/mmbiz_png/hq0PKaHicMTFjtkp7uicm0jlMNJYITbthAs3xWuFUKoQuMzwGoCD0Rzqb3OHudfY2kOStDmLiaibp8qa5Ndiamv7ekw/640?tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

我们开始输入必须的安装包，确保你已经安装了OpenCV（我推荐3.4以上的版本），其次你要安装imutils：

![img](https://mmbiz.qpic.cn/mmbiz_png/hq0PKaHicMTFjtkp7uicm0jlMNJYITbthAd7OGLcRq7iaOXJPgicGc7UtvImh7iaL7S3uejEE8eXYRurEyQrlUxz6RQ/640?tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

输入安装包后，我们开始分析命令行参数：

![img](https://mmbiz.qpic.cn/mmbiz_png/hq0PKaHicMTFjtkp7uicm0jlMNJYITbthAIM2ia9Q9bl3ibKSqia8ibqHO20icmng8d4TTEJEgiccYcE776zdibWqOEqMmA/640?tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

我们的命令行参数包括：

- --video：到达输入视频文件的替代路线。如果该参数失效，那么脚本将会使用你的网络摄像头。
- --tracker：假设默认追踪器设置的是kcf，一整列可能的追踪器代码表示下一个代码块或下方的部分。

让我们处理追踪器的不同类别：

![img](https://mmbiz.qpic.cn/mmbiz_png/hq0PKaHicMTFjtkp7uicm0jlMNJYITbthAOmxMenEjZnyDkuQwnmF09VL2I3eXdwJVKgKQJzbjR8ibQHCdMmoaIkg/640?tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

图2中我们提到，并不是所有追踪器都使用OpenCV 3以上的版本。在3.3版本上，同样发生了安装上的变化，在3.3之前，追踪器必须用cv2. Tracker_create创造，并且要在追踪器的名字上用大写字符串标注（22和23行）。

对于3.3以上的版本，每个追踪器可以用各自的函数创造，如cv2. TrackerKCF_create。词典OPENCV_OBJECT_TRACKERS包含了7种OpenCV的目标追踪器（30—38行）。它将目标追踪器的命令行参数字符串映射到实际的OpenCV追踪器函数上。

其中42行里的tracker目的是根据追踪器命令行参数以及从OPENCV_OBJECT_TRACKERS得来的相关重要信息。

> *注意：这里我没有将GOTURN加入到追踪器设置中因为它还需要额外的模型文件。*

我们还对initBB进行初始化（46行），当我们用鼠标选中目标物体时，该变量会显示目标物体的边界框坐标。

接下来，让我们对视频流和FPS进行初始化：

![img](https://mmbiz.qpic.cn/mmbiz_png/hq0PKaHicMTFjtkp7uicm0jlMNJYITbthAj99Cq4VnwOrjFocRwz4x6MleOkNQZRvEWdOFmIKTQua5APiayxhcNWA/640?tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

49—52行是访问网络摄像头的步骤，这里我们设定一个一秒钟的暂停时间，好让摄像头传感器进行“热身”。

接着--video命令行参数会出现，所以我们可以从视频文件中对视频流进行初始化（55—56行）。

下面是从视频流中进行帧数迭代循环的步骤：

![img](https://mmbiz.qpic.cn/mmbiz_png/hq0PKaHicMTFjtkp7uicm0jlMNJYITbthAiaxlnXD7hmia8uDwG7k4d0xIUp7YhtXv8Tw6gV854ciaOktBzoZlFcUQA/640?tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

在65和66行中我们提取一个frame，同时在69和70行处理视频文件中没有帧数的情况。

为了让我们的算法处理帧数的速度更快，我们用resize将输入的视频帧调整为50像素（74行），这里处理的数据越少，速度就会越快。

之后，我们提取视频帧的宽度和高度，之后我们会用到高度（75行）。

目标物体选定之后，我们就可以用以下代码进行处理：

![img](https://mmbiz.qpic.cn/mmbiz_png/hq0PKaHicMTFjtkp7uicm0jlMNJYITbthAk4KL0CS5nCtOJMibyDH7Ta7Rd4oXD0sPPWecxe2FVhpCMT2ruGoODZw/640?tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

如果目标物体已经被选定，我们需要不断更新目标物体的位置，为了做到这一点，我们在80行使用update方法，它会定位目标物体的新位置并且返回一个success和box值。

如果顺利的话，我们可以在frame中得到更新后的边界框位置。注意，追踪器可能会跟丢目标物并且报错，所以success可能不会一直是True。接着更新FPS估计器。

接着，让我们展示一下frame，以及用鼠标选取目标物体：

![img](https://mmbiz.qpic.cn/mmbiz_png/hq0PKaHicMTFjtkp7uicm0jlMNJYITbthAT6ib0o4stcEhCWyCG3kVl25PFFCaNCkRm9Bc48ue3o7gWa6HgLITl6Q/640?tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

我们将展示frame，并且继续迭代循环，键入其他指令才会停止。

当键入“s”后，我们用cv2.selectROI“选择”一个目标ROI。这一函数可以让你在视频暂停的时候手动选择一个ROI：

![img](https://mmbiz.qpic.cn/mmbiz_gif/hq0PKaHicMTFjtkp7uicm0jlMNJYITbthAWVXtjT3LSoQ1ac3FrZVZDxYhlwfSJtjtl4rc1eAYD7u92n0icKFe7Vg/640?tp=webp&wxfrom=5&wx_lazy=1)

用户必须画出边界框后按回车或空格键来确定所选区域。如果你需要重新选择，就按“ESCAPE”键。

同样，我们还能用真实的目标探测器来进行手动选择。

最后，如果视频有更多的帧，或者出现了“quit”的情况，如何退出这一循环：

![img](https://mmbiz.qpic.cn/mmbiz_png/hq0PKaHicMTFjtkp7uicm0jlMNJYITbthAf2XtOlzZVgRYGkjfxJDCtyg7glXSPCPknGaia7U2gbBAJwf14oCPkZA/640?tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

最后一个模块展示了我们如何停止循环，这时所有的指标都输出并且窗口关闭。

## 目标追踪结果

提示：为了确保你跟上本文的进度，并且用到了文章中的OpenCV方法，请先确保你在“下载资料”中下载了代码和视频。

之后，打开一个终端并执行以下命令：

![img](https://mmbiz.qpic.cn/mmbiz_png/hq0PKaHicMTFjtkp7uicm0jlMNJYITbthAdLfMUlyjKzszfNToUBeaQ5Vqt7bbG78kDicRgc8j63nVOcP6Czc5UEA/640?tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

如果你下载了本教程的源代码和视频，那么--video的可用参数在以下文件中：

- american_pharoah.mp4
- dashcam_boston.mp4
- drone.mp4
- nascar_01.mp4
- nascar_02.mp4
- race.mp4
- ……

--tracker中的参数在：

- csrt
- kcf
- boosting
- mil
- tld
- medianflow
- mosse

你也可以用计算机的摄像头：

![img](https://mmbiz.qpic.cn/mmbiz_png/hq0PKaHicMTFjtkp7uicm0jlMNJYITbthALc4qMxMwU8ZXJSBXmlpI1pRCVfWDcknNNoHwR6eISqib9TXM3VsY0bg/640?tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



## 总结

这篇文章讲解了如何用OpenCV进行目标物体追踪。具体来说，我们回顾了库里的八种算法：

- CSRF
- KCF
- Boosting
- MIL
- TLD
- MedianFlow
- MOSSE
- GOTURN

我们可以将OpenCV的这八种追踪器用于不同的任务，包括短跑比赛、赛马、赛车、无人机追踪等高速视频上。如需要文中的代码和视频，请点击下方原文地址获取。

原文地址：www.pyimagesearch.com/2018/07/30/opencv-object-tracking/


# 相关

- [OpenCV实战 | 八种目标跟踪算法](https://mp.weixin.qq.com/s?__biz=MzUxNjcxMjQxNg==&mid=2247485157&idx=1&sn=71958fb2951cb3f7ff7ce868a177573c&chksm=f9a2746aced5fd7c9ef86660657f6befe8a70029505b8d1a17cfe212be35f8356baa37b8d9e1&mpshare=1&scene=1&srcid=081271tjeMPDvfKGw57CznR7#rd)
