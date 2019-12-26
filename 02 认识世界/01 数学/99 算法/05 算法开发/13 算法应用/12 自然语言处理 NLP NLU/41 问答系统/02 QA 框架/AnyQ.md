---
title: AnyQ
toc: true
date: 2019-11-17
---
# AnyQ

基于FAQ集合的问答系统

**AnyQ (ANswer Your Questions) 开源项目主要包含面向 FAQ 集合的问答系统框架、文本语义匹配工具 SimNet。**

问答系统框架采用了配置化、插件化的设计，各功能均通过插件形式加入，当前共开放了 20+ 种插件。开发者可以使用 AnyQ 系统快速构建和定制适用于特定业务场景的 FAQ 问答系统，并加速迭代和升级。

SimNet 是百度自然语言处理部于 2013 年自主研发的语义匹配框架，该框架在百度各产品上广泛应用，主要包括 BOW、CNN、RNN、MM-DNN 等核心网络结构形式，同时基于该框架也集成了学术界主流的语义匹配模型，如 MatchPyramid、MV-LSTM、K-NRM 等模型。SimNet 使用 PaddleFluid 和 Tensorflow 实现，可方便实现模型扩展。使用 SimNet 构建出的模型可以便捷的加入 AnyQ 系统中，增强 AnyQ 系统的语义匹配能力。


AnyQ的框架结构：


![img](https://mmbiz.qpic.cn/mmbiz_png/VBcD02jFhgkeqI4keicpcSFGiaqyWevicnfkcrSFog9c6X9N8nY4y1yAB4URibvTmR3wbpvic04v8icibfKEZ0JKrXtpg/640?tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)


项目链接

https://github.com/baidu/AnyQ
