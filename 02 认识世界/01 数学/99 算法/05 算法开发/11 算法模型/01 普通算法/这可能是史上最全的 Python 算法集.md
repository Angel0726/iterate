---
title: 这可能是史上最全的 Python 算法集
toc: true
date: 2019-11-17
---
本文是一些机器人算法（特别是自动导航算法）的Python代码合集。

其主要特点有以下三点：选择了在实践中广泛应用的算法；依赖最少；容易阅读，容易理解每个算法的基本思想。希望阅读本文后能对你有所帮助。

前排友情提示，文章较长，建议收藏后再看。



![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhSvZMAt2zKcxGQN3l1NV4LXSAriayI15u06ibNNlXzIcor2tTtgJBKFxkIicJ8tiaRKRaictbrQEssdSg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**目录**



> **环境需求**
>
> **怎样使用**
>
> **本地化**
>
> - 扩展卡尔曼滤波本地化
> - 无损卡尔曼滤波本地化
> - 粒子滤波本地化
> - 直方图滤波本地化
>
> **映射**
>
> - 高斯网格映射
> - 光线投射网格映射
> - k均值物体聚类
> - 圆形拟合物体形状识别
>
> **SLAM**
>
> - 迭代最近点匹配
> - EKF SLAM
> - FastSLAM 1.0
> - FastSLAM 2.0
> - 基于图的SLAM
>
> **路径规划**
>
> - 动态窗口方式
> - 基于网格的搜索
>
> - 迪杰斯特拉算法
> - A*算法
> - 势场算法
>
> - 模型预测路径生成
>
> - 路径优化示例
> - 查找表生成示例
>
> - 状态晶格规划
>
> - 均匀极性采样（Uniform polar sampling）
> - 偏差极性采样（Biased polar sampling）
> - 路线采样（Lane sampling）
>
> - 随机路径图（PRM）规划
> - Voronoi路径图规划
> - 快速搜索随机树（RRT）
>
> - 基本RRT
> - RRT*
> - 基于Dubins路径的RRT
> - 基于Dubins路径的RRT*
> - 基于reeds-shepp路径的RRT*
> - Informed RRT*
> - 批量Informed RRT*
>
> - 三次样条规划
> - B样条规划
> - 贝济埃路径规划
> - 五次多项式规划
> - Dubins路径规划
> - Reeds Shepp路径规划
> - 基于LQR的路径规划
> - Frenet Frame中的最优路径
>
> **路径跟踪**
>
> - 纯追迹跟踪
> - 史坦利控制
> - 后轮反馈控制
> - 线性二次regulator（LQR）转向控制
> - 线性二次regulator（LQR）转向和速度控制
>
> **项目支持**



![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhSvZMAt2zKcxGQN3l1NV4LwYnW1VvkaHWiaL6W1Mr1yiaNLQpxwhyqice9F1yJzMHticssPX515qyvog/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**环境需求**



- Python 3.6.x
- numpy
- scipy
- matplotlib
- pandas
- cvxpy 0.4.x



**![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhSvZMAt2zKcxGQN3l1NV4Lb4ybNEVGnaAvEDwENKzW27LUKFDGZPKcBneWwTaTpaJyG2C3em7libQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)**

**怎样使用**



1. 安装必要的库；
2. 克隆本代码仓库；
3. 执行每个目录下的python脚本；
4. 如果你喜欢，则收藏本代码库：）



**![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhSvZMAt2zKcxGQN3l1NV4LLqyf6BY4rMfY2LsU81MibFjicKDLjMjib5R23h8uo6GtGDY8OufWJfpEw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)**

**本地化**



**扩展卡尔曼滤波本地化**

![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1nVVHy6ZSVjaclyOygDya9OxAszoTQpdsQ9IH8HxHowNFJf635pOSsDA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

该算法利用扩展卡尔曼滤波器（Extended Kalman Filter, EKF）实现传感器混合本地化。

蓝线为真实路径，黑线为导航推测路径（dead reckoning trajectory），绿点为位置观测（如GPS），红线为EKF估算的路径。

红色椭圆为EKF估算的协方差。

> *相关阅读：*
>
> 概率机器人学
>
> http://www.probabilistic-robotics.org/

**无损卡尔曼滤波本地化**

![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1nAjLDBt1Aiba0APwgusOokiaEqsUawccH7cvONTdTwwzqiafsfGic2BNaiaA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

该算法利用无损卡尔曼滤波器（Unscented Kalman Filter, UKF）实现传感器混合本地化。

线和点的含义与EKF模拟的例子相同。

> *相关阅读：*
>
> 利用无差别训练过的无损卡尔曼滤波进行机器人移动本地化
>
> https://www.researchgate.net/publication/267963417_Discriminatively_Trained_Unscented_Kalman_Filter_for_Mobile_Robot_Localization

**粒子滤波本地化**

![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1nXFa0POHKECMYziba4xMVGj1QUickrT56aricr16rQjiaQnNWt6wn7x0RRA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

该算法利用粒子滤波器（Particle Filter, PF）实现传感器混合本地化。

蓝线为真实路径，黑线为导航推测路径（dead reckoning trajectory），绿点为位置观测（如GPS），红线为PF估算的路径。

该算法假设机器人能够测量与地标（RFID）之间的距离。

PF本地化会用到该测量结果。

> *相关阅读：*
>
> 概率机器人学
>
> http://www.probabilistic-robotics.org/

**直方图滤波本地化**

![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1nLZATD8HVboyElOEdUic9Rd67H6QpzX25pZpcIXEPjDKhNqaiaicZkUDKQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

该算法是利用直方图滤波器（Histogram filter）实现二维本地化的例子。

红十字是实际位置，黑点是RFID的位置。

蓝色格子是直方图滤波器的概率位置。

在该模拟中，x，y是未知数，yaw已知。

滤波器整合了速度输入和从RFID获得距离观测数据进行本地化。

不需要初始位置。

> *相关阅读：*
>
> 概率机器人学
>
> http://www.probabilistic-robotics.org/



**![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhSvZMAt2zKcxGQN3l1NV4LzUzfol6q1COlZYpeYXqe0aia45DXyhcTQW8voWvibFJvEjfmkhPvCrEg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)**

**映射**



**高斯网格映射**

本算法是二维高斯网格映射（Gaussian grid mapping）的例子。

![img](https://mmbiz.qpic.cn/mmbiz_gif/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1nclhN4pKvhHAlRkibBP0iaQt46ADmLVNJphaibeYCFXHnzqyqdtE2Cr0pw/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

**光线投射网格映射**

本算法是二维光线投射网格映射（Ray casting grid map）的例子。

![img](https://mmbiz.qpic.cn/mmbiz_gif/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1nOrSibHE91TdqoUib5Ft9ibicCFzoQR6Us2fv6hgtmyhgd4HwSQAicPuu7pQ/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

**k均值物体聚类**

本算法是使用k均值算法进行二维物体聚类的例子。

![img](https://mmbiz.qpic.cn/mmbiz_gif/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1n2nwzY9sA7DZyAqzFgQBPDszUgbGG5Z7MUNYGJledv68IJ0LzAQmMDA/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

**圆形拟合物体形状识****别**

本算法是使用圆形拟合进行物体形状识别的例子。

![img](https://mmbiz.qpic.cn/mmbiz_gif/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1nHXdZUicVzZvZyibPnjRthe5uoG3TCw0dficYG8Tlko8bLQZRjib9VatyOw/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

蓝圈是实际的物体形状。

红叉是通过距离传感器观测到的点。

红圈是使用圆形拟合估计的物体形状。



**![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhSvZMAt2zKcxGQN3l1NV4L8DRknQu3FB9eZicibvOSlVrgnllB3XcGnsrw4zCk19ic0QjZkTPoqFMWw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)**

**SLAM**



同时本地化和映射（Simultaneous Localization and Mapping，SLAM）的例子。

**迭代最近点匹配**

本算法是使用单值解构进行二维迭代最近点（Iterative Closest Point，ICP）匹配的例子。

它能计算从一些点到另一些点的旋转矩阵和平移矩阵。

![img](https://mmbiz.qpic.cn/mmbiz_gif/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1nvabj5DTib5O4KzlPk81ErAPC1T1mLv0vmMuFQqux9YlYickjPl3x2TmA/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

> *相关阅读：*
>
> 机器人运动介绍：迭代最近点算法
>
> https://cs.gmu.edu/~kosecka/cs685/cs685-icp.pdf

**EKF SLAM**

这是基于扩展卡尔曼滤波的SLAM示例。

蓝线是真实路径，黑线是导航推测路径，红线是EKF SLAM估计的路径。

绿叉是估计的地标。

![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1nMhsBg3tuX6qL9N3DcCB0Nng3OuFqjaR92UquMn3icuycNTOqunA2hYQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

> *相关阅读：*
>
> 概率机器人学
>
> http://www.probabilistic-robotics.org/

**FastSLAM 1.0**

这是用FastSLAM 1.0进行基于特征的SLAM的示例。

蓝线是实际路径，黑线是导航推测，红线是FastSLAM的推测路径。

红点是FastSLAM中的粒子。

黑点是地标，蓝叉是FastLSAM估算的地标位置。

![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1nhFiaMqBrqCicwkraM1UtOrQ8UYffPPaL7VXhaGlATC42BKM4aAKGu7Jg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

> *相关阅读：*
>
> 概率机器人学
>
> http://www.probabilistic-robotics.org/

**FastSLAM 2.0**

这是用FastSLAM 2.0进行基于特征的SLAM的示例。

动画的含义与FastSLAM 1.0的情况相同。

![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1nW3NxHT5OITxk0B18Q5knbiat9WVKXlgxs77HDZChWRPq0afB47bPrmA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

> *相关阅读：*
>
> 概率机器人学
>
> http://www.probabilistic-robotics.org/
>
> Tim Bailey的SLAM模拟
>
> http://www-personal.acfr.usyd.edu.au/tbailey/software/slam_simulations.htm

**基于图的SLAM**

这是基于图的SLAM的示例。

蓝线是实际路径。

黑线是导航推测路径。

红线是基于图的SLAM估算的路径。

黑星是地标，用于生成图的边。

![img](https://mmbiz.qpic.cn/mmbiz_gif/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1nEWBKwovauibScicW8Aeg6WFiaRMjgiauiacghZsuURhGtv1Yfuy4ZMU2LDw/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

> *相关阅读：*
>
> 基于图的SLAM入门
>
> http://www2.informatik.uni-freiburg.de/~stachnis/pdf/grisetti10titsmag.pdf



**![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhSvZMAt2zKcxGQN3l1NV4LFV97HltQYlNPKDzX97K1Ll0EicLrBIuWQtgLtQYdFKkkLcWKGUeMTfg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)**

**路径规划**



**动态窗口方式**

这是使用动态窗口方式（Dynamic Window Approach）进行二维导航的示例代码。

![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1n9ic6SCVXxUeH0dN5GFyptcdNLaPfFicxVd4AnibQOV9BiaywnMSUtNuthQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

> *相关阅读：*
>
> 用动态窗口方式避免碰撞
>
> https://www.ri.cmu.edu/pub_files/pub1/fox_dieter_1997_1/fox_dieter_1997_1.pdf

**基于网格的搜索**

**迪杰斯特拉算法**

这是利用迪杰斯特拉（Dijkstra）算法实现的基于二维网格的最短路径规划。

![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1nNBtLN54gS8uGBj7mLPJRiaVQQ2W4riaq7dZElCtx2mODYURuWoNT8YyA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

动画中青色点为搜索过的节点。

**A\*算法**

下面是使用A星算法进行基于二维网格的最短路径规划。

![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1nmysUxTRKUOWFkcn1RCy1XK3jcy6GbxyoesSdhGQbGIibiap0sF4IUXPQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

动画中青色点为搜索过的节点。

启发算法为二维欧几里得距离。

**势场算法**

下面是使用势场算法进行基于二维网格的路径规划。

![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1nNJ56lgk1Cnn0BiccLNmTvoZ2tE7DU2KLYgjJuiaUc89jtcXJjvaNQwrw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

动画中蓝色的热区图显示了每个格子的势能。

> *相关阅读：*
>
> 机器人运动规划：势能函数
>
> https://www.cs.cmu.edu/~motionplanning/lecture/Chap4-Potential-Field_howie.pdf

**模型预测路径生成**

下面是模型预测路径生成的路径优化示例。

算法用于状态晶格规划（state lattice planning）。

**路径优化示例**

![img](https://mmbiz.qpic.cn/mmbiz_gif/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1nQIM763BJfJTzXu3J2ZM4GkJiaLS1Tb542DDJF8SDibFbUlt2St1UkRAw/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

**查找表生成示例**

![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1njEcLw5DMUf5uXl6q77mNfEib0pZXJbRZcPuiayWXbHc1BPDg8pM0GGoQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

> *相关阅读：*
>
> 用于带轮子的机器人的最优不平整地形路径生成
>
> http://journals.sagepub.com/doi/pdf/10.1177/0278364906075328

**状态晶格规划**

这个脚本使用了状态晶格规划（state lattice planning）实现路径规划。

这段代码通过模型预测路径生成来解决边界问题。

> *相关阅读：*
>
> 用于带轮子的机器人的最优不平整地形路径生成
>
> http://journals.sagepub.com/doi/pdf/10.1177/0278364906075328
>
> 用于复杂环境下的高性能运动机器人导航的可行运动的状态空间采样
>
> http://www.frc.ri.cmu.edu/~alonzo/pubs/papers/JFR_08_SS_Sampling.pdf

**均匀极性采样（Uniform polar sampling）**

![img](https://mmbiz.qpic.cn/mmbiz_gif/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1ns5xjza9pTy6RW9YBHAUM45VSjMVaBnNVn5V7ibrAEzT1Z6wrwyraHDQ/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

**偏差极性采样（Biased polar sampling）**

![img](https://mmbiz.qpic.cn/mmbiz_gif/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1nR27Xicl8LhSqVdzoibjvw4tscpaEEWmiaItj3vYOfIOsycQHgylpY3bvw/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

**路线采样（Lane sampling）**

![img](https://mmbiz.qpic.cn/mmbiz_gif/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1nHN2gmN58ctzmX00j9B5jYrLSpt2iaJTQIEW3gOmvLEDx0Q1ERVlMk4Q/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

**随机路径图（PRM）规划**

![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1naUbonBBC9dzJQo9qZVV9R0q59nKUOrk6Ik9zBv2AXLiavCdEoQNaXrQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

这个随机路径图（Probabilistic Road-Map，PRM）规划算法在图搜索上采用了迪杰斯特拉方法。

动画中的蓝点为采样点。

青色叉为迪杰斯特拉方法搜索过的点。

红线为PRM的最终路径。

> *相关阅读：*
>
> 随机路径图
>
> https://en.wikipedia.org/wiki/Probabilistic_roadmap

**Voronoi路径图规划**

![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1nRZXicz4xXf9ia7xFs5gbO0hH1xQ0esRfZTicfYg3YMgcBrZ5zZ9SPoPeQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

这个Voronoi路径图（Probabilistic Road-Map，PRM）规划算法在图搜索上采用了迪杰斯特拉方法。

动画中的蓝点为Voronoi点。

青色叉为迪杰斯特拉方法搜索过的点。

红线为Voronoi路径图的最终路径。

> *相关阅读：*
>
> 机器人运动规划
>
> https://www.cs.cmu.edu/~motionplanning/lecture/Chap5-RoadMap-Methods_howie.pdf

**快速搜索随机树（RRT）**

**基本RRT**

![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1nW2BdeGricKRJH0x7F8OC11ib8GgoFpXh0lfI8uzq7IibQIia2QCDrzJu5A/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

这是个使用快速搜索随机树（Rapidly-Exploring Random Trees，RRT）的简单路径规划代码。

黑色圆为障碍物，绿线为搜索树，红叉为开始位置和目标位置。

**RRT\***

![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1nDtogLtjzXgichib57PmMbQY2aib529MlBWU0icqaPPlSmyLt1iawzeDvUFg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

这是使用RRT*的路径规划代码。

黑色圆为障碍物，绿线为搜索树，红叉为开始位置和目标位置。

> *相关阅读：*
>
> 最优运动规划的基于增量采样的算法
>
> https://arxiv.org/abs/1005.0416
>
> 最优运动规划的基于采样的算法
>
> http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.419.5503&rep=rep1&type=pdf

**基于Dubins路径的RRT**

![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1nrIuQVmJ76Z8icN7zuM1G3zkiciaNMXCOR27N7RXibBngwsaickMmqfASw9g/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

为汽车形机器人提供的使用RRT和dubins路径规划的路径规划算法。

**基于Dubins路径的RRT\***

![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1nLyL9rODT5qSMmxO6AX2G93y9rsnRtHe6FNQ0gU3xbKwicDQCBaFVOeA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

为汽车形机器人提供的使用RRT*和dubins路径规划的路径规划算法。

**基于reeds-shepp路径的RRT\***

![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1niaRjFlKVDicPrtqXmrtdLiajNXCcqwQfxI5icicE6ZiaLSeTQ4Md40SklqeQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

为汽车形机器人提供的使用RRT*和reeds shepp路径规划的路径规划算法。

**Informed RRT\***

![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1nYsN8CYbfXiasIkB24O2h9iaH8NlGfuKAwRfdc4kJG6THds8w0Q8wxqAQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

这是使用Informed RRT*的路径规划代码。

青色椭圆为Informed RRT*的启发采样域。

> *相关阅读：*
>
> Informed RRT*：通过对可接受的椭球启发的直接采样实现最优的基于采样的路径规划
>
> https://arxiv.org/pdf/1404.2334.pdf

**批量Informed RRT\***

![img](https://mmbiz.qpic.cn/mmbiz_gif/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1nrFa9hcQ0QgrzRxqr9rIlUfXOPia2YQ25Lh3N2YEsKoQXRaQLGr6iaVAA/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

这是使用批量Informed RRT*的路径规划代码。

> *相关阅读：*
>
> 批量Informed树（BIT*）：通过对隐含随机几何图形进行启发式搜索实现基于采样的最优规划
>
> https://arxiv.org/abs/1405.5848

**闭合回路RRT\***

使用闭合回路RRT*（Closed loop RRT*）实现的基于车辆模型的路径规划。

![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1n7kKVQgJ6l5x7vwNDMNeIcSkFaAEELDNR75mobxA1T3JT8dPYbAkYEA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

这段代码里，转向控制用的是纯追迹算法（pure-pursuit algorithm）。

速度控制采用了PID。

> *相关阅读：*
>
> 使用闭合回路预测在复杂环境内实现运动规划
>
> http://acl.mit.edu/papers/KuwataGNC08.pdf）
>
> 应用于自动城市驾驶的实时运动规划
>
> http://acl.mit.edu/papers/KuwataTCST09.pdf
>
> [1601.06326]采用闭合回路预测实现最优运动规划的基于采样的算法
>
> https://arxiv.org/abs/1601.06326

**LQR-RRT\***

这是个使用LQR-RRT*的路径规划模拟。

LQR局部规划采用了双重积分运动模型。

![img](https://mmbiz.qpic.cn/mmbiz_gif/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1nauekMkiaBicejQYb1ppLKohzEzRC1RMhWapcUzamJc1h9d7W7uYxpHhQ/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

> *相关阅读：*
>
> LQR-RRT*：使用自动推导扩展启发实现最优基于采样的运动规划
>
> http://lis.csail.mit.edu/pubs/perez-icra12.pdf
>
> MahanFathi/LQR-RRTstar：LQR-RRT*方法用于单摆相位中的随机运动规划
>
> https://github.com/MahanFathi/LQR-RRTstar

**三次样条规划**

这是段三次路径规划的示例代码。

这段代码根据x-y的路点，利用三次样条生成一段曲率连续的路径。

每个点的指向角度也可以用解析的方式计算。

![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1ncj2hOZB4z0ziaribM8JGZqU2mTMe4fp1pnHVs3RGBSJb9GzTFTmGparw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1nXABpSwmtRM1qKn9TUj0TFSh6U2bwzaW6V0hSyWGsYZGCcvLFaaIY2Q/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1nuoyXB8zJrniaQFDm1FlV93aibibGjRM4cXVpcpP8VfdNVU88ibLgPicOf2g/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**B样条规划**

![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1nf0Ipa22xJj6hzezpwdbCq0dKrvYX1jJicsUagLxmT8tiabP72YPo5nZA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

这是段使用B样条曲线进行规划的例子。

输入路点，它会利用B样条生成光滑的路径。

第一个和最后一个路点位于最后的路径上。

> *相关阅读：*
>
> B样条
>
> https://en.wikipedia.org/wiki/B-spline

**Et****a^****3样条路径规划**

![img](https://mmbiz.qpic.cn/mmbiz_gif/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1nLvWHmiaAC9ux0FmzXR6jD5FiczMMwBOl2ibAv0KCacj37BBlC99XEoDJg/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

这是使用Eta ^ 3样条曲线的路径规划。

> *相关阅读：*
>
> \eta^3-Splines for the Smooth Path Generation of Wheeled Mobile Robots
>
> https://ieeexplore.ieee.org/document/4339545/

**贝济埃路径规划**

贝济埃路径规划的示例代码。

根据四个控制点生成贝济埃路径。

![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1nDrZKWpVyskC675XrMC2eIUO8yNclxj7KpncpHDUblInt4sfT7t5MuA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

改变起点和终点的偏移距离，可以生成不同的贝济埃路径：

![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1nGFXYbEOGEiaxTW7yYlr2JumO5GKVqVrMqyTNHWjWgwzoULEDZJxBrew/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

> *相关阅读：*
>
> 根据贝济埃曲线为自动驾驶汽车生成曲率连续的路径
>
> http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.294.6438&rep=rep1&type=pdf

**五次多项式规划**

利用五次多项式进行路径规划。

![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1npWibZG9SMic3rayhwHmicJD53JKjLdqEtxyL8yj5iaiaWJSFgxDDdx7AdNg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

它能根据五次多项式计算二维路径、速度和加速度。

> *相关阅读：*
>
> 用于Agv In定位的局部路径规划和运动控制
>
> http://ieeexplore.ieee.org/document/637936/

**Dubins路径规划**

Dubins路径规划的示例代码。

![img](https://mmbiz.qpic.cn/mmbiz_gif/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1nicMFYdtSV3UTtXZg9f5oEtmyZ3aiaqdL1NGtEcnxCPeJncv7KMRIQcuw/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

> *相关阅读：*
>
> Dubins路径
>
> https://en.wikipedia.org/wiki/Dubins_path

**Reeds Shepp路径规划**

Reeds Shepp路径规划的示例代码。

![img](https://mmbiz.qpic.cn/mmbiz_gif/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1nZqfA76ibUODaoebHqK4xAic6FBYlNCZHu4XLcTwkmewUlT7NFhd1caaQ/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

> *相关阅读：*
>
> 15.3.2 Reeds-Shepp曲线
>
> http://planning.cs.uiuc.edu/node822.html
>
> 用于能前进和后退的汽车的最优路径
>
> https://pdfs.semanticscholar.org/932e/c495b1d0018fd59dee12a0bf74434fac7af4.pdf
>
> ghliu/pyReedsShepp：实现Reeds Shepp曲线
>
> https://github.com/ghliu/pyReedsShepp

**基于LQR的路径规划**

为双重积分模型使用基于LQR的路径规划的示例代码。

![img](https://mmbiz.qpic.cn/mmbiz_gif/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1n4jp8YRJtSuvYpskC3icr9vHmvOlOs7Y7S1lmDicxxiczCXkO0rDDmicpLg/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

**Frenet Frame中的最优路径**

![img](https://mmbiz.qpic.cn/mmbiz_gif/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1nET5ibIibiaHdGashDHZBs4npXJw388065TY9oFAoDRBkftByiaGOHZzTMA/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

这段代码在Frenet Frame中生成最优路径。

青色线为目标路径，黑色叉为障碍物。

红色线为预测的路径。

> *相关阅读：*
>
> Frenet Frame中的动态接到场景中的最优路径生成
>
> https://www.researchgate.net/profile/Moritz_Werling/publication/224156269_Optimal_Trajectory_Generation_for_Dynamic_Street_Scenarios_in_a_Frenet_Frame/links/54f749df0cf210398e9277af.pdf
>
> Frenet Frame中的动态接到场景中的最优路径生成
>
> https://www.youtube.com/watch?v=Cj6tAQe7UCY



**![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhSvZMAt2zKcxGQN3l1NV4Lf6AGBpotDb1DGltQgly1vKzgCCOtT3OQn43luu8r1JxUV1PmiaSAViaw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)**

**路径跟踪**



**姿势控制跟踪**

这是姿势控制跟踪的模拟。

![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1nlbcN9CcAelcJGRnXuukHdzj0iccmJtGC8dcnY6HbINZY9atRopuMNaA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

> *相关阅读：*
>
> Robotics, Vision and Control - Fundamental Algorithms In MATLAB® Second, Completely Revised, Extended And Updated Edition | Peter Corke | Springer
>
> https://www.springer.com/us/book/9783319544120

**纯追迹跟踪**

使用纯追迹（pure pursuit）转向控制和PID速度控制的路径跟踪模拟。

![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1n3pTCicR9vsaAiarFVEV8ehB5RasiajvSnTqG96oSAy8hk8b6licpwjzsgw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

红线为目标路线，绿叉为纯追迹控制的目标点，蓝线为跟踪路线。

> *相关阅读：*
>
> 城市中的自动驾驶汽车的运动规划和控制技术的调查
>
> https://arxiv.org/abs/1604.07446

**史坦利控制**

使用史坦利（Stanley）转向控制和PID速度控制的路径跟踪模拟。

![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1nSksgwhNvgQCvcibb2nyRudxAO8CjoZG5Z78gG2CxwhicgslL1ibC0UQgQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

> *相关阅读：*
>
> 史坦利：赢得DARPA大奖赛的机器人
>
> http://robots.stanford.edu/papers/thrun.stanley05.pdf
>
> 用于自动驾驶机动车路径跟踪的自动转向方法
>
> https://www.ri.cmu.edu/pub_files/2009/2/Automatic_Steering_Methods_for_Autonomous_Automobile_Path_Tracking.pdf

**后轮反馈控制**

利用后轮反馈转向控制和PID速度控制的路径跟踪模拟。

![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1nsqyI5YM4VBN2KIjAneGYwnh8vGQLDooncyHnaI28wTup0jWAsoOH0g/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

> *相关阅读：*
>
> 城市中的自动驾驶汽车的运动规划和控制技术的调查
>
> https://arxiv.org/abs/1604.07446

**线性二次regulator（LQR）转向控制**

使用LQR转向控制和PID速度控制的路径跟踪模拟。

![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1nupWg90e9GOVHCjWzgT2JvO3ZuwuLNxb1BYpRAeKUkrorpnL3IKnFSQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

> *相关阅读：*
>
> ApolloAuto/apollo：开源自动驾驶平台
>
> https://github.com/ApolloAuto/apollo

**线性二次regulator（LQR）转向和速度控制**

使用LQR转向和速度控制的路径跟踪模拟。

![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1nfTXa0db9Ijc604wGH2dB8qIdN1EbFLianlRFZlAfr3zJ9EKgXgoorYA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

> 相关阅读：
>
> 完全自动驾驶：系统和算法 - IEEE会议出版物
>
> http://ieeexplore.ieee.org/document/5940562/

**模型预测速度和转向控制**

使用迭代线性模型预测转向和速度控制的路径跟踪模拟。

![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhW10ufN3icVxhnmic7JHOY1n6d3C2iaHreonGY1AQicw9syWTIas6dmQgswtHKBQNNbCiaavUmVr3zLfg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

这段代码使用了cxvxpy作为最优建模工具。

> *相关阅读：*
>
> 车辆动态和控制 | Rajesh Rajamani | Springer
>
> http://www.springer.com/us/book/9781461414322
>
> MPC课程资料 - MPC Lab @ UC-Berkeley
>
> http://www.mpc.berkeley.edu/mpc-course-material



**![img](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAuhSvZMAt2zKcxGQN3l1NV4L7H0ibIdQobyzuicxzGicfibXUugu11UNG7jA3g0M1ibp02QOr8UTuTDiaDwA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)**

**项目支持**



可以通过Patreon（https://www.patreon.com/myenigma）对该项目进行经济支持。

如果你在Patreon上支持该项目，则可以得到关于本项目代码的邮件技术支持。




# 相关

- [这可能是史上最全的 Python 算法集！| 技术头条](https://mp.weixin.qq.com/s?__biz=MjM5MjAwODM4MA==&mid=2650703075&idx=2&sn=a056320b964226cfe143f9ce2ee52c3d&chksm=bea6f53089d17c26fa63bf1f0c7bdbede7ff13bc7df09fa969bbec56a693d61d36677ffd00e8&mpshare=1&scene=1&srcid=0819esHFbMNIM1GrdGVMo9FO#rd)
- https://atsushisakai.github.io/PythonRobotics/#what-is-this 作者 Atsushi Sakai (@Atsushi_twi)，Daniel Ingram，Joe Dinius，Karan Chawla，Antonin RAFFIN，Alexis Paques。
