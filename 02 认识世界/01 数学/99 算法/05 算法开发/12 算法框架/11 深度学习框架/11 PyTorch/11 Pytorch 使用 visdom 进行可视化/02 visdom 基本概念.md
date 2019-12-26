---
title: 02 visdom 基本概念
toc: true
date: 2019-06-15
---
# Visdom 基本概念

Visdom 有一组简单的特性，可以用它们组合成不同的用例。


## Panes（窗格）

UI 刚开始是个白板，你可以用图像和文本填充它。这些填充的数据出现在 Panes 中，你可以对这些 Panes 进行拖放、删除、调整大小和销毁操作。Panes 是保存在 Environments（环境）中的，Environments（环境）的状态存储在会话之间。你可以下载 Panes 中的内容，包括你在 SVG 中的绘图。<span style="color:red;">嗯，其实非常想知道的是怎么把 pytorch 训练过程中的信息更新到 panes 上面。</span>

可以使用浏览器的放大缩小功能来调整 UI 的大小。

## Environments（环境）

可以使用 Envs 对可视化空间进行分区。每个用户都会有一个叫作 main 的 Envs。可以通过编程或 UI 创建新的 Envs。Envs 的状态是长期保存的。<span style="color:red;">Envs 的状态是可以长期保存的是什么意思？</span>

可以通过 http://localhost.com:8097/env/main 访问特定的 ENV。如果服务器是被托管的，那么可以将此 URL 分享给其他人，那么其他人也会看到可视化结果。<span style="color:red;">是的，非常想知道这个是怎么做的？嗯，感觉这个还是挺有用的，因为可以远程看到训练的状态。</span>

在初始化服务器的时候，Envs 默认通过 $HOME/.visdom/ 加载。也可以将自定义的路径当作命令行参数传入。如果移除了 Envs 文件夹下的 .json 文件，那么相应的环境也会被删除。<span style="color:red;">嗯。</span>

## State（状态）

一旦你创建了一些可视化，状态是被保存的。服务器自动缓存你的可视化，如果你重新加载网页，你的可视化就会重新出现。

- Save：你可以通过点击 save 按钮手动保存 Envs。它首先会序列化 Envs 的状态，然后以 .json 文件的形式保存到硬盘上，包括窗口的位置。同样，你也可以通过编程来实现 Envs 的保存。例如数据丰富的演示、模型的训练仪表板，或者系统实验。这种设计依旧可以使这些可视化十分容易分享和复用。<span style="color:red;">没有很清楚这种 save 保存的是什么？</span>
- Fork：输入一个新的 Envs 名字，“保存”会建立一个新的 Envs：有效地分割之前的状态。





# 相关

- 《深度学习框架 Pytorch 快速开发与实战》
