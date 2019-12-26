---
title: 如何保持运动小车上的旗杆屹立不倒 TensorFlow利用A3C算法训练智能体玩CartPole游戏
toc: true
date: 2019-11-17
---
# 如何保持运动小车上的旗杆屹立不倒 TensorFlow利用A3C算法训练智能体玩CartPole游戏



> 本教程讲解如何使用深度强化学习训练一个可以在 CartPole 游戏中获胜的模型。研究人员使用 tf.keras、OpenAI 训练了一个使用「异步优势动作评价」（Asynchronous Advantage Actor Critic，A3C）算法的智能体，通过 A3C 的实现解决了 CartPole 游戏问题，过程中使用了贪婪执行、模型子类和自定义训练循环。



该过程围绕以下概念运行：



- 贪婪执行——贪婪执行是一个必要的、由运行定义的接口，此处的运算一旦从 Python 调用，就要立刻执行。这使得以 TensorFLow 开始变得更加容易，还可以使研究和开发变得更加直观。
- 模型子类——模型子类允许通过编写 tf.keras.Model 子类以及定义自己的正向传导通路自定义模型。由于可以强制写入前向传导，模型子类在贪婪执行启用时尤其有用。
- 自定义训练循环。



本教程遵循的基本工作流程如下：



- 建立主要的智能体监管
- 建立工作智能体
- 实现 A3C 算法
- 训练智能体
- 将模型表现可视化



本教程面向所有对强化学习感兴趣的人，不会涉及太深的机器学习基础，但主题中涵盖了高级策略网络和价值网络的相关知识。此外，我建议阅读 Voldymyr Mnih 的《Asynchronous Methods for Deep Reinforcement Learning》（https://arxiv.org/abs/1602.01783），这篇文章很值得一读，而且文中涉及到本教程采用的算法的很多细节。



**什么是 Cartpole？**



Cartpole 是一个游戏。在该游戏中，一根杆通过非驱动关节连接到小车上，小车沿无摩擦的轨道滑动。初始状态（推车位置、推车速度、杆的角度和杆子顶端的速度）随机初始化为 +/-0.05。通过对车施加 +1 或 -1（车向左或向右移动）的力对该系统进行控制。杆开始的时候是直立的，游戏目标是防止杆倒下。杆保持直立过程中的每个时间步都会得到 +1 的奖励。当杆倾斜 15 度以上或小车与中间位置相隔 2.4 个单位时游戏结束。

