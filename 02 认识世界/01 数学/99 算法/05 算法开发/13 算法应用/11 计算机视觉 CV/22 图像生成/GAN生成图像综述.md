---
title: GAN生成图像综述
toc: true
date: 2018-07-30
---
# GAN生成图像综述

## **摘要**



生成对抗网络（Generative adversarial network, GAN）[1] 自 2014 年由 Ian Goodfellow等人提出后，就越来越受到学术界和工业界的重视。而随着 GAN 在理论与模型上的高速发展，它在计算机视觉、自然语言处理、人机交互等领域有着越来越深入的应用，并不断向着其它领域继续延伸。其中，GAN在图像生成上取得了巨大的成功，这取决于 GAN 在博弈下不断提高建模能力，最终实现以假乱真的图像生成。



围绕 GAN 的研究可以分为两条主线，一是理论主线，从数学理论上研究如何解决 GAN 的不稳定性和模式崩塌问题，或者从信息理论和基于能量的模型等不同角度重新阐述它。二是应用主线，致力于将 GAN 应用于计算机视觉领域、利用 GAN 进行图像生成（指定图像合成、文本到图像，图像到图像、视频）以及将 GAN 应用于 NLP 或其它领域。利用 GAN 进行图像生成和转换是当前研究最多的，并且该领域的研究已经证明了 GAN 在图像合成中的巨大潜力。



本文从 GAN 的基本模型开始，简要总结 GAN 的原理和改进，介绍其在图像生成与转换中的研究和应用，并讨论其理论和应用中尚存在的挑战。



## **正文**



## **引言**



在机器学习中，生成模型可以用来直接对数据建模，也可以用来建立变量间的条件概率分布。它的应用十分广泛，可以用来不同的数据进行建模，比如图像、文本、声音等。以图像生成为例，我们将图像表示为一个随机向量 X，其中每一维都表示一个像素值。假设自然场景的图像都服从一个未知的分布 p(x)，我们希望通过一些观测样本来估计其分布，高维随机向量一般比较难以直接建模，需要通过一些条件独立性来简化模型。但是，自然图像中不同像素之间存在的复杂的依赖关系，很难用一个明确的图模型来描述其依赖关系，因此直接建模 p(x)比较困难，对生成模型的要求很高。



在 GAN 之前，人们就已经研究了很多生成模型，随着深度神经网络的发展，生成模型也得到了巨大的飞跃。目前有影响力的生成模型主要有 Autoregressive model（自回归模型）[2][3]、VAE[4]、GLOW[5]和 GAN[1]：



1、自回归模型（Autoregressive model）：自回归模型在 PixelRNN[2]和 PixelCNN[3]上展示了很不错的实验效果，但是由于是按照像素点去生成图像，导致计算成本高，在可并行性上受限，在处理大型数据如大型图像或视频具有一定的麻烦。



2、变分自编码器（VAE）：VAE是在 Autoencoder 的基础上让图像编码的潜在向量服从高斯分布从而实现图像的生成，优化了数据对数似然的下界，VAE在图像生成上是可并行的， 但是 VAE 存在着生成图像模糊的问题。



3、基于流的方法（Glow）：Glow提出了一种新的基于流的生成模型，通过寻找可逆的双射来实现输入和潜在空间的相互转换，其在标准图像建模基准上的对数似然性方面展示了改进的定量性能，特别是在高分辨率人脸图像合成和插值生成上取得了惊艳的效果。



4、生成对抗网络（GAN）：GAN的思想就是利用博弈不断的优化生成器和判别器从而使得生成的图像与真实图像在分布上越来越相近。GAN生成的图像比较清晰，在很多 GAN 的拓展工作中也取得了很大的提高。但是 GAN 生成中的多样性不足以及训练过程不稳定是 GAN 一直以来的问题。



