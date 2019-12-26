---
title: 02 GAN 对抗生成网络实例
toc: true
date: 2019-06-20
---
# 可以补充进来的

- 例子不错，其他的可以拆分出去。

# GAN 对抗生成网络实例

在 PyTorch 中实现对抗生成网络。

代码如下；

```py
import torch
import torchvision
import torch.nn as nn
import torch.nn.functional as F
from torchvision import datasets
from torchvision import transforms
from torchvision.utils import save_image
from torch.autograd import Variable


def to_var(x):
    if torch.cuda.is_available():
        x = x.cuda()
    return Variable(x)

def denorm(x):
    out = (x + 1) / 2
    return out.clamp(0, 1)

# Image processing
transform = transforms.Compose([
                transforms.ToTensor(),
                transforms.Normalize(mean=(0.5, 0.5, 0.5),
                                     std=(0.5, 0.5, 0.5))])
# MNIST dataset
mnist = datasets.MNIST(root='./mnist/',
                       train=True,
                       transform=transform,
                       download=True)
# Data loader
data_loader = torch.utils.data.DataLoader(dataset=mnist,
                                          batch_size=100,
                                          shuffle=True)
# Discriminator
D = nn.Sequential(
    nn.Linear(784, 256),
    nn.LeakyReLU(0.2),
    nn.Linear(256, 256),
    nn.LeakyReLU(0.2),
    nn.Linear(256, 1),
    nn.Sigmoid())

# Generator
G = nn.Sequential(
    nn.Linear(64, 256),
    nn.LeakyReLU(0.2),
    nn.Linear(256, 256),
    nn.LeakyReLU(0.2),
    nn.Linear(256, 784),
    nn.Tanh())

if torch.cuda.is_available():
    D.cuda()
    G.cuda()

# Binary cross entropy loss and optimizer
criterion = nn.BCELoss()
d_optimizer = torch.optim.Adam(D.parameters(), lr=0.0003)
g_optimizer = torch.optim.Adam(G.parameters(), lr=0.0003)

# Start training
for epoch in range(200):
    for i, (images, _) in enumerate(data_loader):
        # Build mini-batch dataset
        batch_size = images.size(0)
        images = to_var(images.view(batch_size, -1))

        # Create the labels which are later used as input for the BCE loss
        real_labels = to_var(torch.ones(batch_size))
        fake_labels = to_var(torch.zeros(batch_size))

        #============= Train the discriminator =============#
        # Compute BCE_Loss using real images where BCE_Loss(x, y): - y * log(D(x)) - (1-y) * log(1 - D(x))
        # Second term of the loss is always zero since real_labels == 1
        outputs = D(images)
        d_loss_real = criterion(outputs, real_labels)
        real_score = outputs

        # Compute BCELoss using fake images
        # First term of the loss is always zero since fake_labels == 0
        z = to_var(torch.randn(batch_size, 64))
        fake_images = G(z)
        outputs = D(fake_images)
        d_loss_fake = criterion(outputs, fake_labels)
        fake_score = outputs

        # Backprop + Optimize
        d_loss = d_loss_real + d_loss_fake
        D.zero_grad()
        d_loss.backward()
        d_optimizer.step()

        #=============== Train the generator ===============#
        # Compute loss with fake images
        z = to_var(torch.randn(batch_size, 64))
        fake_images = G(z)
        outputs = D(fake_images)

        # We train G to maximize log(D(G(z)) instead of minimizing log(1-D(G(z)))
        # For the reason, see the last paragraph of section 3. https://arxiv.org/pdf/1406.2661.pdf
        g_loss = criterion(outputs, real_labels)

        # Backprop + Optimize
        D.zero_grad()
        G.zero_grad()
        g_loss.backward()
        g_optimizer.step()

        if (i+1) % 300 == 0:
            print('Epoch [%d/%d], Step[%d/%d], d_loss: %.4f, '
                  'g_loss: %.4f, D(x): %.2f, D(G(z)): %.2f'
                  %(epoch, 200, i+1, 600, d_loss.item(), g_loss.item(),
                    real_score.data.mean(), fake_score.data.mean()))

    # Save real images
    if (epoch+1) == 1:
        images = images.view(images.size(0), 1, 28, 28)
        save_image(denorm(images.data), './mnist/real_images.png')

    # Save sampled images
    fake_images = fake_images.view(fake_images.size(0), 1, 28, 28)
    save_image(denorm(fake_images.data), './mnist/fake_images-%d.png' %(epoch+1))

# Save the trained parameters
torch.save(G.state_dict(), './generator.pkl')
torch.save(D.state_dict(), './discriminator.pkl')
```


