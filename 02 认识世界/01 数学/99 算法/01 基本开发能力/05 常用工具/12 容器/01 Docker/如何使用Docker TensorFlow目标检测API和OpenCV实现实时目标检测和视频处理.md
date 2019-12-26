---
title: 如何使用Docker TensorFlow目标检测API和OpenCV实现实时目标检测和视频处理
toc: true
date: 2019-11-17
---
# 如何使用Docker、TensorFlow目标检测API和OpenCV实现实时目标检测和视频处理


> 本文展示了如何使用 Docker 容器中的 TensorFlow 目标检测 API，通过网络摄像头执行实时目标检测，同时进行视频后处理。作者使用的是 OpenCV 和 Python3 多进程和多线程库。本文重点介绍了项目中出现的问题以及作者采用的解决方案。



github 地址：

https://github.com/lbeaucourt/Object-detection



![img](https://mmbiz.qpic.cn/mmbiz_gif/KmXPKA19gW8kvbFO2qbs7Apc1Ydbf0Fy6fRb9TU2b3JheNJ6M6YJjKpPAkdxUpc83Zj9bvSWJicvKUlc3THZSrQ/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

*用 YouTube 视频进行视频处理测试*



**动机**



我是从这篇文章《Building a Real-Time Object Recognition App with Tensorflow and OpenCV》（https://towardsdatascience.com/building-a-real-time-object-recognition-app-with-tensorflow-and-opencv-b7a2b4ebdc32）开始探索实时目标检测问题，这促使我研究 Python 多进程库，使用这篇文章（https://www.pyimagesearch.com/2015/12/21/increasing-webcam-fps-with-python-and-opencv/）中介绍的方法提高每秒帧数（frames per second，FPS）。为了进一步加强项目的可移植性，我试着将自己的项目整合到 Docker 容器中。这一过程的主要困难在于处理流入和流出容器的视频流。



此外，我还在项目中添加了视频后处理功能，这一功能也使用了多进程，以减少视频处理的时间（如果使用原始的 TensorFlow 目标检测 API 处理视频，会需要非常非常长的时间）。



在我的个人电脑上可以同时进行高性能的实时目标检测和视频后处理工作，该过程仅使用了 8GB 的 CPU。



**用于数据科学的 Docker**



鉴于大量文章对 TensorFlow 目标检测 API 的实现进行了说明，因此此处不再赘述。作为一名数据科学家，我将展示如何在日常工作中使用 Docker。请注意，我用的是来自 Tensorflow 的经典 ssd_mobilenet_v2_coco 模型。我在本地复制了模型（.pb 文件）和对应的标签映射，以便后续个人模型的运行。



我相信现在使用 Docker 已经是数据科学家最基础的技能了。在数据科学和机器学习的世界中，每周都会发布许多新的算法、工具和程序，在个人电脑上安装并测试它们很容易让系统崩溃（亲身经历！）。为了防止这一悲惨事件的发生，我现在用 Docker 创建数据科学工作空间。



你可以在我的库中找到该项目的相关 Docker 文件。以下是我安装 TensorFlow 目标检测的方法（按照官方安装指南进行）：



```
# Install tensorFlow
RUN pip install -U tensorflow
# Install tensorflow models object detection
RUN git clone https://github.com/tensorflow/models /usr/local/lib/python3.5/dist-packages/tensorflow/models
RUN apt-get install -y protobuf-compiler python-pil python-lxml python-tk
#Set TF object detection available
ENV PYTHONPATH "$PYTHONPATH:/usr/local/lib/python3.5/dist-packages/tensorflow/models/research:/usr/local/lib/python3.5/dist-packages/tensorflow/models/research/slim"
RUN cd /usr/local/lib/python3.5/dist-packages/tensorflow/models/research && protoc object_detection/protos/*.proto --python_out=.
```





同样，我还安装了 OpenCV：





```
# Install OpenCV
RUN git clone https://github.com/opencv/opencv.git /usr/local/src/opencv
RUN cd /usr/local/src/opencv/ && mkdir build
RUN cd /usr/local/src/opencv/build && cmake -D CMAKE_INSTALL_TYPE=Release -D CMAKE_INSTALL_PREFIX=/usr/local/ .. && make -j4 && make install
```



建立图像会花几分钟的时间，但是之后用起来就会又快又容易。



**实时目标检测**



首先我试着将目标检测应用于网络摄像头视频流。《Building a Real-Time Object Recognition App with Tensorflow and OpenCV》完整地介绍了这项工作的主体部分。困难在于如何将网络摄像头视频流传送到 Docker 容器 中，并使用 X11 服务器恢复输出流，使视频得以显示出来。



**将视频流传送到容器中**



使用 Linux 的话，设备在 /dev/ 目录中，而且通常可以作为文件进行操作。一般而言，你的笔记本电脑摄像头是「0」设备。为了将视频流传送到 docker 容器中，要在运行 docker 图像时使用设备参数：





```
docker run --device=/dev/video0
```



对 Mac 和 Windows 用户而言，将网络摄像头视频流传送到容器中的方法就没有 Linux 那么简单了（尽管 Mac 是基于 Unix 的）。本文并未对此进行详细叙述，但 Windows 用户可以使用 Virtual Box 启动 docker 容器来解决该问题。



**从容器中恢复视频流**



解决这个问题时花了我一些时间（但解决方案仍旧不尽如人意）。我在 http://wiki.ros.org/docker/Tutorials/GUI 网页发现了一些使用 Docker 图形用户界面的有用信息，尤其是将容器和主机的 X 服务器连接，以显示视频。



首先，你必须要放开 xhost 权限，这样 docker 容器才能通过读写进 X11 unix socket 进行正确显示。首先要让 docker 获取 X 服务器主机的权限（这并非最安全的方式）：





```
xhost +local:docker
```



在成功使用该项目后，再将控制权限改回默认值：





```
xhost -local:docker
```



创建两个环境变量 XSOCK 和 XAUTH：





```
XSOCK=/tmp/.X11-unix
XAUTH=/tmp/.docker.xauth
```



XSOCK 指 X11 Unix socket，XAUTH 指具备适当权限的 X 认证文件：





```
xauth nlist $DISPLAY | sed -e 's/^..../ffff/' | xauth -f $XAUTH nmerge -
```



最后，我们还要更新 docker 运行的命令行。我们发送 DISPLAY 环境变量，为 X11 Unix socket 和带有环境变量 XAUTHORITY 的 X 认证文件安装卷：





```
docker run -it --rm --device=/dev/video0 -e DISPLAY=$DISPLAY -v $XSOCK:$XSOCK -v $XAUTH:$XAUTH -e XAUTHORITY=$XAUTH
```



现在我们可以运行 docker 容器了，而它完成后是这样的：





![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8kvbFO2qbs7Apc1Ydbf0FytibevFMOKxZYr43qxvGm1WxgicnMlPaAbicIicO3bvAIHg5edneXCG1dwQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*工作中的我和其他物体（因为害羞就不露脸了）。*



尽管主机配置有 X 服务器，但我还是无法完全删除我代码中疑似错误的部分。OpenCV 需要通过调用 Python 脚本使用 cv2.imshow 函数进行「初始化」。我收到了以下错误信息：





```
The program 'frame' received an X Window System error.
```



然后，我可以调用 Python 主脚本（my-object-detection.py），视频流也可以发送到主机的显示器了。我对使用第一个 Python 脚本初始化 X11 系统的解决方法并不十分满意，但是我尚未发现其他可以解决这一问题的办法。



**视频处理**



为了成功用网络摄像头实时运行目标检测 API，我用了线程和多进程 Python 库。线程用来读取网络摄像头的视频流，帧按队列排列，等待一批 worker 进行处理（在这个过程中 TensorFlow 目标检测仍在运行）。



就视频处理而言，使用线程是不可能的，因为必须先读取所有视频帧，worker 才能对输入队列中的第一帧视频应用目标检测。当输入队列满了时，后面读取的视频帧会丢失。也许使用大量 worker 和多个队列可以解决这一问题（但会产生大量的计算损失）。



简单队列的另一个问题是，由于分析时间不断变化，输出队列中的视频帧无法以与输入队列相同的顺序发布。



为了添加视频处理功能，我删除了读取视频帧的线程，而是通过以下代码来读取视频帧：





```
while True:
  # Check input queue is not full
  if not input_q.full():
     # Read frame and store in input queue
     ret, frame = vs.read()
      if ret:
        input_q.put((int(vs.get(cv2.CAP_PROP_POS_FRAMES)),frame))
```



如果输入队列未满，则接下来会从视频流中读取下一个视频帧，并将其放到队列中去。否则输入队列中没有视频帧是不会进行任何处理的。



为了解决视频帧顺序的问题，我使用优先级队列作为第二输出队列：



\1. 读取视频帧，并将视频帧及其对应的编号一并放到输入队列中（实际上是将 Python 列表对象放到队列中）。

\2. 然后，worker 从输入队列中取出视频帧，对其进行处理后再将其放入第一个输出队列（仍带有相关的视频帧编号）。





```
while True:
  frame = input_q.get()
frame_rgb = cv2.cvtColor(frame[1], cv2.COLOR_BGR2RGB)
  output_q.put((frame[0], detect_objects(frame_rgb, sess, detection_graph)))
```



\3. 如果输出队列不为空，则提取视频帧，并将视频帧及其对应编号一起放入优先级队列，视频编号即为优先级编号。优先级队列的规模被设置为其他队列的三倍。





```
# Check output queue is not empty
if not output_q.empty():
  # Recover treated frame in output queue and feed priority queue
  output_pq.put(output_q.get())
```



\4. 最后，如果输出优先级队列不为空，则取出优先级最高（优先级编号最小）的视频（这是标准优先级队列的运作）。如果优先级编号与预期视频帧编号一致，则将这一帧添加到输出视频流中（如果有需要的话将这一帧写入视频流），不一致的话则将这一帧放回优先级队列中。





```
# Check output priority queue is not empty
 if not output_pq.empty():
 prior, output_frame = output_pq.get()
 if prior > countWriteFrame:
 output_pq.put((prior, output_frame))
 else:
 countWriteFrame = countWriteFrame + 1
 # Do something with your frame
```



要停止该进程，需要检查所有的队列是否为空，以及是否从该视频流中提取出所有的视频了。





```
if((not ret) & input_q.empty() &
 output_q.empty() & output_pq.empty()):
 break
```



**总结**



本文介绍了如何使用 docker 和 TensorFlow 实现实时目标检测项项目。如上文所述，docker 是测试新数据科学工具最安全的方式，也是我们提供给客户打包解决方案最安全的方式。本文还展示了如何使用《Building a Real-Time Object Recognition App with Tensorflow and OpenCV》中的原始 Python 脚本执行多进程视频处理。



# 相关

- [教程 | 如何使用Docker、TensorFlow目标检测API和OpenCV实现实时目标检测和视频处理](https://mp.weixin.qq.com/s?__biz=MzA3MzI4MjgzMw==&mid=2650741123&idx=4&sn=11b3d4e9d07a010bcbc445a456960580&chksm=871addfdb06d54ebe030c1fb91e85e7ab51927a853644fcbf4e03c2b8550f250cdd73363964e&mpshare=1&scene=1&srcid=0421Jk81HUokRKxB9icM7vkU#rd)
