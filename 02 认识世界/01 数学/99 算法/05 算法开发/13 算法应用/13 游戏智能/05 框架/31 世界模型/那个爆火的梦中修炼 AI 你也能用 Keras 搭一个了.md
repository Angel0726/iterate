---
title: 那个爆火的梦中修炼 AI 你也能用 Keras 搭一个了
toc: true
date: 2019-11-17
---
# 那个爆火的“梦中修炼”AI，你也能用Keras搭一个了



上月，量子位报道了Google Brain的David Ha和“LSTM之父”Jürgen Schmidhuber的论文World Models。论文中[习得周星驰睡梦罗汉拳的AI可在梦里“修炼”](http://mp.weixin.qq.com/s?__biz=MzIzNjc1NzUzMw==&mid=2247496223&idx=1&sn=4649de56313065892477b50126f71eea&chksm=e8d0456ddfa7cc7b3184a90a64e192da45ebe509ef0a886f0b75cf98b2c93df54c7713759c4e&scene=21#wechat_redirect)，好生厉害~

这篇文章就教你如何用Python和Keras搭建一个属于自己的“梦境修炼AI”。

﻿![img](https://mmbiz.qpic.cn/mmbiz_gif/YicUhk5aAGtDv0mxibJRmC8loUVADAZheGgYFK5mzXSW8yuUV8utIEYTICITe3SxgKXILnAaXL3JDgnHX9vMl4Yg/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)﻿

开始教程前，先放上原研究的论文地址：

https://arxiv.org/abs/1803.10122

# 第一步：理解环境

我们要搭建一个能在2D车道环境中开车的强化学习模型，那么这个环境从哪来呢？我推荐OpenAI GYM，可进入下方地址获取。

环境获取地址：

https://gym.openai.com/

在这个任务的每个时间步中，我们需要用64×64像素的车辆和环境的彩图喂算法，并且需要返回下一组操作，尤其当方向(-1到1)，加速度(0到1)和制动(0到1)变化时。

这个动作将随后被传递到环境中，之后再返回下一组操作，如此循环。

得分会随着智能体跑过轨道而积累，随着时间步消耗，每个时间步得-0.1分。例如，如果一个智能体用732帧跑完了轨道，那么最后的得分就是1000-0.1×732=926.8分。

下面这张图展示的是一个智能体在前200个时间步中执行的[0,1,0]的动作，但之后画风一转突然变成了随机乱跑……不过这显然不是个好策略。

﻿![img](https://mmbiz.qpic.cn/mmbiz_gif/YicUhk5aAGtDv0mxibJRmC8loUVADAZheGrSE3LNF5sdJA9EgdKiaO4h2ysX8AcZQT3K2QcmGQaouP3tKHvctXZSw/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)﻿

OK第一步已经完成，我们接下来的任务是，训练智能体理解它周围环境的信息，确定下一步的最佳行动。

# 第二步：解决方案

开始第二步前先给大家推荐我们今天这篇论文的在线交互版~

交互版地址：

https://worldmodels.github.io/

接下来我将顺着上面的这个方案，重点其中的几部分是怎样组合起来的。这样吧，我们将虚拟环境与真实开车情况做对比，直观理解一下这个解决方案。

这个方案由三部分组成，每一部分都需要单独训练：

## 一种变分自编码器（VAE）

想象一下，当你在开车的同时考虑别的事情时，你不会分析视野中的每个“像素”。你的大脑会自动将视觉信息压缩成更少的“本征”实体，如道路的弯曲程度、即将到来的转弯和相对于道路的位置，指挥下一步动作。

**这就是VAE要做的——**将64×64×3（RGB）的输入图像遵循高斯分布压缩成一个32维的本征矢量latent vector（z）。

这一步非常重要，现在对智能体周围环境的表示变得更小了，因此学习过程将变得更加高效。

## 带混合密度网络输出层的循环神经网络(MDN-RNN)

如果你的决策中没有MDN-RNN组件，那么开车时可能是这样的情景。

![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtDv0mxibJRmC8loUVADAZheGTlaeXhnU6QibCBlHgJJSkO4ShZiaia8PgACX1b9kjYC5aica3NXGnHq6tQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

当你在开车时，每个场景都不会完全出乎你的意料。在我们这个程序里，**这种前瞻性的思考由RNN完成**，在我们这个例子中，LSTM中总共有256个隐藏单元，这个隐藏状态的向量由h表示。

和VAE相似，RNN也试图对汽车当前在环境中的状态建立一个“本征”理解，但这一次带着一个目标：基于之前的“z”和之前的动作，预测下一个“z”可能是什么样子。

MDN输出层仅允许出现下一个“z”从任何一个高斯分布中提取的情况。

﻿![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtDv0mxibJRmC8loUVADAZheGvRomhibbK5icZQz1EUfUr4w9LdJ2x3O86GZnVjgghGEvc9ZMYvyictpibQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)﻿

在这篇World Models的研究中，下一个观察到的潜在状态可能从任何一种高斯分布中得到。

## 控制器

到目前为止，我们还没讲到关于选择操作的事~这部分主要是控制器完成的。

控制器是一个紧密连接的神经网络，输入z的联结(长度为32的VAE当前潜在状态)和h(长度为256的RNN隐藏态)。

这3个输出神经元对应于三个动作，并按比例缩小到合适的范围。

## 模拟“三方会谈”

如果说你还是不太明白这三部分职责之间的联系，那我模拟一下行车过程中它们三方的对话，帮你形象理解一下。

﻿![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtDv0mxibJRmC8loUVADAZheGZiaRWTgdBsZ1cAvylr8KYSkRiaMDlbt3ZK4r634nd3sCbdUlgvI1jufw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)﻿

> VAE：前面看起来是条直路，向左有个轻微的拐弯，汽车正朝着道路的方向行驶（z）。
>
> RNN：根据你的描述（z），以及控制器选择上个时间步猛加速的行为，我将更新我的隐藏状态(h)，这样下个观察到的视野就会被预测为一条直线，但在视野中稍微偏左一点。
>
> 控制器：基于VAE (z)的描述和RNN (h)的当前隐藏状态，我的神经网络输出下一个动作为[0.34,0.8,0]。

这个动作将被传递给环境，然后返回更新后的视野，如此反复循环。

看明白了吧？就是这三部分控制了车辆的移动。那么接下来，是时候研究如何设置环境，帮训练自己的智能体。

﻿![img](https://mmbiz.qpic.cn/mmbiz_gif/YicUhk5aAGtDv0mxibJRmC8loUVADAZheGvxBOKHjQjWKd4xSuxrM1dmjciaO1LVTeAG8uKYKudgmKqwC7X0MZBeQ/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)﻿

# 第三步：设置环境

如果你的笔记本性能比较高，可以在本地运行解决方案。对于电脑条件一般的程序猿们，我还是建议你用Google Cloud Compute，快还方便。

https://cloud.google.com/compute/

下面这些步骤我已经在Linux (Ubuntu 16.04)上测试过了——如果你要在Mac或Windows系统里装，更改安装包的相关指令即可。

跟我一步一步来——

![img](https://mmbiz.qpic.cn/mmbiz_gif/YicUhk5aAGtDv0mxibJRmC8loUVADAZheGjZlCMXib1PSSa4cD6jUWicILybUKa9aeb7f8DSaZ9cYmwgfmhAIQxpyw/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)



\1. 克隆存储库

储存库地址：

https://github.com/AppliedDataSciencePartners/WorldModels

在命令行中，找到想要克隆存储库的地方，输入以下内容:

﻿![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtDv0mxibJRmC8loUVADAZheGpCrSupuYAqcdNJVEYF196jtwLfXAac0fHugeQOxrjwbA2H577kK1sA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)﻿

这个库是由World Model的一作David Ha开发的实用estool库改编的，他用Keras和TensorFlow后端实现了神经网络的训练。

2.设置虚拟环境

我们需要创建一个Python 3虚拟环境(我用的是virutalenv和virtualenvwrapper)

﻿![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtDv0mxibJRmC8loUVADAZheGd3UQTiad4lxPqdCK7NiaaAuJB8QbNVciarib8XFSFqibV4J2icVutbzrgdBA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)﻿

3.安装包

﻿![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtDv0mxibJRmC8loUVADAZheGLuuFCF0sbLTs1xMtD3G8XjIS0zrqGd4GOwDCWTPKRKqSia4YSb22ddQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)﻿

4.安装requirements.txt

﻿![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtDv0mxibJRmC8loUVADAZheGq7dVOL5qtuecQBVhQ5sSia0PQKqP6C5gYOSkRAJz7MZwiajXYf3EWm1w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)﻿

# 第四步：生成随机rollout

对于赛车环境来说，VAE和RNN可以用于随机的rollout数据上——也就是说，在每一个时间步中随机采取行动产生的观测数据。实际上，我们使用的是伪随机动作，最开始会强迫汽车加速，让它脱离起跑线。

由于VAE和RNN独立于决策控制器，所以需要保证我们提供各种各样的观察结果，和各种各样的动作，将它们存为训练数据。

要生成随机的rollout，可以从命令行运行以下指令：

﻿![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtDv0mxibJRmC8loUVADAZheGhFx6jRibPHTB8A2qzsXM4xDJ4NTm09XTgZia8fHsShQJXiamI4ejax59A/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)﻿

或者在一台没有显示器的服务器上运行以下指令：

﻿![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtDv0mxibJRmC8loUVADAZheGmDXoA3ictDs0WfEll7lYqQ9munCUGlxBgPyGiaIx3yWOlqEr8icFib070g/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)﻿

这将产生2000次的输出，每个rollout最长为300个时间步。

两组文件在`./data`中保存，一是`obs_data_*.npy`(将64×64×3图像存储为numpy数组)，二是`action_data_*.npy`(存储三维动作)

# 第五步：训练VAE

上面我们介绍了VAE是操纵小车在环境中移动的一把手，现在我们就讲讲如何训练它。这一步可能比你想象的要简单的多，因为所需文件只要`obs_data_*.npy`文档就好了。不过温馨提示一下，确保你已经完成了第四步，因为要之后将这些文档放于`./data`文件夹中。

从命令行运行：
﻿![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtDv0mxibJRmC8loUVADAZheGq5BNtQ8fjNSicp9KzG3u8ibrc4PKiahx8t0I796wnWwCf5QiaRwky8wOWA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)﻿
我们将从0到9的每批数据上训练一个新VAE。

模型的权重将被存储在`./vae/weights.h5`中，`--new_model`是在提示脚本从头开始训练模型。

如果文件夹中有weights.h5，并且没有指定`--new_model`标记时，脚本将从该文件加载权重，并继续训练现有模型。这样，你就可以批量迭代VAE。

度低了，VAE架构规范在`./vae/arch.py`文件夹中。

第六步：生成RNN数据

现在我们有了训练好的VAE，就可以用它生成为RNN训练集。

RNN需要将VAE中编码的图像数据(z)和动作(a)作为输入，并将VAE中预先编码的图像数据作为输出。你可以通过运行下面的指令生成这些数据：

﻿![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtDv0mxibJRmC8loUVADAZheGSFoqu9EKEHP67o70GcZCiaEcaQL3kaRuiabE8R3UiaCMlmJojtc2jeb0w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)﻿

这将改变`obs_data_* .npy`和`action_data_*.npy`文件中从batch 0到batch 9的数据，并且将它们转化成RNN需要的正确格式完成训练。

两组文件将保存在`./data`中，`rnn_input_* .npy`存储[z a]连接的向量，`rnn_output_*.npy`存储的是前一个时间步的z向量。

第七步：训练RNN

上一步生成了RNN的数据后，训练它只需`rnn_input_*.npy`和`rnn_output_*.npy`文件就可以了。在这里再次提醒：第六步一定要完成，这些文件都要在`./data`文件夹里才可以。

在命令行运行下列代码：

﻿![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtDv0mxibJRmC8loUVADAZheGGAENk7zOmtpoNRmicv60tQ9icvB63cLnmUI5jpJdEVcAvFqn0Yy4MXyw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)﻿

这将在0到9的每批数据上训练一个新的VAE。模型权重将被保存到`./rnn/weights.h5`中。`new_model`标志提示脚本从头开始训练模型。

和VAE一样，如果文件夹中存在`weights.h5`并且没有指定`--new_model`标记，那么脚本将从该文件加载权重，并继续训练现有模型。通过这种方式，您可以迭代地批量训练RNN。

找不到RNN架构说明的可以去翻翻`./rnn/arch.py`文件，可能会让你小小开心一下。

﻿![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtDv0mxibJRmC8loUVADAZheGzDTgnYeRjlk91bn16moZCR8YiadvXZYLsoMAJIcQ82WvNq61m6uichcQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)﻿

# 第八步：训练控制器

这是一个愉快的章节。

上面，我们已经用深度学习搭建了一个VAE，它可以把高维图像压缩成一个低维潜在空间；还搭好了一个RNN，可以预测潜在空间随着时间推移会发生怎样的变化。能走到这一步，是因为我们给VAE和RNN各自装备了一个由随机rollout data组成的训练数据集。

现在，我们要使用一种强化学习方法，依靠名为CMA-ES的进化算法来训练控制器。

﻿![img](https://mmbiz.qpic.cn/mmbiz_gif/YicUhk5aAGtDv0mxibJRmC8loUVADAZheGwt4tVXmY6rgQz4zPGiaq0xj1KHwDuSRBBl7oIXJ7hTSmISicQ9KdARUA/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)﻿

输入向量有288维，输出向量是3维。于是，我们一共有288 x 3 + 1 (bias) = 867个参数需要训练。

首先，CMA-ES要为这867个参数，创建多个随机初始化副本，形成种群 (population) 。而后，这个算法会在环境中，测试种群中的每一个成员，记录平均分。像达尔文的自然选择一样，分数比较高的那些权重就会获得“繁衍”后代的资格。

敲下这个代码，给每个参数选择一个合适的值，就可以开始训练了：

﻿![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtDv0mxibJRmC8loUVADAZheGGcJJ73eAf0DK0zic7DzjBuMFIhgBqFqybf9gnjwkzJXw87CwEtjotDw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)﻿

如果没有显示器的话，就用这个代码：

﻿![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtDv0mxibJRmC8loUVADAZheGlwcXBnwhicV3TJfdic8JaUrzGYxNXy8rqribTu3ML4yEbtyU1txyv3NqA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)﻿

其中，

`--num_worker 16`：这个数字设置不要超过核数。

`--num_work_trial 2`：这里是每个worker要测试的，种群中的成员数量。

`--num_episode 4`：这里是种群里每个成员需要接受打分的集数。

`--max_length 1000`：这里是一集里最大的时间步数。

`--eval_steps 25`：这里是在长达100集的最佳权重评估过程中，经历了多少代进化。

`--init_opt ./controller/car_racing.cma.4.32.es.pk`在默认情况下，控制器每一次都会从头开始运行，把当前状态保存到controller目录下的pickle文件中。然后，下次我们就可以从存档的地方继续训练。

﻿![img](https://mmbiz.qpic.cn/mmbiz_jpg/YicUhk5aAGtDv0mxibJRmC8loUVADAZheG0CvEFZQ1ib9KY4sDo9GYrWdaDPD2Z9JlXnrazibY1lUnAL0BVWy9NUfQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)﻿

在每一代繁衍完成之后，算法的当前状态和最佳权重集合都会输出到`./controller`文件夹。

# 第九步：可视化

﻿![img](https://mmbiz.qpic.cn/mmbiz_gif/YicUhk5aAGtDv0mxibJRmC8loUVADAZheGqT9zroOhqZu8Xl3OLvibThibgWsJcjBpeG2w1uSkwClyDjOfv1s9g8kw/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)﻿

到这里，我们的智能体走过了200代，练到了883.13的平均分。它轮回的天地是：Google Cloud，Ubuntu 16.04 vCPU, 67.5 RAM，步骤和参数都和这篇文章里写的一样。

论文的作者大大们训练出906的平均分，那是2000代修仙的结果，也是目前这个环境下的最高分了。他们需要的配置也高一些，训练数据有10,000集，种群大小是64，计算机是64核等等。

﻿![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtDv0mxibJRmC8loUVADAZheGhHzZuoN5yKwBL7NXo36sFGH3MHIqRfpnPf4ICTsnVYXH5dDKBgQ2Zg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)﻿

将控制器的当前状态可视化，要敲上面这个代码。其中：

`--filename`：这是你想要赋予控制器的那些优秀权重的json路径。

`--render_mode`：在屏幕上渲染环境。

`--record_video`：把mp4输出到./video文件夹：

`--final_mode`：开始100集的控制器测试，输出平均分。下面是个可爱的小Demo。

﻿![img](https://mmbiz.qpic.cn/mmbiz_gif/YicUhk5aAGtDv0mxibJRmC8loUVADAZheGtEuODKibubfDZFVIECLkxRR7d8LicYGsa6HKUoqlD5YSDZ9MDIDydodA/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)﻿

# 第十步：太虚幻境

前面已经有很多颗赛艇了，不过论文的下一小节着实让我吃了一鲸。我感觉，这个方法还是很有现实意义的。

论文中介绍了在名为DoomTakeCover的另一个环境里，获得的美妙训练结果。这里的目标是让智能体学会躲避火球，活得越长越好。

﻿![img](https://mmbiz.qpic.cn/mmbiz_gif/YicUhk5aAGtDv0mxibJRmC8loUVADAZheGJHI8Axx7wibpZTncibQoSF88zOJv1EwRbTdVSPFJ2Otj75yuLxnwtKeA/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)﻿

这一章里，我们可以看到，智能体是如何在VAE和RNN联合打造的幻境里 (而非所处的环境里) ，一步步解锁游戏技能的。

唯一不同的是，这里的RNN还要学会预测，自己在下一个时间步里扑街的概率。如此一来，VAE与RNN的组合可以生成一个自己需要的环境，再用它来训练控制器。这便是浩瀚无边的“World Model”了。

# 亦真亦梦的总结

﻿![img](https://mmbiz.qpic.cn/mmbiz_gif/YicUhk5aAGtDv0mxibJRmC8loUVADAZheG2PMjYeo1ZVTicPd92Akh3FSMsSntskBepCpnA10Vjibqg3zbrRJy1gsw/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)﻿

我们可以把幻境学习的过程总结一下——

智能体的原始训练数据只是和真实环境的随机互动而已。有了这些，它就能对世界运行规律产生一个潜在的理解——自然分组、物理原理，以及自己的行为会对世界的状态产生怎样的影响。

然后，依靠这份理解，智能体便会为某个特定的任务，建立一套最佳的执行策略。这里，它甚至不需要接受真实环境的考验，只要在自己幻想出来的环境模型里玩耍，就当测试了。

﻿![img](https://mmbiz.qpic.cn/mmbiz_gif/YicUhk5aAGtDv0mxibJRmC8loUVADAZheGWYtR38pZMC9cWyRrWPibSjbVPDXndL4MJ6E0Kb7ficInvAbT8kZBeamA/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)﻿**△** 那里绿草如茵

就像小朋友初学走路，那是多么生意盎然的场景啊。




# 相关

- [那个爆火的“梦中修炼”AI，你也能用Keras搭一个了](https://mp.weixin.qq.com/s?__biz=MzIzNjc1NzUzMw==&mid=2247497790&idx=3&sn=9adf1062ca9623b3cfa7ce8d8bae176a&chksm=e8d04f4cdfa7c65abb9decb688bd28a44bde5b50cc3db01b06a7500accdd03ec19a5f1d29f3c&mpshare=1&scene=1&srcid=05017P5FxNoGmmRUlZyJtF9T#rd)