输出：

```
Epoch [0/200], Step[300/600], d_loss: 0.1963, g_loss: 5.4862, D(x): 0.98, D(G(z)): 0.12
Epoch [0/200], Step[600/600], d_loss: 0.2590, g_loss: 2.3454, D(x): 0.90, D(G(z)): 0.13
Epoch [1/200], Step[300/600], d_loss: 0.2631, g_loss: 3.4495, D(x): 0.90, D(G(z)): 0.12
Epoch [1/200], Step[600/600], d_loss: 1.0826, g_loss: 1.7508, D(x): 0.64, D(G(z)): 0.36
略..
Epoch [198/200], Step[600/600], d_loss: 0.8926, g_loss: 1.9239, D(x): 0.70, D(G(z)): 0.25
Epoch [199/200], Step[300/600], d_loss: 0.7877, g_loss: 1.8884, D(x): 0.72, D(G(z)): 0.26
Epoch [199/200], Step[600/600], d_loss: 0.7669, g_loss: 1.7857, D(x): 0.70, D(G(z)): 0.22
```

中间输出的图像选择几张如下：

fake_images-1.png：


<center>

![](http://images.iterate.site/blog/image/20190620/dOBXpXrks8hW.png?imageslim){ width=55% }

</center>

fake_images-100.png：

<center>

![](http://images.iterate.site/blog/image/20190620/ilbaCA4KWVhc.png?imageslim){ width=55% }

</center>

fake_images-200.png：

<center>

![](http://images.iterate.site/blog/image/20190620/52xkcGws9sl1.png?imageslim){ width=55% }

</center>

real_images.png

<center>

![](http://images.iterate.site/blog/image/20190620/MSzitJA09zyV.png?imageslim){ width=55% }

</center>

<span style="color:red;">挺好的，但是感觉距离真实图片还是有差距呀？是因为模型还不够好吗？还是说训练的 epoch 不够？</span>


<span style="color:red;">哇塞！代码很清晰，从头到尾看下来，嗯，赞！连带着对于  G 网络的 loss 是 $\log (1-D(G(z)))$，而 D 的 loss 是 $-(\log (D(x))+\log (1-D(G(z)))$ 都理解了不少。挺好的。</span>

没有很明白的地方：

<span style="color:red;">

1. `if torch.cuda.is_available()` 之前我们是使用的这个判断来决定是否转成 cuda 的吗？忘接了。
2. `out.clamp(0, 1)` 是什么？
3. `transforms.Normalize(mean=(0.5, 0.5, 0.5), std=(0.5,0.5, 0.5))` 为什么要使用这个？
4. `lr=0.0003` 是怎么定出来的？
5. 为什么使用 `nn.Sequential` 来搭建 D 和 G？
6. `nn.BCELoss` 这个之前没有使用过。
7. 只需要对 `d_loss_real + d_loss_fake` 进行 `backward` 就行了吗？
8. 对于 `images = images.view(images.size(0), 1, 28, 28)` 想更多了解下。
9. 没想到 `from torchvision.utils import save_image` 还有 `save_image(denorm(images.data), './mnist/real_images.png')` 这个函数。挺好的，保存的图片挺 nice 的。以后可以使用下。

</span>


GAN启发自博弈论中的二人零和博弈（Two-player Game）,GAN模型中的两位博弈方分别由生成式模型（Generative Model）和判别式模型（Discriminative Model）充当。

- 生成模型 G 捕捉样本数据的分布，用服从某一分布（均匀分布，高斯分布等）的噪声 z 生成一个类似真实训练数据的样本，追求效果越像真实样本越好；
- 判别模型 D 是一个二分类器，估计一个样本来自训练数据（而非生成数据）的概率，如果样本来自于真实的训练数据，D 输出大概率，否则，D 输出小概率。


训练的过程中固定的一方更新另一方的网络权重，交替迭代，在这个过程中，双方都极力优化自己的网络，从而形成竞争对抗，直到双方达到一个动态的平衡（纳什均衡），此时生成模型 G 恢复了训练数据的分布（造出了和真实数据一模一样的样本），判别模型再也判别不出来结果，准确率为 50% ，约等于乱猜。<span style="color:red;">嗯，怎么设定模型到纳什均衡的时候结束？</span>

当固定生成网络 G 的时候，对于判别网络 D 的优化，可以这样理解：输入来自真实数据，D优化网络结构使自己输出 1，输入来自生成数据，D 优化网络结构使自己输出 0；当固定判别网络 D 的时候，G 优化自己的网络使自己输出尽可能和真实数据一样的样本，并且使得生成的样本经过 D的判别之后，D 输出高概率。

从细节上来看，生成模型可以做一些无中生有的事情。比如图片的高清化，遮住图片的一部分去修复，再或者画了一幅人脸的肖像轮廓，将其渲染成栩栩如生的照片等。<span style="color:red;">是的，厉害呀。</span>

再提高一层，生成模型的终极是创造，通过发现数据里的规律来生产一些东西，这就和真正的人工智能对应起来了。一个人，他可以通过看、听、闻去感知这世界，这是所谓的识别，他也可以说、画、想一些新的事情，这就是创造。所以，生成模型我认为是 AI 在识别任务发展相当成熟之后，AI 发展的又一个阶段。<span style="color:red;">嗯。</span>

风格迁移（Style Transfer）是深度学习众多应用中非常有趣的一种，如图所示，我们可以使用这种方法把一张图片的风格“迁移”到另一张图片上：

<center>

![](http://images.iterate.site/blog/image/20190620/7WxjKC5dpSug.png?imageslim){ width=55% }

</center>


原始的风格迁移速度是非常慢的。在 GPU 上，生成一张图片都需要 10 分钟左右，而如果只使用 CPU 而不使用 GPU 运行程序，甚至需要几个小时。这个时间还会随着图片尺寸的增大而迅速增大。

这其中的原因在于，在原始的风格迁移过程中，把生成图片的过程当作一个 “训练” 的过程。每生成一张图片，都相当于要训练一次模型，这中间可能会迭代几百几千次。图像风格转移，直观来看，就是将一幅图片的 “风格” 转移到另一幅图片，而保持它的内容不变。一般我们将内容保持不变的图称为内容图，content image，把含有我们想要的风格的图片，如梵高的星空，称为风格图，style image。<span style="color:red;">没怎么讲呀，要拆分出去。</span>

其实要实现的东西很清晰，就是需要将两张图片融合在一起，这个时候就需要定义怎么才算融合在一起。首先需要的就是内容上是相近的，然后风格上是相似的。这样我们就知道需要做的事情是什么了，我们需要计算融合图片和内容图片的相似度，或者说差异性，然后尽可能降低这个差异性；<span style="color:red;">是的。</span>同时我们也需要计算融合图片和风格图片在风格上的差异性，然后也降低这个差异性就可以了。<span style="color:red;">是的，但是要怎么计算呢？怎么去量化呢？</span>这样我们就能够量化我们的目标了。<span style="color:red;">啥？到这里就没讲了吗。。好吧，要补充下。</span>





# 相关

- 《深度学习框架 Pytorch 快速开发与实战》