然而 GAN 存在的问题并没有限制 GAN 的发展，在 GAN 改进和应用方面的文章层出不穷。其中 GAN 在图像生成上的应用最为突出，当然在计算机视觉中还有许多其他应用，如图像绘画，图像标注，物体检测和语义分割。在自然语言处理中应用 GAN 的研究也是一种增长趋势，如文本建模，对话生成，问答和机器翻译。在不同的任务中训练 GAN 可能面临不同的问题并且需要更多技术，这也使其成为具有挑战性但有趣的研究领域。



## **GAN的基本原理与研究进展**



## **（1）朴素 GAN 的基本模型**



GAN受博弈论中的零和博弈启发，将生成问题视作判别器和生成器这两个网络的对抗和博弈：生成器从给定噪声中（一般是指均匀分布或者正态分布）产生合成数据，判别器分辨生成器的的输出和真实数据。前者试图产生更接近真实的数据，相应地，后者试图更完美地分辨真实数据与生成数据。由此，两个网络在对抗中进步，在进步后继续对抗，由生成网络得到的数据也就越来越完美，逼近真实数据，从而可以生成想要得到的数据（图片、序列、视频等）。



![img](https://pic1.zhimg.com/80/v2-b6c9801e6be0bbee2484280fd2a0b9e0_hd.png)图一：GAN的基本结构



理论上 GAN 可以将任意的分布作为输入，如上图所示，输入 z 为随机噪声，在实验中我们多取 z∼(0,1)或 z ~ [-1,1]的均匀分布作为输入。生成器 G 的参数为θ，输入 z 在生成器下得到 ![[公式]](https://www.zhihu.com/equation?tex=x%5E%7B%E2%80%99%7D%3DG%28z%3B%CE%B8%29) ，输出可以被视为从分布中抽取的样本 G(z;θ)∼ ![[公式]](https://www.zhihu.com/equation?tex=p_g) 。对于训练样本 x 的数据分布为 ![[公式]](https://www.zhihu.com/equation?tex=p_%7Bdata%7D)，生成器 G 的目标是使 ![[公式]](https://www.zhihu.com/equation?tex=p_g) 近似![[公式]](https://www.zhihu.com/equation?tex=p_%7Bdata%7D+)，判别器 D 的目标则是尽可能区分生成样本和真实样本的真假，通过最大-最小博弈来进行训练，这种博弈可公式化为：

![[公式]](https://www.zhihu.com/equation?tex=%5Cmin+_%7BG%7D+%5Cmax+_%7BD%7D+%5Cmathrm%7BV%7D%28%5Cmathrm%7BD%7D%2C+%5Cmathrm%7BG%7D%29%3D%5Cmathbb%7BE%7D_%7Bx+%5Csim+%5Cmathrm%7Bp%7D+a+t+a%28x%29%7D%5B%5Clog+%5Cmathrm%7BD%7D%28%5Cmathrm%7Bx%7D%29%5D%2B%5Cmathbb%7BE%7D_%7Bz+%5Csim+%5Cmathrm%7Bp%7D_%7Bz%7D%28z%29%7D%5B%5Clog+%281-%5Cmathrm%7BD%7D%28%5Cmathrm%7BG%7D%28%5Cmathrm%7Bz%7D%29%29%29%5D)



其中第一项的 logD(x)表示判别器对真实数据的判断，第二项 log(1 - D(G(z)))表示对合成数据的判断。通过这样一个最大最小(Max-min)博弈，循环交替地分别优化 G 和 D 来训练所需要的生成式网络与判别式网络，直到到达 Nash 均衡点。



## **（2）GAN的问题与研究进展**



最早 GAN 的提出是为了通过生成模型和判别模型对抗来达到对生成图片最大相似度的伪装，比起 VAE 生成的图片会比较清晰。但是原始 GAN 模型本身也存在一些问题，主要的问题有两个



（1）判别器越好，生成器的梯度消失越严重，这样会导致在网络训练上很多时候生成器的参数基本上不会发生改变。



（2）由于网络是对抗式的，常常会造成训练时模型的崩溃（collapse mode），在训练时往往需要权衡训练的生成器与鉴别器的参数来防止崩溃的发生。这样在实际的应用上也带了很多不便。



WGAN[6]和 WGAN-GP[7]首先分析了原始 GAN 的问题，前者通过对生成样本和真实样本加噪声使得两个分布产生重叠，理论上可以解决训练不稳定；后者引入梯度惩罚，使得 GAN 训练更加稳定，收敛更快，同时能够生成更高质量的样本。LSGAN[8] 使用最小二乘损失函数代替了原始 GAN 的损失函数，让模型在训练的过程中更多的关注真实度不高的样本，缓解了 GAN 训练不稳定和生成图像质量差多样性不足的问题。DCGAN[9]将 CNN 引入生成器和判别器，借助 CNN 更强的拟合与表达能力，缓解 GAN 的问题的同时，大大提高了生成图像的能力。



![img](https://pic2.zhimg.com/80/v2-9c37c35f092599f7d6c57da193779175_hd.png)图二：DCGAN生成器结构图





此外，在研究中也产生了很多 GAN 的变种，比较突出的有将 GAN 与 Encoder 结合起来的 BiGAN[10]和 ALI[11]，与 VAE 结合起来的 VAE-GAN[12]，添加额外辅助分类器的 ACGAN[13]，以及添加监督信息的 cGAN[14]，引入信息理论的 infoGAN[15]，和引入能量的概念与方法的 EBGAN[16]等。这些变种在不同的目标上增强了 GAN 模型的拟合能力与鲁棒性，极大地扩展了 GAN 的应用范围。



## **GAN-图像生成**



根据不同的 GAN 所拥有的生成器和判别器的数量，可以将 GAN 图像生成的方法概括为三类：直接方法，迭代方法和分层方法[17]。



![img](https://pic1.zhimg.com/80/v2-e2a019be5d6108de97d899f34b3c4a48_hd.png)图三：GAN在图像生成中的三类方法



## **（1）直接法**



早期的 GANs 都遵循在其模型中使用一个生成器和一个判别器的原理，并且生成器和判别器的结构是直接的，没有分支。如 GAN [1]、DCGAN [9]、ImprovedGAN [18]，InfoGAN [15]，f-GAN [19]和 GANINT-CLS [20]。这类方法在设计和实现上比较容易，通常也能得到良好的效果。



## **（2）分层法**



分层法的主要思想是将图像分成两部分，如“样式和结构”和“前景和背景”，然后在其模型中使用两个生成器和两个鉴别器，其中不同的生成器生成图像的不同部分，然后再结合起来。两个生成器之间的关系可以是并联的或串联的。



以 SS-GAN [21]为例，其使用两个 GAN，一个 Structure-GAN用于生成表面结构，然后再由 Style-GAN补充图片细节，最后生成图片，整体结构如下所示：



![img](https://pic4.zhimg.com/80/v2-38a11b0636622792c9b6b0e3c0146baf_hd.png)图四：SS-GAN的分层结构



## **（3）迭代法**



迭代法使用具有相似或甚至相同结构的多个生成器，经过迭代生成从粗到细的图像。



以 LAPGAN [22]为例： LAPGAN中的多个生成器执行相同的任务：最低级别的生成器仅将噪声向量作为输入并输出图像，而其他生成器都从前一个生成器获取图像并将噪声矢量作为输入，这些生成器结构的唯一区别在于输入/输出尺寸的大小，每一次迭代后的图像都拥有更多清晰的细节。



![img](https://pic4.zhimg.com/80/v2-8b788a4b2f1b187a6e2896cd56d5db83_hd.png)图五：LAPGAN的迭代结构



## **GAN-图像转换**



图像到图像的转换被定义为将一个场景的可能表示转换成另一个场景的问题，例如图像结构图映射到 RGB 图像，或者反过来。该问题与风格迁移有关，其采用内容图像和样式图像并输出具有内容图像的内容和样式图像的样式的图像。图像到图像转换可以被视为风格迁移的概括，因为它不仅限于转移图像的风格，还可以操纵对象的属性。



图像到图像的转换可分为有监督和无监督两大类，根据生成结果的多样性又可分为一对一生成和一对多生成两类：



## **（1）有监督下图像到图像转换（paired image translation）**

在原始 GAN 中，因为输出仅依赖于随机噪声，所以无法控制生成的内容。但 cGAN[14]的提出使得我们可以将条件输入 y 添加到随机噪声 Z，以便生成的图像由 G(z,y)定义。条件 y 可以是任何信息，如图像标注，对象的属性、文本描述，甚至是图片。



![img](https://pic2.zhimg.com/80/v2-33cc0ea039fd05252df2d0aa694926b9_hd.png)图六：CGAN的结构



如果引入图片作为监督信息，cGAN就可以完成一些 paired data才能完成的任务，如把轮廓图转化成真实图片，把 mask 转化成真实图，把黑白图转化成真实图等。其中最具代表性的工作为 pix2pix[23]：



![img](https://pic4.zhimg.com/80/v2-7bb638cef7b86dc4b4a8ac96d0e87dc3_hd.png)图七：pix2pix结构图



pix2pix提出将 cGAN 的损失与 L1 正则化损失相结合，使得生成器不仅被训练以欺骗判别器而且还生成尽可能接近真实标注的图像，使用 L1 而不是 L2 的原因是 L1 产生较少的模糊图像。其损失函数设计如下：

含有条件信息的 GAN 损失：



![[公式]](https://www.zhihu.com/equation?tex=%5Cbegin%7Baligned%7D+%5Cmathcal%7BL%7D_%7Bc+G+A+N%7D%28G%2C+D%29%3D%26+%5Cmathbb%7BE%7D_%7Bx%2C+y%7D%5B%5Clog+D%28x%2C+y%29%5D%2B%5C%5C+%26+%5Cmathbb%7BE%7D_%7Bx%2C+z%7D%5B%5Clog+%281-D%28x%2C+G%28x%2C+z%29%29%5D%5Cend%7Baligned%7D)



约束自相似性的 L1 损失



![[公式]](https://www.zhihu.com/equation?tex=%5Cmathcal%7BL%7D_%7BL+1%7D%28G%29%3D%5Cmathbb%7BE%7D_%7Bx%2C+y%2C+z%7D%5Cleft%5B%5C%7Cy-G%28x%2C+z%29%5C%7C_%7B1%7D%5Cright%5D)



总的目标函数



![[公式]](https://www.zhihu.com/equation?tex=G%5E%7B%2A%7D%3D%5Carg+%5Cmin+_%7BG%7D+%5Cmax+_%7BD%7D+%5Cmathcal%7BL%7D_%7Bc+G+A+N%7D%28G%2C+D%29%2B%5Clambda+%5Cmathcal%7BL%7D_%7BL+1%7D%28G%29)



![img](https://pic2.zhimg.com/80/v2-d5d93f1a0004f0dbcef002f0ddbe9e0d_hd.png)图八：pix2pix生成效果



## **（2）无监督的图像到图像转换（unpaired image translation）**

虽然有监督下图像转换可以得到很好的效果，但需要的条件信息以及 paired image成为其很大的限制。但如果用无监督学习，学习到的网络可能会把相同的输入映射成不同的输出，这就意味着，我们输入任意 ![[公式]](https://www.zhihu.com/equation?tex=x_i) 并不能得到想要的输出 ![[公式]](https://www.zhihu.com/equation?tex=y_i) 。



CycleGAN [24]、DualGAN [25] 和 DiscoGAN [26]突破了这个限制，这几项工作都提出了一致/重构损失（consistent loss），采取了一个直观的思想：即生成的图像再用逆映射生成回去应该与输入的图像尽可能接近。在转换中使用两个生成器和两个判别器，两个生成器 ![[公式]](https://www.zhihu.com/equation?tex=G_%7BXY%7D) 和 ![[公式]](https://www.zhihu.com/equation?tex=G_%7BYX%7D)进行相反的转换，试图在转换周期后保留输入图像。



以 CycleGAN 为例，在 CycleGAN 中，有两个生成器， ![[公式]](https://www.zhihu.com/equation?tex=G_%7BXY%7D) 用于将图像从域 X 传输到 Y，![[公式]](https://www.zhihu.com/equation?tex=G_%7BXY%7D)用于执行相反的转换。此外，还有两个判别器![[公式]](https://www.zhihu.com/equation?tex=D_X) 和 ![[公式]](https://www.zhihu.com/equation?tex=D_Y) 判断图像是否属于该域。



![img](https://pic2.zhimg.com/80/v2-fd6c973bf435abe3ff4029d55af0f5c1_hd.png)图九：cycleGAN结构



其 Consistent loss由 L1 进行描述：



![[公式]](https://www.zhihu.com/equation?tex=%5Cbegin%7Baligned%7D+%5Cmathcal%7BL%7D_%7B%5Cmathrm%7Bcyc%7D%7D%28G%2C+F%29+%26%3D%5Cmathbb%7BE%7D_%7Bx+%5Csim+p_%7B%5Ctext+%7B+data+%7D%7D%28x%29%7D%5Cleft%5B%5C%7CF%28G%28x%29%29-x%5C%7C_%7B1%7D%5Cright%5D+%5C%5C+%26%2B%5Cmathbb%7BE%7D_%7By+%5Csim+p_%7B%5Ctext+%7B+data+%7D%7D%28y%29%7D%5Cleft%5B%5C%7CG%28F%28y%29%29-y%5C%7C_%7B1%7D%5Cright%5D+%5Cend%7Baligned%7D)





![img](https://pic2.zhimg.com/80/v2-3cb9a3053dd2199c5a8e952bd1766c85_hd.png)图十：CycleGAN的生成效果



当然，尽管 CycleGAN 和 DualGAN 具有相同的模型结构，但它们对生成器使用不同的实现。CycleGAN使用卷积架构的生成器结构，而 DualGAN 遵循 U-Net结构。在实践中可以根据不同的需求更换生成器和判别器的实现结构。



UNIT[27]在循环一致损失基础上为无监督图像生成增加了新的思路：将 VAE 与 GAN 结合起来，假设两个编码器共享相同的隐空间。我们知道，GAN的生成器的输入为一个已知分布 Z 的 latent code z. 生成器把这个分布映射成另外一个分布，这个分布中的一个样本点就是 G( z ) . 由于 UNIT 涉及到的是 2 个 domain 之间的关系，即 latent code z 既要映射到 domain ![[公式]](https://www.zhihu.com/equation?tex=X_1++) 它的样本点是![[公式]](https://www.zhihu.com/equation?tex=G_1+%28+z+%29) ，又要映射到 domain ![[公式]](https://www.zhihu.com/equation?tex=X_2) ，它的样本点是 ![[公式]](https://www.zhihu.com/equation?tex=G_2+%28+z+%29) 。 生成器 G 把 latent code 变成图像，而编码器 E 把图像还原成 latent code. 共享 latent code其实就是 ![[公式]](https://www.zhihu.com/equation?tex=E_1%28x_1%29+%3D+z+%3D+E_2%28x_2%29) 。基于共享潜在空间假设，UNIT强制在编码器的最后几层之间以及发生器的前几层之间进行权重共享。



![img](https://pic1.zhimg.com/80/v2-923b9a7a9094ec9cfd1643de212c4920_hd.png)图十一：UNIT的思路和网络结构





如图十一所示，两个 domain ![[公式]](https://www.zhihu.com/equation?tex=X_1+%2CX_2) 的样本 ![[公式]](https://www.zhihu.com/equation?tex=x_1%2Cx_2) 输入两个编码器中，![[公式]](https://www.zhihu.com/equation?tex=G_1+%EF%BC%88z_1%EF%BC%89%3D+G_1+%EF%BC%88E_1+%EF%BC%88x_1%EF%BC%89%EF%BC%89) 是还原原图 ![[公式]](https://www.zhihu.com/equation?tex=x_1) ，是生成有 ![[公式]](https://www.zhihu.com/equation?tex=X_1) 风格的图像； ![[公式]](https://www.zhihu.com/equation?tex=G_2+%EF%BC%88z_2%EF%BC%89%3D+G_2+%EF%BC%88E_2+%EF%BC%88x_2%EF%BC%89%EF%BC%89) 是还原原图 ![[公式]](https://www.zhihu.com/equation?tex=x_2%2CG_2+%EF%BC%88z_1%EF%BC%89%3D+G_2+%EF%BC%88E_1+%EF%BC%88x_1%EF%BC%89%EF%BC%89) 是生成有 ![[公式]](https://www.zhihu.com/equation?tex=X_2) 风格的图像。 ![[公式]](https://www.zhihu.com/equation?tex=D_1) ，![[公式]](https://www.zhihu.com/equation?tex=D_2) $ 分别是 domain ![[公式]](https://www.zhihu.com/equation?tex=X_1) ， ![[公式]](https://www.zhihu.com/equation?tex=X_2)的判别器。在笔者所做的一些人脸数据集上的实验中，UNIT取得了比 CycleGAN 系列更好的效果。



（3）从一对一生成到一对多生成（one-to-many translation）

从 pix2pix[23]到 CycleGAN 系列[24][25][26]，再到 UNIT[27]，这些方法实现的 image-to-image translation不管是有监督的还是无监督的，都是一对一的，也就是说输入一张图片只能产生一种风格，缺乏多样性。但其实大多数情况下，image translation是多对多的，也就是一张图片对应不同风格的转换图片。比如我们设计衣服时，一张轮廓图其实可以设计成不同风格的衣服。再比如同一个场景，不同的光照条件就是一个模式，不一定只有白天和黑夜，还可能有傍晚，清晨等。



BicycleGAN[28]首先对此进行了尝试，其在模型中添加随机噪声，通过随机采样使噪声得到不同的表达，并在输出与潜在空间上添加双向映射。双向映射指的是：不仅仅可以由潜在编码映射得到输出也可以由输出反过来生成对应的潜在编码，这可以防止两个不同的潜在编码生成同样的输出，避免输出的单一性。



但直接用不同的随机噪声来产生多样化的结果，由于 mode collapse的存在，很容易训练失败。MUNIT[29]和 DRIT[30]在 UNIT 的基础上，将 latent code进一步细化为内容编码 c 和风格编码 s 。不同 domain 的图像共享内容编码空间 C 而独享风格编码空间 S ，将内容编码 c 与不同的风格编码 s 结合起来就能得到更鲁棒的多样性的结果。



![img](https://pic3.zhimg.com/80/v2-efcb036b19e447606b501999892c5486_hd.png)图十二：MUNIT将 latent code分为内容 c 和风格 c



如下所示，BicycleGAN、MUNIT和 DRIT 都取得了不错的生成结果：



![img](https://pic2.zhimg.com/80/v2-ac7c96114ae9f9b14e18eb51951c6201_hd.png)



![img](https://pic2.zhimg.com/80/v2-aad6ebbb732c11307f1a0446633058f1_hd.png)



![img](https://pic4.zhimg.com/80/v2-f1afb170c2c6fe1e82ffa172b321ba4f_hd.png)



## **总结**



GAN在图像生成和转换中的巨大潜力已经得到研究证明，利用 GAN 进行图像到图像间的生成和转换最好已经到达几乎无法分辨的地步。除此之外，利用 GAN 进行指定图像合成，生成高清图像和视频，以及将 GAN 应用于 NLP 和其他领域的研究都还受到研究者们的重点关注。GAN图像生成仍然会是一个充满挑战与价值的课题，存在很大的研究空间。



# 相关

- [GAN生成图像综述](https://zhuanlan.zhihu.com/p/62746494)
