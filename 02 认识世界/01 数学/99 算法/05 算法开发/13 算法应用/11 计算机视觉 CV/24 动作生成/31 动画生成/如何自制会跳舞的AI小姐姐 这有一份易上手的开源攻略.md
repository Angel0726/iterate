---
title: 如何自制会跳舞的AI小姐姐 这有一份易上手的开源攻略
toc: true
date: 2019-11-17
---
身材苗条，动作有力，姿势优美，视频片段里的小姐姐跳得行云流水，颇有C位出道的气势。

﻿![img](https://mmbiz.qpic.cn/mmbiz_gif/YicUhk5aAGtBlUzTtzY9SuukOqnCSpt8GFCdmkx8RqlfxsJrp81sIn1GVCjribO13J4RuM95AfRLJ8lwQiaHJXX7w/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)﻿

只不过，正在跳舞的小姐姐并不是真人，而是一个刚刚诞生不久的AI。

这几天，网友Jaison Saji开源了个叫DanceNet的神经网络，这是一个用变分自编码器、LSTM和混合密度网络构建的舞蹈生成器，合成不同姿态的逼真舞蹈动作不在话下。

开头提到的那个片段，便是DanceNet在短时间内用Keras训练合成的。

﻿![img](https://mmbiz.qpic.cn/mmbiz_gif/YicUhk5aAGtBlUzTtzY9SuukOqnCSpt8GPQlFNTdMfcCLULKfJtIqLOMbquYmyq0iaNXxQmxEFian0ktgia7t9glfA/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)﻿

短短几天，这个开源的小项目就在推特、Reddit等技术论坛火了起来。

# 开源详情

Jaison想做AI跳舞生成器是受了油管上的视频Does my AI have better dance moves than me的启发。

这个视频中，科技博主carykh提出了一种想法，即给模型喂食一段人类跳舞的视频，在经过一段时间的训练后，AI学会自动生成舞蹈。视频很火，但问题是作者并没有给出详细的代码。



Jaison觉得这事很有意思，几天之内也做了这个AI出来，并将代码挂在的Github上。

代码地址：

https://github.com/jsn5/dancenet

跳舞AI主要用到了变分自编码器和LSTM+混合密度网络完成，用油管上一段1小时19分的舞蹈视频作为训练集，画风如下：

﻿![img](https://mmbiz.qpic.cn/mmbiz_gif/YicUhk5aAGtBlUzTtzY9SuukOqnCSpt8Gicly7QQUMUrML3P95KowCCanU8TE9VrV1KibryOFQiaKeMic1sdbcQwMFw/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)﻿

训练集视频地址（请注意科学前往）：

https://www.youtube.com/watch?v=NdSqAAT28v0

如果需要作者训练过的权重，也可以科学前往下面的地址下载，并将其提取到dancenet目录中。

https://drive.google.com/file/d/1LWtERyPAzYeZjL816gBoLyQdC2MDK961/view?usp=sharing

随后，运行dancegen.ipynb就可以实现本地运行了。

如果想在浏览器中运行，可在FloydHub workspace中打开代码，随后训练过的权重数据集就能自动连接至环境中。非常简单，也容易上手。

Jupyter笔记本地址：

https://nbviewer.jupyter.org/github/jsn5/dancenet/blob/master/dancegen.ipynb

# 训练过程

这是一份友好的小教程，即使你从零开始训练，这五步之后也可以自制出好看的热舞小姐姐：

1. 在imgs/文件夹中，将训练视频中的序列图像依次标记为1.jpg，2.jpg
2. 运行model.py代码块
3. 运行gen_lv.py，将图像编码
4. 运行video_from_lv.py，测试解码的视频
5. 运行jupyter笔记本dancegen.ipynb，训练DanceNet网络，随后，视频就可以新鲜出炉了

你的训练结果如何？


# 相关

- [如何自制会跳舞的AI小姐姐？这有一份易上手的开源攻略](https://mp.weixin.qq.com/s?__biz=MzIzNjc1NzUzMw==&mid=2247502384&idx=3&sn=19d411936ab016fd7fa4ea17fb716436&chksm=e8d07d42dfa7f454b8f7ba800f394bf7a8ce2d68ecce7ffc775929c83cfb23f980c382dc1001&mpshare=1&scene=1&srcid=0814qYNAjHaHrsqe5vvIlBMJ#rd)
