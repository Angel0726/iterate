---
title: OpenAI 挑战索尼克 阿里南大队如何一举夺魁
toc: true
date: 2019-11-17
---
# OpenAI 挑战索尼克 阿里南大队如何一举夺魁



OpenAI举办了首届强化学习竞赛Retro Contest，比赛主题就是“用AI玩《刺猬索尼克》游戏”，吸引了全球数百支队伍的竞技追逐。最终，由阿里南大的联合团队Dharmaraja（法王）队以压倒性优势获得了冠军。


**前言**





![img](https://mmbiz.qpic.cn/mmbiz_gif/LwZPmXjm4WyS2D1wNpSOGaBakA1r08yZ4UoHHB53E19jyl7UicuK7LpUQdkmYicM1teq8wzkwh06aqFlYHj5BRyg/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

![img](https://mmbiz.qpic.cn/mmbiz_gif/LwZPmXjm4WyS2D1wNpSOGaBakA1r08yZO1A7iauE12MbWXWNqic9htKNVCEdpHv3Gjjx856xo6Ux8h6KeUxc17eg/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)



这个竞赛的目标，是评估强化学习算法从以往的经验中泛化的能力。具体说，就是让AI玩视频游戏《刺猬索尼克》（世嘉公司开发的一款竞速式2D动作游戏），其基本上模拟马里奥的游戏方式，玩家在尽可能短的时间内到达目的地，索尼克可以通过不停加速来快速完成关卡，最后可能需要对抗BOSS。



之前也有外部的媒体对此进行过报道，但大多是直译OpenAI的blog。这里我将从我们的视角分析一下此次比赛，希望可以抛砖引玉，不当之处还请大家批评指正。



# **OpenAI的弦外之音**



OpenAI是断然不需要靠组织各类学科竞赛来博取关注和扩大影响力的，这次破天荒的组织了这个OpenAI Retro Contest的比赛，其根本目的既不是类似商业公司寻找最优算法方案，亦不是扩展自己的人才库，而是试图立这样一个flag：强化学习的强泛化性是通往通用人工智能的关键路径之一。



我们首先来看看强化学习研究中如何评测这件事。不同于监督学习是从监督样本中学习，强化学习可以自主地跟环境交互，并通过环境反馈的信号不断调整策略得到自我提升，这个跟人类自主学习的模式非常接近。



但正是因为有大量的固有的标记样本存在，使得监督学习在评估这件事情上的机制非常完善，类似CIFAR-10这样的数据集，都非常明确地划分好了训练集和测试集，算法只需要在这样的划分下测试算法的精度，就可以和其他算法进行公平的比较。如此简单、成熟而又公平的评估方法，促使了上一个十年的刷榜竞赛，把语音识别、图像检测和识别以及自然语言处理等任务的精度提升到前所未有的程度。



反观强化学习，由于没法在固定的测试数据上评测，所以通常是需要研究者自己实现一个环境，然后汇报算法在这个环境中的性能，其不确定性要远远大于在固定数据上的测试精度（如果环境实现的有问题，可能会导致完全相反的结论），使得早年很多强化学习论文中的实验结果被认可度，相较监督学习而言，其实要低很多。



幸运的是，领域内的学者很快就关注到了这个问题，共建了类似RL-Glue、RLPy、Arcade LearningEnvironment公共的环境库。在这些库中，研究者只需要实现智能体学习部分的代码便可以完成评测。其中的集大成者，是后来居上的OpenAI的gym。除了公共环境之外，甚至允许研究者将其在gym框架下的评测结果上传到gym的网站，从而自然地形成了每个任务上的算法排行榜，从而使强化学习评测更加趋于成熟和公平。



即便于此，对于近年来的强化学习的进展仍然存在不少质疑。其核心观点大概有2个：深度强化学习并不work，真正work的可能仅仅是深度神经网络；强化学习在简单游戏上动辄上千万的训练帧数，其本质上可能更接近在memorizing搜索到的解，而不是学到了真正的知识。



对于第一点其实没有讨论的必要，举个例子，深度神经网络只是一个建模工具，强化学习是一大类学习问题，而NLP则是一个更上层的应用问题，当你使用底层是神经网络表示的强化学习算法，很好地解决了一个NLP中的一个具体问题时，你能区分是神经网络、强化学习算法和NLP建模方法谁最重要么？



但关于第二点的质疑，其实是致命的。我们先看看计算机视觉领域，我们现在可以实际使用到的人脸检测，照片场景识别等应用都是基于算法工程师在训练数据上得到的模型，这些模型在我们实际使用中（训练数据并没有我们的数据），仍然可以比较精准的检测人脸，识别场景，其根本原因就在于，监督学习在训练阶段可以以相对比较容易的方式控制模型的复杂度，从而获得较好的泛化性能。



然而这样的结论在目前的强化学习研究中并不成立，甚至没有能引起足够多的重视。以下图为例，一个在《极品飞车》游戏中训练的自动驾驶策略，如果直接应用到《QQ飞车》，99%的概率要扑街，类似的现象在Atari 2600游戏中也可以观察到。

![img](https://mmbiz.qpic.cn/mmbiz_png/LwZPmXjm4WyS2D1wNpSOGaBakA1r08yZpTOiaELJHsWMLSIvOnj95L4weR1orBGheaapthVQXiabzVaqtl6wISGA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

这个现象其实并不难解释，我们在评估强化学习算法时，常规的做法仍然是在同一个环境训练和测试，这种模式在单个环境学习到最优策略仍然很困难的历史时期是有意义的，其至少可以比较模型类似监督学习中的“拟合逼近”能力，但可能并不适用于现在或者未来的强化学习研究中。



此外，很容易发现，类似的问题在人类学习中并不存在：一个极品飞车玩的很好的选手，一般也可以非常轻松的上手QQ飞车并很快也玩的非常好。基于以上所有的观察和思考，我们不禁得出这样的结论：一个更智能的学习算法，不仅可以在一个陌生环境中自主学习到最优策略，而且可以将知识总结泛化，使其在类似的环境中仍然可以表现良好且迅速适应。



因此，OpenAI此次参照监督学习中常规的模式“训练样本->验证样本->测试样本”，设立了“训练环境->验证环境->测试环境”这一前所未有的方式。当然，类似监督学习中所有的样本都要满足i.i.d.假设，这里训练环境、验证环境和测试环境也是满足于一个环境分布的，具体地，也就是索尼克游戏。



索尼克游戏是一个系列游戏，按照在不同的场景可以细分为多个独立的小游戏，在每个小游戏中也存在多个不同的关卡，但其核心元素都是相似的，因此非常适合作为一个元环境进行评测。需要指出的是，在此之前也有一些零星的研究在做类似的事情，但这些工作要么是在非常简单的toy domain上进行（换言之其对算法比较的结论可能是完全不置信的），要么是在ALE环境中进行的（ALE中不同游戏的相似度并不大），所以并没有一个里程碑式的成果。





![img](https://mmbiz.qpic.cn/mmbiz_jpg/LwZPmXjm4WyS2D1wNpSOGaBakA1r08yZPCAT37cDaBfOXfL2T3B4A4aGItBUc1PU2SiaCjic2ibjIzzRAXibyxO2fw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



如果你想深入了解强化学习，可以参考我们之前推出的电子书：**《强化学习在阿里的技术演进与业务创新》**。本书首次在工业界系统地披露强化学习在实践应用的技术细节，其中更包含了阿里算法工程师对强化学习的深入理解、思考和创新。此书共有12个章节，作者跨越了多个阿里核心算法团队，希望能和你一起交流、探讨。



![img](https://mmbiz.qpic.cn/mmbiz_jpg/NVvB3l3e9aE2OpBwxfI3NlEX2W44r3EMSOnLtasouJgiahFI8lx9nliaiblOl0ib0kcmqGavpiaXA8SmpnWAckyzwqw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



**如何下载？**



长按识别以下二维码，关注“**阿里技术**”官方公众号，回复 “**强化学习**”，即可免费在线阅读、下载此书。

![img](https://mmbiz.qpic.cn/mmbiz_jpg/vpWlcHcJUIBTMqicR6Cic90lBic7QOXZzic7wkZaOkoFRI3iaicbGroR84mvYXe29EL4JEO6v9B88EpN9wn5UfqfTA4Q/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



**参赛团队**



![img](https://mmbiz.qpic.cn/mmbiz_png/LwZPmXjm4WyS2D1wNpSOGaBakA1r08yZibf4QHVVw35FeHlROc3UicEKbd0MHuWhHiaC9e5dOjbxOY34jWpQ66rFA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



本次比赛，阿里南大队以压倒性优势获得了冠军（优势从public leaderboard一直延续到final private leaderboard）。这支胜利之师成员包括达卿（笔者）、冷江、仁重、波克、胡张广达（即将入职阿里）以及南京大学机器学习与数据挖掘研究所的俞扬副教授。其中冷江、波克和胡张广达是今年暑假即将从学校毕业入职该团队的“准员工”。一方面，考虑在他们入职前2个多月的闲暇时间，我们将这个比赛作为其在强化学习上的实战演练课题，并在达卿，仁重和俞扬副教授的指导下，远程合作（横跨杭州-新加坡-南京）完成这次的比赛；另一方面，由于这次比赛的重点是强化学习在相似多场景中的泛化性和可迁移性，而这个问题在阿里多场景的背景下则尤为显得重要，例如AE就有这样的相似多场景：众多的海外站点。因此，我们组织这3位准阿里员工，在我们的指导下，系统性完成了这次比赛。



同时，也非常感谢阿里临在团队和九丰团队的大力支持。



**赛题简介**



在这次比赛中，OpenAI开放了3类索尼克游戏：Sonic The Hedgehog，Sonic The Hedgehog 2和Sonic 3 & Knuckles。其中每一种游戏存在着若干的关卡（level），3个游戏总计有58个关卡。每个关卡的画面和任务都各不相同，在所有的关卡上，对刺猬的动作控制是相同的（当然存在某些关卡上存在某些特殊的动作组合，即所谓的技能，但我们并没有对此进行特殊建模）。



此外，关卡之间是共享部分元素的，即使一个新的关卡你完全没有看过，但其中的元素A在某些训练关卡上见过，某些元素B在另外一些关卡上见过等等，因此，是存在泛化的可能的。



![img](https://mmbiz.qpic.cn/mmbiz_png/LwZPmXjm4WyS2D1wNpSOGaBakA1r08yZichnAeOIb9lW0yicoY5b6c0ZmCLpP2A2oickcibUec5M6TK0kIuopL0Fibg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



跟以往的监督学习算法类的比赛不同（通常只需要提交在测试样本上的预测结果），此次比赛需要提交算法。具体地，将算法依赖的代码和数据打包成docker镜像，然后上传至OpenAI的网站。在收到评测请求之后，OpenAI会针对每一个评测关卡（public leaderboard上有5个评测关卡，而final private test上则有11个），在配置有K80显卡的aws instance上以独占GPU的方式运行我们的算法（感慨一下OpenAI的壕气，毕竟有上百个团队在比赛期间不停提交评测请求），学习时间被100万个游戏帧和12小时物理时长同时限制，任意条件满足则程度退出。



在这个过程中，算法在评测关卡上的平均episode reward作为最终的分数，而总分则是在所有评测关卡上的平均。对于每一个这样的关卡，任务是学习到一个从原始的游戏的RGB图像到游戏手柄的12个物理按键的映射，以尽可能短的时间，让智能体通关。对于这次比赛而言，任务是通过在训练关卡上的预训练，尽可能地让算法在由专业的游戏设计师重新设计的关于索尼克的全新关卡上，迅速（100万帧）学习到最优通关策略。



**技术方案**



根据OpenAI提供的技术报告，针对这个问题，他们内部进行算法调研的结果显示，取得性能最好的方案是joint PPO, 要远胜于DQN的综合改进大杂烩版本Rainbow。同时考虑到ppo对内存的要求要小很多（不像DQN类的算法动辄百万的replay buffer ），且其样本利用率要高很多，所以我们计划首先从joint PPO开始，在训练游戏上得到一个全局策略，然后以此作为初始化权重，在测试游戏上进行100万帧的学习和测试。



具体地，我们采用了Deepmind在其Nature paper中描述的网络结构：原始灰度图像->3层卷积->1层稠密层->动作映射，并且结合在Atari2600上的一些训练trick（帧随机跳动、帧堆叠、奖赏缩放等），并在策略的输入输出上做了些微小的改进：



● 状态空间：灰度图像->RGB图像。因为Atari游戏相对简单，所以灰度图像提供的信息就足够了，但索尼克游戏所有的元素要丰富和复杂许多，所以我们直观上觉得，灰度图像提供的信息应该是不够的。



● 动作空间：直接学习原始12个按键的组合显然是不靠谱的（![img](https://mmbiz.qpic.cn/mmbiz_png/Z6bicxIx5naLzfA8Khl142fJ60XTpvIq86fibes3rk1UJ1OoN1n0u0VOyibbtibaO4ZJ1sibwLM2icWSAVbdLQkD0kWQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)个动作），结合官方提供的baseline和我们的经验，抽象了如下10个离散动作（actions），其中[]表示的不按任何键，对应的就是在游戏中等待，对应的操作在需要原地等待的关卡中非常有用。





```
buttons = ["B", "A", "MODE", "START", "UP", "DOWN", "LEFT", "RIGHT", "C", "Y", "X", "Z"]
actions = [['LEFT'], ['RIGHT'], ['LEFT', 'DOWN'], ['RIGHT', 'DOWN'], ['DOWN'], ['DOWN', 'B'], ['B'], [], ['LEFT', 'B'], ['RIGHT', 'B']]
```

● 奖赏函数：原始环境的奖赏函数是直接正比于智能体所在的x坐标值，即

score ∝ x，由于不同游戏通关所走的x的距离不等，所以这里做了一个归一化，使得所有游戏上，智能体达到终点会得到一个9000的分数。



同时，为了鼓励智能体以尽可能短的时间到达终点，在一个episode结束后，还会根据智能体通关的时间给予一个0-1000的奖励，即在比赛开始即通关（虽然是不可能的）会有1000的奖励，到比赛约定的4500步（对应的是5分钟的游戏时间）才通关则有0的奖励，中间的线性插值可以得到。不难发现，相比Atari的稀疏奖励，这里其实已经把问题简化了，除了1000的对通关时间的奖励，关于通关本身的奖励是稠密的：向前走就会有正向瞬时奖励，向后退就会有负向瞬时奖励。



这会显著的加速学习，但也带来一个致命缺陷，使得智能体的探索能力变得很弱，几乎没有回溯的能力，非常容易卡在一个需要回头走另一条路径的地方。针对这中情况，我们设计了一种非常巧妙的cache机制，使得智能体回退不产生负向奖赏，同时也避免了这种情况下智能体反复前进后退的trivial解。



```
self.episode_negbuf = 0...
reward = env.step(action)if reward < 0 or self.episode_negbuf < 0:
    self.episode_negbuf += reward
    reward = 0
```

**工程优化**



不同于OpenAI使用MPI实现了joint PPO，我们选择了更为方便的tensorflow的分布式并行方案：ps节点作为参数服务器，worker节点用作采样和梯度计算。具体而言，在每个worker中维护了一个retro游戏的环境，该worker通过和这个环境交互产生四元组(s,a,r,s′)数据，并在这批数据上计算策略梯度，发送至参数服务器进行全局更新。



在一般的监督学习任务中，通常定义好图，feed进数据直接sess.run就可以了，为了尽可能的利用GPU的并行，一般来说feed的数据的batch数肯定要远大于1的。



然而在我们这里每个worker中，其交互的是其中一个环境，为了收集训练用的episode数据，计算流通常是这样组织的：整个流程分为采集和训练2部分，采集部分需要频繁地和环境进行交互，在TF的op执行和环境的step函数执行之间不断切换。等收集到了足够的数据就运行训练的op，这部分和监督的网络是类似的。可能有同学要问了，难道没有那种可以端到端训练的强化学习框架么？也不能说完全没有，例如NIPS16年的best paper，但只是一个很初步的探索，离实用还有非常远的距离。

![img](https://mmbiz.qpic.cn/mmbiz_png/LwZPmXjm4WyS2D1wNpSOGaBakA1r08yZAm3rqAB40WiaYh3B5hibhnjALO6iaHPNaxGTUMXtVL67ffAySm1q3Cj2Q/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



上面所示的结构在分布式训练下带来2个显著的性能问题：



- 在采集数据阶段，每次执行π(s)都会使worker向ps发起同步请求。在我们的每次训练迭代中，会固定的采集8192步，则意味着每次迭代中至少需要从ps同步全局权重8192次。
- 在只有一个环境的前提下，π(s)输入的数据的batch只有1（只有一个状态)），无法充分利用GPU并行。



对应的解决方案也很直接



- 将π对应的actor网络复制一份到本地，每次迭代之前先将全局的actor网络同步至本地的actor网络，再使用本地actor进行采样。



- 每个worker维护n份同一个环境的副本（在不同进程中，cpu上的并行），每次π(s)对n个状态同时进行决策，即这里s的batch数为n。值得指出的是，这里的n并不是越大越好，因为总帧数固定的话，n越大则意味着学习算法在环境上的迭代次数变少了，所以真正的训练中我们采用了n=10这个相对合理的数值。结合采样步数8192，所以对应的训练op的batch数是81920，这个是会撑爆显存的，所以实际上我们将这将近8万条样本拆成若干个小batch分批执行，最终使得一张p100卡可以同时运行3个这样的worker。

#

# **joint PPO训练**



在以上的实现基础之上，我们开始了一个全局模型（暂且称之为模型 A）的训练，下图是在所有58个训练关卡上随着训练帧数递增的平均得分

![img](https://mmbiz.qpic.cn/mmbiz_png/LwZPmXjm4WyS2D1wNpSOGaBakA1r08yZR8vLLJ9G3Dib23Vscr0CiaMsgp9GchdytEI3UibA9FTcoEyTVfx8YV0sw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

上图中的分数跳跃，对应的是解锁了某一个技能，使得智能体可以向前方继续走一长段距离。从上图可以看出，这个单一全局模型A到1.21.2亿帧之后基本就收敛了，大约平均在5500左右。我们将58个游戏自己的学习曲线展示出来不难发现，仍然有大量的游戏仍然在开始的地方卡住，如下图所示：



![img](https://mmbiz.qpic.cn/mmbiz_png/LwZPmXjm4WyS2D1wNpSOGaBakA1r08yZ1wicuribNcXB71JdiaBETYRohiaX16iajRQuM1yDjx6o8aibOico9py9HayCQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



这里的人类分数是按照如下标准进行的测试：人类选手在训练游戏上进行2个小时的练习，然后在测试游戏上进行1个小时的测试，然后最终平均的分数。

#

# **分而学之**



鉴于全局模型在训练游戏上已经收敛，我们接着将从整个模型出发，分别训练每一个游戏，并在此基础之上，增加了一下奖赏。



- 金币的奖励
- 来到新位置(x,y)的奖励
- 在通关前死掉的惩罚 其中前2点依赖环境返回的info（在训练环境可以取到，但测试环境中只有RGB图像），第3点可以通过简单的启发式进行设计实现。



分而学之带来的奖赏提升还是非常明显的，综合58个游戏其平均分数提高至7000左右，我们称这时的模型为Bs（因为有58个独立模型）。



# **合众为一**



我们直观上认为，之前的全局模型A的表达能力是足够强的，只是joint训练无法收敛到全局最优，所以在得到模型Bs之后，我们使用了DeepMimic方法去fine tune模型A，具体地，对PPO中的策略网络，优化



![img](https://mmbiz.qpic.cn/mmbiz_png/Z6bicxIx5naLzfA8Khl142fJ60XTpvIq8hiaqkdJcOVAoJsrpmgbk0j1iaJicrMkjqL6MktttAOcWYmcGrJiaUeqcTA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)


对价值网络，直接做简单的回归优化即可



![img](https://mmbiz.qpic.cn/mmbiz_png/Z6bicxIx5naLzfA8Khl142fJ60XTpvIq8GsSia5IH49oVBOdWF39luzDHAzRuBPqxL0t2uv7zLNZ93hTtqNNzGow/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)


其中，为了防止策略过拟合训练游戏，我们在策略拟合的式子中加入了2项约束：更小的权重和更大的熵，都是限制策略的复杂度，使其学习到更加general的策略，以达到增强泛化性的目的。在DeepMimic收敛之后，我们继续运行了joint PPO算法在所有游戏上进行“收官”，毕竟纯粹的模仿和强化学习本身的目标并不是完全一致。通过调节系数lambda和β，我们最终得到了模型C，其L2 norm和熵和模型A非常接近，并且作为一个单一模型，在所有训练游戏上得到了前所未有的分数，如下表所示：



![img](https://mmbiz.qpic.cn/mmbiz_jpg/LwZPmXjm4WyS2D1wNpSOGaBakA1r08yZE1su1Cib9tnRRO438f8eOeW6T94olxkTqB6crI7pxuqUQy7TochZiaSw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



可以看到，和模型A同样网络、权重大小、熵的模型C，已经在训练集上取得了接近7300的成绩，对阵人类最好成绩接近有45%的胜率。



# **线上探索**



由于我们在训练过程中使用了很多在测试取不到的信息，例如智能体的坐标，所以在实际测试时，我们使用了一个非常简单的替代方案，即通过图像来判断是否到达了一个新地方，由于我们这里本质是在实现好奇心模型，所以这部分的近似也是可以的。



我们在以下三个视频中分别展示了使用原始奖赏、增加对探索的鼓励以及其进一步迭代的策略效果。从中可以看出如果仅仅使用原始的奖赏，智能体极容易困在需要回溯的路径上，而随着探索奖赏的加入，智能体甚至学会了在复杂机关上下楼的动作；随着训练的进一步迭代，智能体甚至找到了一个游戏的bug，使其不需要下楼也可以迅速达到前方远处。



![img](https://mmbiz.qpic.cn/mmbiz_gif/LwZPmXjm4WyS2D1wNpSOGaBakA1r08yZu3svEnSJJCmyT08Nc5NNd35p4QD7YnQ5I6yuO2rZAIGgP6yBHyPf2A/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

![img](https://mmbiz.qpic.cn/mmbiz_gif/LwZPmXjm4WyS2D1wNpSOGaBakA1r08yZAicnBsoU5mboj1qXI135mhQicsAbfAITp2oaibA7zQcN7Thiclnab5qcug/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

![img](https://mmbiz.qpic.cn/mmbiz_gif/LwZPmXjm4WyS2D1wNpSOGaBakA1r08yZphIoZibKth03zjX9oibzJukLeP8xaE5JWrKQWaPmPx9fHicRzxsPzvkkg/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

#

# **终极测试**



最终，在public leaderboard上top10的队伍被选择进行最终的测试。在这个测试中，所有的队伍提交的代码将在11个由游戏设计师设计的全新的索尼克游戏上进行100万帧的学习测试。为了避开随机种子的影响，所有的测试都进行了3次，取平均成绩作为最后的分数。我们提交的算法最终排名第一，其和第2，3名的学习曲线对比为：

![img](https://mmbiz.qpic.cn/mmbiz_png/LwZPmXjm4WyS2D1wNpSOGaBakA1r08yZOP9HreeGickDyFKbIiaMVZibhZyoznPVlSGqtprCIgibhY6oIKH5kysomQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



可以看到，在学习的初期，我们的优势并不显著。但随着迭代的递增，其他算法的学习曲线趋于收敛，而我们的算法仍然在稳定提升。下面的视频显示了我们的算法在不同的学习步数上的性能对比：





下面的视频显示了top3的团队在某个游戏中对通关路径的覆盖程度，可以看出我们的智能体以最快的速度完成了通关。











**思考和不足**



- 除此之外，我们还尝试了很多方法，例如使用Yolo检测游戏里的关键元素，虽然Yolo已经是state-of-the-art的检测方法了，但是用在这里还是太慢了。
- 我们尝试通过专家replay数据进行训练，但相比于巨大的策略空间（从图像到按键组合），replay的数据量看起来是远远不够的。
- 虽然我们在训练阶段得到了看起来最优的模型C，但实际上我们最终还是保守提交了模型A，因为C的泛化性能没有我们想象的稳定（差于A），换言之，除了L2 norm和entropy，还有其他更重要的量衡量的策略的泛化性，目前尚没有被人找到。



**小彩蛋**



你曾经在游戏里，和AI角色发生过哪些难忘的故事呢？无论是爱恨情愁，还是对抗到底，抑或誓死结盟，都欢迎在留言区分享。


# 相关

- [OpenAI 挑战《索尼克》，阿里南大队如何一举夺魁？](https://mp.weixin.qq.com/s?__biz=MzIzOTU0NTQ0MA==&mid=2247487834&idx=1&sn=6fb4e0ccfc1e9fad9afa08b2388a37bb&chksm=e9292c55de5ea543117bc20e4a7fbc750edbadca0b8de0c31ba45942f3b5fead036e31a1768f&scene=21#wechat_redirect)
