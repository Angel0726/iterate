---
title: 01 在 Linux 系统上安装
toc: true
date: 2019-10-29
---
# 在 Linux 系统上安装

版本

gcc --version  需>4.8
python 2.7+
cmake --version





安装cmake



cd /usr/local/src/
wget http://www.cmake.org/files/v3.1/cmake-3.1.3.tar.gz
tar -zxvf cmake-3.1.3.tar.gz
cd cmake-3.1.3/
 ./configure
make && make install
cmake --version





安装numpy

cd ../
yum install python-devel
wget https://pypi.python.org/packages/source/n/numpy/numpy-1.9.1.tar.gz#md5=78842b73560ec378142665e712ae4ad9
tar -zxvf numpy-1.9.1.tar.gz
cd numpy-1.9.1/
python setup.py install
python setup.py install





安装其他扩展

cd ../
  915  python
  916  yum install -y gcc gcc-c++ gtk+-devel libjpeg-devel libtiff-devel jasper-devel libpng-devel zlib-devel cmake
  917  yum install git gtk2-devel pkgconfig numpy python python-pip python-devel gstreamer-plugins-base-devel libv4l ffmpeg-devel
  918  yum install mplayer mencoder flvtool2
  919  yum install libdc1394
  920  yum install gtk*




安装opencv3.0

wget https://github.com/Itseez/opencv/archive/3.0.0-beta.zip
 unzip 3.0.0-beta.zip
  973  cd opencv-3.0.0-beta/
  974  mkdir build
cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=./build -D  WITH_IPP=OFF ../opencv-3.0.0-beta
make -j8
make install
cd lib
ll cv2.so



处理错误：

“CMake Warning at 3rdparty/ippicv/downloader.cmake:54 (message):
  ICV: Local copy of ICV package has invalid MD5 hash:
  0103b909e19ca9c6497a7ae696c16480 (expected:
  8b449a536a2157bcad08a2b9f266828b)
Call Stack (most recent call first):
  3rdparty/ippicv/downloader.cmake:108 (_icv_downloader)
  cmake/OpenCVFindIPP.cmake:235 (include)
  cmake/OpenCVFindLibsPerf.cmake:12 (include)
  CMakeLists.txt:526 (include)




-- ICV: Downloading ippicv_linux_20141027.tgz...
CMake Error at 3rdparty/ippicv/downloader.cmake:71 (file):
  file DOWNLOAD HASH mismatch


    for file: [/home/jason/program/opencv-3.0.0/3rdparty/ippicv/downloads/linux-8b449a536a2157bcad08a2b9f266828b/ippicv_linux_20141027.tgz]
      expected hash: [8b449a536a2157bcad08a2b9f266828b]
        actual hash: [0103b909e19ca9c6497a7ae696c16480]



Call Stack (most recent call first):
  3rdparty/ippicv/downloader.cmake:108 (_icv_downloader)
  cmake/OpenCVFindIPP.cmake:235 (include)
  cmake/OpenCVFindLibsPerf.cmake:12 (include)
  CMakeLists.txt:526 (include)”





 下载文件 http://sourceforge.net/projects/opencvlibrary/files/3rdparty/ippicv/ippicv_linux_20141027.tgz/download

覆盖原文件即可

mv /usr/local/src/opencv-3.0.0-beta/3rdparty/ippicv/downloads/ippicv_linux_20141027.tgz /usr/local/src/opencv-3.0.0-beta/3rdparty/ippicv/downloads/linux-8b449a536a2157bcad08a2b9f266828b/


# 相关

- [centos7 安装 opencv3.0](https://blog.csdn.net/design321/article/details/47811099)
