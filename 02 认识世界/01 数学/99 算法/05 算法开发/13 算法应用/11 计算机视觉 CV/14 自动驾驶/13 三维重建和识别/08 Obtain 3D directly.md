---
title: 08 Obtain 3D directly
toc: true
date: 2018-08-18 16:35:06
---


Obtain 3D directly

直接从 3D 获取信息

- Laser range sensor
    - Time of flight
    - Kinect


### Photometric stereo

![](http://images.iterate.site/blog/image/180817/gCBim46dja.png?imageslim){ width=55% }

![](http://images.iterate.site/blog/image/180817/BiDd0dAm28.png?imageslim){ width=55% }

- Given 3 or more known light source we can find the normal N
- From the set of N we can approximate the surface
http://www.taurusstudio.net/research/photex/ps/equation.htm
http://www.wisdom.weizmann.ac.il/~vision/photostereo/



### Photometric stereo using multiple cameras and multiple light sources

![](http://images.iterate.site/blog/image/180817/78HEHfBal9.png?imageslim){ width=55% }


Dynamic Shape Capture using Multi-View Photometric Stereo SIGGRAPH 2009
http://www.youtube.com/watch?v=9hgs5zN38lk





### Multiple cameras fro human body reconstruction

![](http://images.iterate.site/blog/image/180817/1bLbHl6a60.png?imageslim){ width=55% }

Homepage://media.au.tsinghua.edu.cn


### Experimental Results

![](http://images.iterate.site/blog/image/180817/84bGG7Gjl6.png?imageslim){ width=55% }

![](http://images.iterate.site/blog/image/180817/LJ2emDCm7i.png?imageslim){ width=55% }



### Multiple camera doom

![](http://images.iterate.site/blog/image/180817/jAeBfD125e.png?imageslim){ width=55% }


http://www.mpi-inf.mpg.de/~yliu/


想直接获取深度信息的另一个方法：



### Structured light method

Calculate the shape by how the strip is distorted.

![](http://images.iterate.site/blog/image/180817/62GFgjDbD9.png?imageslim){ width=55% }


http://www.laserfocusworld.com/articles/2011/01/lasers-bring-gesture-recognition-to-the-home.html





### Real time Virtual 3D Scanner - Structured Light Technology
Demo

![](http://images.iterate.site/blog/image/180817/cLglG8f8l1.png?imageslim){ width=55% }


http://www.youtube.com/watch?v=a6pgzNUjh_s




### Time of flight laser method



- Send the IR-laser light to different directions and sense how each beam is delayed.
- Use the delay to calculate the distance of the object point


![](http://images.iterate.site/blog/image/180817/4fHjm7LdEF.png?imageslim){ width=55% }

http://www.laserfocusworld.com/articles/2011/01/lasers-bring-gesture-recognition-to-the-home.html





### LIDAR light detection and
ranging scanner

http://www.youtube.com/watch?v=MuwQTc8KK44

![](http://images.iterate.site/blog/image/180817/jechEbfcbA.png?imageslim){ width=55% }

![](http://images.iterate.site/blog/image/180817/FAdLFG1h0E.png?imageslim){ width=55% }
Leica terrestrial lidar (light detection and ranging) scanner



http://hodcivil.edublogs.org/2011/11/06/lidar-%E2%80%93-light-detection-and-ranging/
http://commons.wikimedia.org/wiki/File:Lidar_P1270901.jpg





### 3D Laser Scanning - Underground Mine Mapping

![](http://images.iterate.site/blog/image/180817/4hCFe03LB5.png?imageslim){ width=55% }

http://www.youtube.com/watch?v=BZbvz8fePeQ



### Motion capture for film production (MOCAP)


![](http://images.iterate.site/blog/image/180817/43F15GIcC8.png?imageslim){ width=55% }

http://www.youtube.com/watch?v=IxJrhnynlN8


http://upload.wikimedia.org/wikipedia/commons/7/73/MotionCapture.jpg
http://www.naturalpoint.com/optitrack/products/s250e/indepth.html


### 3D body scanner

![](http://images.iterate.site/blog/image/180817/2D4IG4idIC.png?imageslim){ width=55% }

http://www.youtube.com/watch?v=86hN0x9RycM

http://www.cyberware.com/products/scanners/ps.html
http://www.cyberware.com/products/scanners/wbx.html




### 3-D Face capture

![](http://images.iterate.site/blog/image/180817/3EG7G1kDD7.png?imageslim){ width=55% }

![](http://images.iterate.site/blog/image/180817/Hj75IF5lC1.png?imageslim){ width=55% }


http://www.captivemotion.com/products/
http://www.youtube.com/watch?v=-TTR0JrocsI&feature=related




### Dimensional Imaging 4D Video Face Capture with Textures

![](http://images.iterate.site/blog/image/180817/J8cAB5LJDi.png?imageslim){ width=55% }

Dimensional Imaging 4D Video Face Capture with Textures
http://www.youtube.com/watch?v=XtTN7tWaXTM&feature=related



### Kinect

![](http://images.iterate.site/blog/image/180817/dA6Ge0899d.png?imageslim){ width=55% }

- Another structure light method
- Use dost rather than strips

http://www.laserfocusworld.com/articles/2011/01/lasers-bring-gesture-recognition-to-the-home.html

![](http://images.iterate.site/blog/image/180817/1ecLBGj3hA.png?imageslim){ width=55% }

See the IR-dots emitted by KINECT

![](http://images.iterate.site/blog/image/180817/3JAek7IcdE.png?imageslim){ width=55% }

![](http://images.iterate.site/blog/image/180817/68G2ebkkhk.png?imageslim){ width=55% }


http://www.youtube.com/watch?v=-gbzXjdHfJA
http://www.youtube.com/watch?v=dTKlNGSH9Po&feature=related





### Novel sensors : light field camera

Spin off from Stanford camera array

- light field camera :LYTRO camera
- Be able to refocus after the picture is taken

![](http://images.iterate.site/blog/image/180817/Dim647c07e.png?imageslim){ width=55% }

https://www.lytro.com/camera
http://www.youtube.com/watch?v=7QV152jc3Ac



light field camera How does it work

![](http://images.iterate.site/blog/image/180817/G2jDl5DGFH.png?imageslim){ width=55% }

http://www.quora.com/Lytro/How-does-the-new-Lytro-camera-work





### 3D (Volumetric) display

Rendering for an Interactive 360º Light Field Display
SIGGRAPH 2007 Papers Proceedings


![](http://images.iterate.site/blog/image/180817/HLC32c83ik.png?imageslim){ width=55% }

![](http://images.iterate.site/blog/image/180817/Jc9KL31Faa.png?imageslim){ width=55% }

http://www.youtube.com/watch?v=h6aUIS44ezE
http://gl.ict.usc.edu/Research/3DDisplay/




### Occlusion-Capable Volumetric 3D Display by Cossairt,etal.
Actuality Systems, Inc

![](http://images.iterate.site/blog/image/180817/50hB2hejC1.png?imageslim){ width=55% }
![](http://images.iterate.site/blog/image/180817/fdhFB8654l.png?imageslim){ width=55% }

http://www.youtube.com/watch?v=8KaQmn2VTzs
http://www.3dcgi.com/cooltech/displays/displays.html



### 3D display
Using a lattice with thin slits, viewer's eyes see different
pixels on the screen to create 3d perception


![](http://images.iterate.site/blog/image/180817/KlhDAa04i3.png?imageslim){ width=55% }

http://www.televisions.com/tv-articles/TV-in-3D/Displaying-3D-Without-Glasses.php





## Convolutional-Recursive Deep Learning for 3D Object Classification

![](http://images.iterate.site/blog/image/180817/CLFE93iim6.png?imageslim){ width=55% }

因为是在 RGBD 的数据集上测得，因此有深度信息。

![](http://images.iterate.site/blog/image/180817/EGL1c94Ida.png?imageslim){ width=55% }


https://papers.nips.cc/paper/4773-convolutional-recursive-deeplearning-for-3d-object-classification.pdf




3D computer vision 的未来/值得思考的问题

- Content search in 3D video data bases 有一堆监控视频，要找出一个人，然后，视频太多了，找出最有代表性的，就基于 content search。注意，这个 3D 指的是图像有在视频中以帧的形式存在，相当于多了一个时间维度。不是说画面是 3D 的。<span style="color:red;">这个还是要注意下的，但是感觉放在这里还是不是很合适，毕竟，这里大部分都是讲的 3D 的 computer graphics 的 </span>
- Shot boundary detection
- Video data mining 基于 video 的。
- Video classification






# 相关

- 七月在线 opencv计算机视觉
