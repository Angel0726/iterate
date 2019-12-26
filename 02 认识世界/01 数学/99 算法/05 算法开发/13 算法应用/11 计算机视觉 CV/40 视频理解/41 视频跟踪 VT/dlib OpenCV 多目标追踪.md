---
title: dlib OpenCV 多目标追踪
toc: true
date: 2019-10-29
---
# dlib OpenCV 多目标追踪


Multiple objects tracker using openCV and dlib

<center>

![mark](http://images.iterate.site/blog/image/20191023/dnCQOvAll54J.png?imageslim)

</center>

Requsites

- openCV 3.0
- dlib-19.3
- visual studio 15 (c+11 is necessary)



Directories

\1. dlib-19.3
dlib

\2. tracker
Source files and Visual Studio solution

\3. video_frame
Video frames



How to compile

To run this program, you need to compile opencv and dlib. You will be able to compile opencv easily from many websites. The folliwing instruction is how to compile dlib given that you are done with opencv compiling clearly and using visual studio in window. If you have troubles in compiling dlib then you can just use tracker/vs_solution/MultiObjectTracker.slninstead, but you need to change opencv and dlib path with your environment in the project property.

1. Make a new visual studio project.
2. Include dlib-19.3/dlib/all/source.cpp and tracker/src/Tracker.cpp to your project. Tracker.cpp file will be divided into several files soon.
3. Make new folder(filter) in your project. Include all files(all cpp files and headers) in dlib-19.3/dlib/external/libjpeg to your project.
4. Open project properties -> Configuration Properties -> C/C++. You can see additional include directory. Write dlib-19.3 (next directory should be dlib) to there.
5. Opem project properties -> Configuration Properties -> C/C++ -> Preprocessor. You can see Preprocessor Definitions. Write DLIB_JPEG_STATIC and DLIB_JPEG_SUPPORT to there.
6. Build.



How to run

1. Open cmd and move to the directory which has exe file.
2. Write command : MultiObjectTracker.exe "YOUR_FRAME_IMAGE_PATH". If you're using frame images in this repository, the command is MultiObjectTracker.exe "video_frame".
3. You will be able to see a window with the first frame image. Draw rectangles around the targets you want to track. You can use ESC if you want to remove rectangles and ENTER if you are done with drawing.
4. Now you can see tracking all targets you made.



链接：

https://github.com/eveningglow/multi-object-tracker



原文链接：

https://m.weibo.cn/1402400261/4122559756530330


# 相关

- [【推荐】(dlib/OpenCV)多目标追踪](https://weibo.com/1402400261/F9sSHroPU?type=comment#_rnd1571840833857)