![img](https://mmbiz.qpic.cn/mmbiz_gif/KmXPKA19gW8z8b8pQrp0s9dStZDzKIyLo5KBbL3t5wJeU17Q7UdcpFzG6BibcbskT94db4ZgcTd8LibIHlKAGWQw/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)



**代码**



- 完整代码：https://github.com/tensorflow/models/blob/master/research/a3c_blogpost/a3c_cartpole.py
- 安装指南：https://github.com/tensorflow/models/tree/master/research/a3c_blogpost



**建立基线**



为了正确判断模型的实际性能以及评估模型的度量标准，建立一个基线通常非常有用。举个例子，如果返回的分数很高，你就会觉得模型表现不错，但事实上，我们很难确定高分是由好的算法还是随机行为带来的。在分类问题的样例中，可以通过简单分析类别分布以及预测最常见的类别来建立基线。但我们该如何针对强化学习建立基线呢？可以创建随机的智能体，该智能体可以在我们的环境中做出一些随机行为。



```
class RandomAgent:
  """Random Agent that will play the specified game

    Arguments:
      env_name: Name of the environment to be played
      max_eps: Maximum number of episodes to run agent for.
  """
  def __init__(self, env_name, max_eps):
    self.env = gym.make(env_name)
    self.max_episodes = max_eps
    self.global_moving_average_reward = 0
    self.res_queue = Queue()

  def run(self):
    reward_avg = 0
    for episode in range(self.max_episodes):
      done = False
      self.env.reset()
      reward_sum = 0.0
      steps = 0
      while not done:
        # Sample randomly from the action space and step
        _, reward, done, _ = self.env.step(self.env.action_space.sample())
        steps += 1
        reward_sum += reward
      # Record statistics
      self.global_moving_average_reward = record(episode,
                                                 reward_sum,
                                                 0,
                                                 self.global_moving_average_reward,
                                                 self.res_queue, 0, steps)

      reward_avg += reward_sum
    final_avg = reward_avg / float(self.max_episodes)
    print("Average score across {} episodes: {}".format(self.max_episodes, final_avg))
    return final_avg
```



就 CartPole 这个游戏而言，我们在 4000 个循环中得到了 ～20 的平均值。为了运行随机的智能体，要先运行 python 文件： python a3c_cartpole.py—algorithm=random—max-eps=4000。



**什么是异步优势动作评价算法**



异步优势动作评价算法是一个非常拗口的名字。我们将这个名字拆开，算法的机制就自然而然地显露出来了：



- 异步：该算法是一种异步算法，其中并行训练多个工作智能体，每一个智能体都有自己的模型和环境副本。由于有更多的工作智能体并行训练，我们的算法不仅训练得更快，而且可以获得更多样的训练经验，因为每一个工作体的经验都是独立的。
- 优势：优势是一个评价行为好坏和行为输出结果如何的指标，允许算法关注网络预测值缺乏什么。直观地讲，这使得我们可以衡量在给定时间步时遵循策略 π 采取行为 a 的优势。
- 动作-评价：算法的动作-评价用了在策略函数和价值函数间共享层的架构。



**它是如何起作用的？**



在更高级别上，A3C 算法可以采用异步更新策略，该策略可以在固定的经验时间步上进行操作。它将使用这些片段计算奖励和优势函数的估计值。每一个工作智能体都会遵循下述工作流程：



1. 获取全局网络参数
2. 通过遵循最小化（t_max：到终极状态的步长）步长数的局部策略与环境进行交互
3. 计算价值损失和策略损失
4. 从损失中得到梯度
5. 用梯度更新全局网络
6. 重复



在这样的训练配置下，我们期望看到智能体的数量以线性速度增长。但你的机器可以支持的智能体数量受可用 CPU 核的限制。此外，A3C 可以扩展到多个机器上，有一些较新的研究（像是 IMPALA（https://deepmind.com/blog/impala-scalable-distributed-deeprl-dmlab-30/））甚至支持它更进一步扩展。但添加太多机器可能会对速度和性能产生一些不利影响。参阅这篇文章（https://arxiv.org/abs/1602.01783）以获取更深入的信息。



**重新审视策略函数和价值函数**



如果你已经对策略梯度有所了解，那么就可以跳过这一节。如果你不知道什么是策略或价值，或是想要快速复习一些策略或价值，请继续阅读。



策略的思想是在给定输入状态的情况下参数化行为概率分布。我们通过创建一个网络来了解游戏的状态并决定我们应该做什么，以此来实现这个想法。因此，当智能体进行游戏时，每当它看到某些状态（或是相似的状态），它就可以在给定输入状态下计算出每一个可能的行为的概率，然后再根据概率分布对行为进行采样。从更深入的数学角度进行分析，策略梯度是更为通用的分数函数梯度估计的特例。一般情况下将期望表示为 p(x | ) [f(x)]；但在我们的例子中，奖励（优势）函数的期望，f，在某些策略网络中，p。然后再用对数导数方法，算出如何更新我们的网络参数，使得行为样本能获得更高的奖励并以 ∇ Ex[f(x)] =Ex[f(x) ∇ log p(x)] 结束。这个等式解释了如何根据奖励函数 f 在梯度方向上转换 θ 使得分最大化。



价值函数基本上就可以判断某种状态的好坏程度。从形式上讲，价值函数定义了当以状态 s 开始，遵循策略 p 时得到奖励的期望总和。这是模型中「评价」部分相关之处。智能体使用价值估计（评价）来更新策略（动作）。



**实现**



我们首先来定义一下要使用的模型。主智能体有全局网络，每个局部的工作体在它们自己的程序中拥有该网络的的副本。我们用模型子类实例化该模型。模型子类为我们提供了更高的灵活度，而代价是冗余度也更高。



```
public class MyActivity extends AppCompatActivity {
@Override  //override the function
    protected void onCreate(@Nullable Bundle savedInstanceState) {
       super.onCreate(savedInstanceState);
       try {
            OkhttpManager.getInstance().setTrustrCertificates(getAssets().open("mycer.cer");
            OkHttpClient mOkhttpClient= OkhttpManager.getInstance().build();
        } catch (IOException e) {
            e.printStackTrace();
        }
}
```



从前向传递中可以看出，模型得到输入后会返回策略概率 logits 和 values。



**主智能体——主线程**



我们来了解一下该操作的主体部分。主智能体有可以更新全局网络的共享优化器。该智能体实例化了每个工作智能体将要更新的全局网络以及用来更新它的优化器。这样每个工作智能体和我们将使用的优化器就可以对其进行更新。A3C 对学习率的传递是很有弹性的，但针对 Cart Pole 我们还是要用学习率为 5e-4 的 AdamOptimizer（https://www.tensorflow.org/api_docs/python/tf/train/AdamOptimizer）。



```
class MasterAgent():
  def __init__(self):
    self.game_name = 'CartPole-v0'
    save_dir = args.save_dir
    self.save_dir = save_dir
    if not os.path.exists(save_dir):
      os.makedirs(save_dir)

    env = gym.make(self.game_name)
    self.state_size = env.observation_space.shape[0]
    self.action_size = env.action_space.n
    self.opt = tf.train.AdamOptimizer(args.lr, use_locking=True)
    print(self.state_size, self.action_size)

    self.global_model = ActorCriticModel(self.state_size, self.action_size)  # global network
    self.global_model(tf.convert_to_tensor(np.random.random((1, self.state_size)), dtype=tf.float32))
```



主智能体将运行训练函数以实例化并启动每一个智能体。主智能体负责协调和监管每一个智能体。每一个智能体都将异步运行。（因为这是在 Python 中运行的，从技术上讲这不能称为真正的异步，由于 GIL（全局解释器锁）的原因，一个单独的 Python 过程不能并行多个线程（利用多核）。但可以同时运行它们（在 I/O 密集型操作过程中转换上下文）。我们用线程简单而清晰地实现了样例。



```
def train(self):
    if args.algorithm == 'random':
      random_agent = RandomAgent(self.game_name, args.max_eps)
      random_agent.run()
      return

    res_queue = Queue()

    workers = [Worker(self.state_size,
                      self.action_size,
                      self.global_model,
                      self.opt, res_queue,
                      i, game_name=self.game_name,
                      save_dir=self.save_dir) for i in range(multiprocessing.cpu_count())]

    for i, worker in enumerate(workers):
      print("Starting worker {}".format(i))
      worker.start()

    moving_average_rewards = []  # record episode reward to plot
    while True:
      reward = res_queue.get()
      if reward is not None:
        moving_average_rewards.append(reward)
      else:
        break
    [w.join() for w in workers]

    plt.plot(moving_average_rewards)
    plt.ylabel('Moving average ep reward')
    plt.xlabel('Step')
    plt.savefig(os.path.join(self.save_dir,
                             '{} Moving Average.png'.format(self.game_name)))
    plt.show()
```



**Memory 类——存储我们的经验**



此外，为了更简单地追踪模型，我们用了 Memory 类。该类的功能是追踪每一步的行为、奖励和状态。



```
class Memory:
  def __init__(self):
    self.states = []
    self.actions = []
    self.rewards = []

  def store(self, state, action, reward):
    self.states.append(state)
    self.actions.append(action)
    self.rewards.append(reward)

  def clear(self):
    self.states = []
    self.actions = []
    self.rewards = []
```



现在我们已经知道了算法的关键：工作智能体。工作智能体继承自 threading 类，我们重写了来自 Thread 的 run 方法。这使我们得以实现 A3C 中的第一个 A——异步。我们先通过实例化局部模型和设置特定的训练参数开始。



```
class Worker(threading.Thread):
  # Set up global variables across different threads
  global_episode = 0
  # Moving average reward
  global_moving_average_reward = 0
  best_score = 0
  save_lock = threading.Lock()

  def __init__(self,
               state_size,
               action_size,
               global_model,
               opt,
               result_queue,
               idx,
               game_name='CartPole-v0',
               save_dir='/tmp'):
    super(Worker, self).__init__()
    self.state_size = state_size
    self.action_size = action_size
    self.result_queue = result_queue
    self.global_model = global_model
    self.opt = opt
    self.local_model = ActorCriticModel(self.state_size, self.action_size)
    self.worker_idx = idx
    self.game_name = game_name
    self.env = gym.make(self.game_name).unwrapped
    self.save_dir = save_dir
    self.ep_loss = 0.0
```

**运行算法**



下一步是要实现 run 函数。这是要真正运行我们的算法了。我们将针对给定的全局最大运行次数运行所有线程。这是 A3C 中的「动作」所起的作用。我们的智能体会在「评价」判断行为时根据策略函数采取「行动」，这是我们的价值函数。尽管这一节的代码看起来很多，但实际上没有进行太多操作。在每一个 episode 中，代码只简单地做了这些：



\1. 基于现有框架得到策略（行为概率分布）。

\2. 根据策略选择行动。

\3. 如果智能体已经做了一些操作（args.update_freq）或者说智能体已经达到了终端状态（结束），那么：

a. 用从局部模型计算得到的梯度更新全局模型。

\4. 重复



```
def run(self):
    total_step = 1
    mem = Memory()
    while Worker.global_episode < args.max_eps:
      current_state = self.env.reset()
      mem.clear()
      ep_reward = 0.
      ep_steps = 0
      self.ep_loss = 0

      time_count = 0
      done = False
      while not done:
        logits, _ = self.local_model(
            tf.convert_to_tensor(current_state[None, :],
                                 dtype=tf.float32))
        probs = tf.nn.softmax(logits)

        action = np.random.choice(self.action_size, p=probs.numpy()[0])
        new_state, reward, done, _ = self.env.step(action)
        if done:
          reward = -1
        ep_reward += reward
        mem.store(current_state, action, reward)

        if time_count == args.update_freq or done:
          # Calculate gradient wrt to local model. We do so by tracking the
          # variables involved in computing the loss by using tf.GradientTape
          with tf.GradientTape() as tape:
            total_loss = self.compute_loss(done,
                                           new_state,
                                           mem,
                                           args.gamma)
          self.ep_loss += total_loss
          # Calculate local gradients
          grads = tape.gradient(total_loss, self.local_model.trainable_weights)
          # Push local gradients to global model
          self.opt.apply_gradients(zip(grads,
                                       self.global_model.trainable_weights))
          # Update local model with new weights
          self.local_model.set_weights(self.global_model.get_weights())

          mem.clear()
          time_count = 0

          if done:  # done and print information
            Worker.global_moving_average_reward = \
              record(Worker.global_episode, ep_reward, self.worker_idx,
                     Worker.global_moving_average_reward, self.result_queue,
                     self.ep_loss, ep_steps)
            # We must use a lock to save our model and to print to prevent data races.
            if ep_reward > Worker.best_score:
              with Worker.save_lock:
                print("Saving best model to {}, "
                      "episode score: {}".format(self.save_dir, ep_reward))
                self.global_model.save_weights(
                    os.path.join(self.save_dir,
                                 'model_{}.h5'.format(self.game_name))
                )
                Worker.best_score = ep_reward
            Worker.global_episode += 1
        ep_steps += 1

        time_count += 1
        current_state = new_state
        total_step += 1
    self.result_queue.put(None)
```



**如何计算损失？**



工作智能体通过计算损失得到所有相关网络参数的梯度。这是 A3C 中最后一个 A——advantage（优势）所起的作用。将这些应用于全局网络。损失计算如下：



- 价值损失：L=∑(R—V(s))²
- 策略损失：L=-log(𝝅(s)) * A(s)



式中 R 是折扣奖励，V 是价值函数（输入状态），𝛑 是策略函数（输入状态），A 是优势函数。我们用折扣奖励估计 Q 值，因为我们不能直接用 A3C 决定 Q 值。



```
def compute_loss(self,
                   done,
                   new_state,
                   memory,
                   gamma=0.99):
    if done:
      reward_sum = 0.  # terminal
    else:
      reward_sum = self.local_model(
          tf.convert_to_tensor(new_state[None, :],
                               dtype=tf.float32))[-1].numpy()[0]

    # Get discounted rewards
    discounted_rewards = []
    for reward in memory.rewards[::-1]:  # reverse buffer r
      reward_sum = reward + gamma * reward_sum
      discounted_rewards.append(reward_sum)
    discounted_rewards.reverse()

    logits, values = self.local_model(
        tf.convert_to_tensor(np.vstack(memory.states),
                             dtype=tf.float32))
    # Get our advantages
    advantage = tf.convert_to_tensor(np.array(discounted_rewards)[:, None],
                            dtype=tf.float32) - values
    # Value loss
    value_loss = advantage ** 2

    # Calculate our policy loss
    actions_one_hot = tf.one_hot(memory.actions, self.action_size, dtype=tf.float32)

    policy = tf.nn.softmax(logits)
    entropy = tf.reduce_sum(policy * tf.log(policy + 1e-20), axis=1)

    policy_loss = tf.nn.softmax_cross_entropy_with_logits_v2(labels=actions_one_hot,
                                                             logits=logits)
    policy_loss *= tf.stop_gradient(advantage)
    policy_loss -= 0.01 * entropy
    total_loss = tf.reduce_mean((0.5 * value_loss + policy_loss))
    return total_loss
```



工作智能体将重复在全局网络中重置网络参数和与环境进行交互、计算损失再将梯度应用于全局网络的过程。通过运行下列命令训练算法：python a3c_cartpole.py—train。



**测试算法**



通过启用新环境和简单遵循训练出来的模型得到的策略输出测试算法。这将呈现出我们的环境和模型产生的策略分布中的样本。



```
 def play(self):
    env = gym.make(self.game_name).unwrapped
    state = env.reset()
    model = self.global_model
    model_path = os.path.join(self.save_dir, 'model_{}.h5'.format(self.game_name))
    print('Loading model from: {}'.format(model_path))
    model.load_weights(model_path)
    done = False
    step_counter = 0
    reward_sum = 0

    try:
      while not done:
        env.render(mode='rgb_array')
        policy, value = model(tf.convert_to_tensor(state[None, :], dtype=tf.float32))
        policy = tf.nn.softmax(policy)
        action = np.argmax(policy)
        state, reward, done, _ = env.step(action)
        reward_sum += reward
        print("{}. Reward: {}, action: {}".format(step_counter, reward_sum, action))
        step_counter += 1
    except KeyboardInterrupt:
      print("Received Keyboard Interrupt. Shutting down.")
    finally:
      env.close()
```



你可以在模型训练好后运行下列命令：python a3c_cartpole.py。



检查模型所得分数的滑动平均：



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8z8b8pQrp0s9dStZDzKIyLGyqSejG8p0hed8JrUEibj1FVc2VP11bJ6icy5dfB4uiaToIzopV3uvT4g/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



我们应该看到得分 >200 后收敛了。该游戏连续试验 100 次平均获得了 195.0 的奖励，至此称得上「解决」了该游戏。



在新环境中的表现：

![img](https://mmbiz.qpic.cn/mmbiz_gif/KmXPKA19gW8z8b8pQrp0s9dStZDzKIyLo5KBbL3t5wJeU17Q7UdcpFzG6BibcbskT94db4ZgcTd8LibIHlKAGWQw/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)



**关键点**



该教程涵盖的内容：



- 通过 A3C 的实现解决了 CartPole。
- 使用了贪婪执行、模型子类和自定义训练循环。
- Eager 使开发训练循环变得简单，因为可以直接打印和调试张量，这使编码变得更容易也更清晰。
- 通过策略网络和价值网络对强化学习的基础进行了学习，并将其结合在一起以实现 A3C
- 通过应用 tf.gradient 得到的优化器更新规则迭代更新了全局网络。![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8Zfpicd40EribGuaFicDBCRH6IOu1Rnc4T3W3J1wE0j6kQ6GorRSgicib0fmNrj3yzlokup2jia9Z0YVeA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



*原文链接：**https://medium.com/tensorflow/deep-reinforcement-learning-playing-cartpole-through-asynchronous-advantage-actor-critic-a3c-7eab2eea5296*


# 相关

- [教程 | 如何保持运动小车上的旗杆屹立不倒？TensorFlow利用A3C算法训练智能体玩CartPole游戏](https://mp.weixin.qq.com/s?__biz=MzU1NTUxNTM0Mg==&mid=2247489235&idx=1&sn=f44db346075c8912e51924037e250508&chksm=fbd27a72cca5f364a1f0e7430e2538a5cc80b9b0b28e0bf0e9ed7532ca43dc5b57e6723fbfee&mpshare=1&scene=1&srcid=0821Y3F0xc30KTMiodw6rvw6#rd)
