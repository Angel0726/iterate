---
title: DensePose
toc: true
date: 2019-11-17
---
# DensePose



﻿![img](https://mmbiz.qpic.cn/mmbiz_png/ptp8P184xjwmxLdIhgIpa58pOiaGPwnBvicLCicDubcluDvXnnBRjKQooiau1DnjHruuysD1tAT2pk25xo86B6ricdA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)﻿



DensePose 是Facebook 研究院开发的一种实时人体姿态估计方法，它能够将2D RGB 图像中的目标像素映射到3D 表面模型。DensePose 项目旨在通过这种基于3D 表面模型来理解图像中的人体姿态，并能够有效地计算2D RGB 图像和人体3D 表面模型之间的密集对应关系。与人体姿势估计需要使用10或20个人体关节(手腕，肘部等) 不同的是，DenPose 使用超过5000个节点来定义，由此产生的估计准确性和系统速度将加速AR和VR 工作的连接。



> 相关链接：
>
> https://research.fb.com/facebook-open-sources-densepose/
>
> Github 链接：
>
> https://github.com/facebookresearch/DensePose



Facebook AI Research（FAIR）于今年6月18号开源了将2D RGB图像的所有人类像素映射到身体的3D表面模型的实时方法DensePose，这意味着二次元的人类图片可以被转化成三次元模型！



有什么用呢？比如网购的时候传一张自己的照片就可以直接试衣服而且效果感人，比如在手机上有如在练功房一样学跳舞……DensePose在单个GPU上以每秒多帧的速度运行，可以同时处理数十甚至数百个人。



![img](https://mmbiz.qpic.cn/mmbiz_png/ldSjzkNDxlnyABkicKXelU1B4YCibdWJwANjfzpnIWVkWqZ55AlrGPTWgy9NZeQVKhPj1yTONeQ1CSNkibOsfD3ibA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



项目地址：

https://github.com/facebookresearch/DensePose
