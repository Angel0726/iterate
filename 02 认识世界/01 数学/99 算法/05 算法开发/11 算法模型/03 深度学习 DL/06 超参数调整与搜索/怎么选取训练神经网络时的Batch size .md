---
title: 怎么选取训练神经网络时的Batch size 
toc: true
date: 2018-10-20
---

# 怎么选区训练神经网络时候的 Batch Size？



- [怎么选取训练神经网络时的 Batch size?](https://www.zhihu.com/question/61607442)

## 为什么优先选择 2 的幂？

机器学习训练时，Mini-Batch 的大小优选为 2 的幂，如 64 或 128，原因是？

GPU 对 2 的幂次的 batch 可以发挥更佳的性能，利于并行化处理

