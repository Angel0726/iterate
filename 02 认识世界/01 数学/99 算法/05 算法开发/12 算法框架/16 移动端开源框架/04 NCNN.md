---
title: 04 NCNN
toc: true
date: 2019-08-31
---

# NCNN


ncnn 是 2017 年腾讯优图实验室开源的移动端框架，使用 C++ 实现，支持 Android 和 IOS 两大平台。


ncnn 已经被用于腾讯生态中的多款产品，包括微信，天天 P 图等。

项目地址和相关学习资料如下。

```
https://github.com/Tencent/ncnn
https://github.com/BUG1989/caffe-int8-convert-tools.git
```

4、特点：

- 1）NCNN考虑了手机端的硬件和系统差异以及调用方式，架构设计以手机端运行为主要原则。
- 2）无第三方依赖，跨平台，手机端 CPU 的速度快于目前所有已知的开源框架（以开源时间为参照对象）。
- 3）基于 ncnn，开发者能够将深度学习算法轻松移植到手机端高效执行，开发出人工智能 APP。

5、功能：

- 1、NCNN支持卷积神经网络、多分支多输入的复杂网络结构，如 vgg、googlenet、resnet、squeezenet 等。
- 2、NCNN无需依赖任何第三方库。
- 3、NCNN全部使用 C/c++实现，以及跨平台的 cmake 编译系统，可轻松移植到其他系统和设备上。
- 4、汇编级优化，计算速度极快。使用 ARM NEON指令集实现卷积层，全连接层，池化层等大部分 CNN 关键层。
- 5、精细的数据结构设计，没有采用需消耗大量内存的通常框架——im2col + 矩阵乘法，使得内存占用极低。
- 6、支持多核并行计算，优化 CPU 调度。
- 7、整体库体积小于 500K，可精简到小于 300K。
- 8、可扩展的模型设计，支持 8bit 量化和半精度浮点存储。
- 9、支持直接内存引用加载网络模型。
- 10、可注册自定义层实现并扩展。

6、NCNN在 Android 端部署示例

- 1）选择合适的 Android Studio版本并安装。
- 2）根据需求选择 NDK 版本并安装。
- 3）在 Android Studio上配置 NDK 的环境变量。
- 4）根据自己需要编译 NCNN sdk

```
mkdir build-android cd build-android cmake -DCMAKE_TOOLCHAIN_FILE=$ANDROID_NDK/build/cmake/android.toolchain.cmake \ -DANDROID_ABI="armeabi-v7a" -DANDROID_ARM_NEON=ON \ -DANDROID_PLATFORM=android-14 .. make make install
```

安装完成之后，install下有 include 和 lib 两个文件夹。

备注：

```
ANDROID_ABI 是架构名字，"armeabi-v7a" 支持绝大部分手机硬件
ANDROID_ARM_NEON 是否使用 NEON 指令集，设为 ON 支持绝大部分手机硬件
ANDROID_PLATFORM 指定最低系统版本，"android-14" 就是 android-4.0
```

- 5）进行 NDK 开发。

```
1）assets文件夹下放置你的 bin 和 param 文件。
2）jni文件夹下放置你的 cpp 和 mk 文件。
3）修改你的 app gradle文件。
4）配置 Android.mk和 Application.mk文件。
5）进行 java 接口的编写。
6）读取拷贝 bin 和 param 文件（有些则是 pb 文件，根据实际情况）。
7）进行模型的初始化和执行预测等操作。
8）build。
9）cd到 src/main/jni目录下，执行 ndk-build，生成.so文件。
10）接着就可写自己的操作处理需求。
```



# 相关

- [DeepLearning-500-questions](https://github.com/scutan90/DeepLearning-500-questions)
