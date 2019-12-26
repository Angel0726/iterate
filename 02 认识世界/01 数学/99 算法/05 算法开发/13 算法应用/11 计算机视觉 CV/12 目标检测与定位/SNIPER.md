---
title: SNIPER
toc: true
date: 2019-11-17
---
**03 SNIPER：高效的多尺度物体检测算法**



**Github得星：1550**



SNIPER是一种有效的多尺度训练方法，用于实例级识别任务，如对象检测和实例级分割。 SNIPER不是处理图像金字塔中的所有像素，而是选择性地处理地面实况对象周围的上下文区域（a.k.a芯片）。由于它在低分辨率芯片上运行，因此显著加速了多尺度训练。由于其内存高效设计，SNIPER可以在训练期间受益于批量标准化，并且可以在单个GPU上实现更大批量大小的实例级识别任务。



![img](https://mmbiz.qpic.cn/mmbiz_png/ldSjzkNDxlnyABkicKXelU1B4YCibdWJwAHVWkFpRO28XUNM3OSxLVHbNCnFFO1VtyyMwvt5iav331WVibXsfZburQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



项目地址：

https://github.com/mahyarnajibi/SNIPER


# 相关
