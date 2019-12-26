---
title: numpy 实现主流机器学习模型 资料
toc: true
date: 2019-07-12
---
**手写 NumPy 全家福**

作者在 GitHub 中提供了模型/模块的实现列表，列表结构基本就是代码文件的结构了。整体上，模型主要分为两部分，即传统机器学习模型与主流的深度学习模型。

其中浅层模型既有隐马尔可夫模型和提升方法这样的复杂模型，也包含了线性回归或最近邻等经典方法。而深度模型则主要从各种模块、层级、损失函数、最优化器等角度搭建代码架构，从而能快速构建各种神经网络。

除了模型外，整个项目还有一些辅助模块，包括一堆预处理相关的组件和有用的小工具。

该 repo 的模型或代码结构如下所示：

**1. 高斯混合模型**

- EM 训练

**2. 隐马尔可夫模型**

- 维特比解码
- 似然计算
- 通过 Baum-Welch/forward-backward 算法进行 MLE 参数估计

**3. 隐狄利克雷分配模型（主题模型）**

- 用变分 EM 进行 MLE 参数估计的标准模型
- 用 MCMC 进行 MAP 参数估计的平滑模型

**4. 神经网络**

4.1 层/层级运算

- Add
- Flatten
- Multiply
- Softmax
- 全连接/Dense
- 稀疏进化连接
- LSTM
- Elman 风格的 RNN
- 最大+平均池化
- 点积注意力
- 受限玻尔兹曼机 (w. CD-n training)
- 2D 转置卷积 (w. padding 和 stride)
- 2D 卷积 (w. padding、dilation 和 stride)
- 1D 卷积 (w. padding、dilation、stride 和 causality)

4.2 模块

- 双向 LSTM
- ResNet 风格的残差块（恒等变换和卷积）
- WaveNet 风格的残差块（带有扩张因果卷积）
- Transformer 风格的多头缩放点积注意力

4.3 正则化项

- Dropout
- 归一化
- 批归一化（时间上和空间上）
- 层归一化（时间上和空间上）

4.4 优化器

- SGD w/ 动量
- AdaGrad
- RMSProp
- Adam

4.5 学习率调度器

- 常数
- 指数
- Noam/Transformer
- Dlib 调度器

4.6 权重初始化器

- Glorot/Xavier uniform 和 normal
- He/Kaiming uniform 和 normal
- 标准和截断正态分布初始化

4.7 损失

- 交叉熵
- 平方差
- Bernoulli VAE 损失
- 带有梯度惩罚的 Wasserstein 损失

4.8 激活函数

- ReLU
- Tanh
- Affine
- Sigmoid
- Leaky ReLU

4.9 模型

- Bernoulli 变分自编码器
- 带有梯度惩罚的 Wasserstein GAN

4.10 神经网络工具

- col2im (MATLAB 端口)
- im2col (MATLAB 端口)
- conv1D
- conv2D
- deconv2D
- minibatch

**5. 基于树的模型**

- 决策树 (CART)
- [Bagging] 随机森林
- [Boosting] 梯度提升决策树

**6. 线性模型**

- 岭回归
- Logistic 回归
- 最小二乘法
- 贝叶斯线性回归 w/共轭先验

**7.n 元序列模型**

- 最大似然得分
- Additive/Lidstone 平滑
- 简单 Good-Turing 平滑

**8. 强化学习模型**

- 使用交叉熵方法的智能体
- 首次访问 on-policy 蒙特卡罗智能体
- 加权增量重要采样蒙特卡罗智能体
- Expected SARSA 智能体
- TD-0 Q-learning 智能体
- Dyna-Q / Dyna-Q+ 优先扫描

**9. 非参数模型**

- Nadaraya-Watson 核回归
- k 最近邻分类与回归

**10. 预处理**

- 离散傅立叶变换 (1D 信号)
- 双线性插值 (2D 信号)
- 最近邻插值 (1D 和 2D 信号)
- 自相关 (1D 信号）
- 信号窗口
- 文本分词
- 特征哈希
- 特征标准化
- One-hot 编码/解码
- Huffman 编码/解码
- 词频逆文档频率编码

**11. 工具**

- 相似度核
- 距离度量
- 优先级队列
- Ball tree 数据结构



# 相关

- [惊为天人，NumPy手写全部主流机器学习模型，代码超 3 万行](https://www.jiqizhixin.com/articles/David-Bourgin-numpy-ml)
- [numpy-ml](https://github.com/ddbourgin/numpy-ml)
