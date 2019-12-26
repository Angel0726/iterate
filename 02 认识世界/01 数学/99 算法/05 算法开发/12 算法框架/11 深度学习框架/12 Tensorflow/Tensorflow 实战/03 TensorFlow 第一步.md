---
title: 03 TensorFlow 第一步
toc: true
date: 2018-06-26 20:29:24
---
### TensorFlow 第一步



前两章我们讲了 TensorFlow的核心概念和编程模型，又谈了 TensorFlow和其他深度 学习框架的异同。本章将直奔主题，我们将学会安装 TensorFlow，然后使用 TensorFlow （1.0.0-rc0）训练一个手写数字识别（MNIST）的模型。

##### 3.1 TensorFlow的编译及安装

TensorFlow的安装方式没有 Theano 那么直接，因为它并不是全部由 python 写成的库, 底层有很多 c++乃至 CUDA 的代码，因此某些情况下可能需要编译安装（比如你的 gcc 版本比较新，硬件环境比较特殊，或者你使用的 CUDA 版本不是 realease 版预编译的）。 通常安装 TensorFlow 分为两种情况，一种是只使用 CPU’安装相对容易；另一种是使用 GPU，这种情况还需要安装 CUDA 和 cuDNN，情况相对复杂。然而不管哪种情况，我们 都推荐使用 Anaconda20 作为 python 环境，因为可以避免大量的兼容性问题。另外，本书 默认使用 python 3.5作为 python 的基础版本，相比 python 2.7，它更代表了 python未来 的趋势发展。TensorFlow目前支持得比较完善的是 Linux 和 Mac （对 Windows 的支持还 不太全面）。因为 Mac 系统主要使用 CPU 版本（Mac系统很少有使用 NVIDIA 显卡的， 而目前 TensorFlow 对 CUDA 支持得比较好，对 OpenCL 的支持还属于实验性质），安装 方式和 Linux 的 CPU 版基本一致，而 Mac —般没有 NVIDIA 的显卡，所以不适合使用

—「TensorFlow 实战

GPU版本。本章将主要讲解在 Linux 下安装 TensorFlow 的过程。另外，本书基于 2017 年 1 月发布的 TensorFlow 1.0.0-rc0版，旧版本在运行本书的代码时可能会有不兼容的情 况，所以建议读者都安装这个版本或更新版本的 TensorFlow。此外，本书推荐有条件的 读者使用 GPU 版本，因为在训练大型网络或者大规模数据时，CPU版本的速度可能会很 慢。

###### 3.1.1 安装 Anaconda

Anaconda是 python 的一个科学计算发行版，内置了数百个 python 经常会使用的库， 也包括许多我们做机器学习或数据撩掘的库，包括 Scikit-leam、NumPy、SciPy和 Pandas 等，其中可能有一些还是 TensorFlow 的依赖库。我们在安装这些库时，通常都需要花费 不少时间编译，而且经常容易出现兼容性问题，Anaconda提供了一个编译好的环境可以 直接安装。同时 Anaconda 自动集成了最新版的 MKL (Math Kernel Libaray)库，这是 Intel推出的底层数值计算库，功能上包含了 BLAS ( Basic Linear Algebra Software )等 矩阵运算库的功能，可以作为 NumPy、SciPy、Scikit-leam、NumExpr等库的底层依赖， 加速这些库的矩阵运算和线性代数运算。简单来说，Anaconda是目前最好的科学计算的 python环境，方便了安装，也提高了性能。本书强烈推荐安装 Anaconda，接下来的章节 也将默认读者使用 Anaconda 作为 TensorFlow 的 python 环境。

(1 )我们在 Anaconda 的官网上([www.continuum.io/downloads](http://www.continuum.io/downloads) )下载 Anaconda3 4.2.0 版，请读者根据自己的操作系统下载对应版本的 64 位的 python 3.5版。

(2)    我们在 Anaconda 的下载目录执行以下命令(请根据下载的文件替换对应的文件

名)。

bash Anaconda3-4.2.0-Linux-x86_64.sh

(3)    接下来我们会看到安装提示，直接按回车键确认进入下一步。然后我们会进入 Anaconda的 License 文档，这里直接按 q 键跳过，然后输入 yes 确认。下面的这一步会让 我们输入 anaconda 的安装路径，没有特殊情况的话，我们可以按回车键使用默认路径， 然后安装就自动开始了。

(4)    安装完成后，程序提示我们是否把 anaconda3 的 binary 路径加入到.bashrc，读者 可以根据自己的情况考虑，建议添加，这样以后 python 和 ipython 命令就会自动使用 Anaconda python3.5 的环境了。

###### 3.1.2 TensorFlow CPU 版本的安装

TensorFlow的 CPU 版本相对容易安装，一般分为两种情况：第一种情况，安装编译 好的 release 版本，推荐大部分用户安装这种版本；第二种情况，使用 l.O.O-rcO分支源码 编译安装，当用户的系统比较特殊，比如 gcc 版本比较新(gcc 6以上)，或者不支持使 用编译好的 release 版本，才推荐这样安装。

第一种情况，安装编译好的 release 版本，我们可以简单地执行下面这个命令。python 的默认包管理器是 pip，直接使用 pip 来安装 TensorFlow。对于 Mac 或 Windows 系统，可 在 TensorFlow 的 GitHub 仓库上的 Download and Setup页面查看编译好的程序的地趾。

export TF_BINARY_URL=<https://storage.googleapis.com/tensorflow/linux/cpu/ten>

sorflow-1.0.0rc0-cp35-cp35m-linux_x86_64.whl

pip install --upgrade $TF_BINARY_URL

第二种情况，使用 l.O.O-rcO分支的源码编译安装。

此时，确保系统安装了 gcc (版本最好介于 4.8〜5.4之间)，如果没有安装，请根据 自己的系统情况先安装 gcc，本节不再赘述。此外，为了编译 TensorFlow，我们还需要有 Google自家的编译工具 bazel ( github.com/bazelbuild/bazel )，根据其安装教程 ([www.bazel.io/versions/master/docs/install.html)](http://www.bazel.io/versions/master/docs/install.html)%e7%9b%b4%e6%8e%a5%e5%ae%89%e8%a3%85%e5%ae%83%e7%9a%84)[直接安装它的](http://www.bazel.io/versions/master/docs/install.html)%e7%9b%b4%e6%8e%a5%e5%ae%89%e8%a3%85%e5%ae%83%e7%9a%84) v0.43 release 版本即可，不 需要使用最新的 dev 版本的功能。

在正确地安装完 gcc 和 bazel 之后，接下来我们正式开始编译安装 TensorFlow，首先 先下载 TensorFlow 1.0.O-rcO 的源码：

wget <https://github.com/tensorflow/tensorflow/archive/vl.0.0-rc0.tar.gz> tar -xzvf vl.0.0-rc0.tar.gz

完成下载之后，进入 TensorFlow 代码仓库的目录，然后执行下面的命令进行配置:

cd tensorflow-1.0.0-rc0

./configure

接下来的输出要选择 python 路径，确保是 anaconda 的 python 路径即可：

Please specify the location of python. [Default is /home/wenjian/anaconda3/b in/python]:

这里选择 CPU 编译优化选项，默认的-march=native将选择本地 CPU 能支持的最佳配 置，比如 SSE4.2、AVX等。建议选择默认值。

Please specify optimization flags to use during compilation [Default is -m arch=native]:

选择是否使用 jemalloc 作为默认的 malloc 实现（仅限 Linux ），建议选择默认设置。

Do you wish to use jemalloc as the malloc implementation? (Linux only) [Y/ n]

然后它会让我们选择是否开启对 Google Cloud Platform的支持，这个在国内一般是 访问不到的，有需要的用户可以选择支持，通常选 N 即可：

Do you wish to build TensorFlow with Google Cloud Platform support? [y/N]

它会询问是否需要支持 Hadoop File System，如果有读取 HDFS 数据的需求，请选 y 选项，否则就选默认的 N 即可：

Do you wish to build TensorFlow with Hadoop File System support? [y/N]

选择是否开启 XLA JIT编译编译功能支持。这里 XLA 是 TensorFlow 目前实验性的 JIT ( Just in Time)、AOT (Ahead of Time)编译优化功能，还不太成熟，有探索欲望的 读者可以尝拭开启。

Do you wish to build TensorFlow with the XLA just-in-time compiler (experi mental)? [y/N]

然后它会让我们选择 python 的 library 路径，这里依然选择 anaconda 的路径：

Please input the desired python library path to use. Default is

[/home/wenjian/anaconda3/lib/python3.5/site-packages]

接着选择不需要使用 GPU，即 OpenCL 和 CUDA 全部选 N:

Do you wish to build TensorFlow with OpenCL support? [y/N]

Do you wish to build TensorFlow with CUDA support? [y/N]

之后可能需要下载一些依赖库的文件，完成后 configure 就顺利结束了，接下来使用 编译命令执行编译：

bazel build --copt=-march=native -c opt //tensorflow/tools/pip_package:build _pip_package

编译结束后，使用下面的命令生成 pip 安装包：

bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg

最后，使用 pip 命令安装 TensorFlow：

pip install /tmp/tensorflow_pkg/tensorflow-1.0.0rc0-cp35-cp35m-linux_x86_64. whl

###### 3.1.3 TensorFlow GPU 版本的安装

TensorFlow的 GPU 版本安装相对复杂。目前 TensorFlow 仅对 CUDA 支持较好，因 此我们首先需要一块 NVIDIA 显卡，AMD的显卡只能使用实验性支持的 OpenCL，效果 不是很好。接下来，我们需要安装显卡驱动、CUDA和 cuDNNo

CUDA是 NVIDIA 推出的使用 GPU 资源进行通用计算(Genral Purpose GHJ)的 SDK， CUDA的安装包里一般集成了显卡驱动，我们直接去官网下载 NVIDIA CUDA (<https://developer.nvidia.com/cuda-toolkit> )o

在安装前，我们需要暂停当前 NVIDIA 驱动的 X server，如果是远程连接的 Linux 机 器，可以使用下面这个命令关闭 X server：

sudo init 3

之后，我们将 CUDA 的安装包权限设置成可执行的，并执行安装程序：

chmod u+x cuda_8.0.44_linux.run

sudo ./cuda_8.0.44_linux.run    •

接下来我们正式进入 CUDA 的安装过程，先按 q 键跳过开头的 license 说明，接着输 入 accept 接收协议，然后按 y 键选择安装驱动程序：

Install NVIDIA Accelerated Graphics Driver for Linux-x86_64 367.48? (y)es/(n)o/(q)uit:

按 y 键选择安装 CUDA 并确认安装路径’ 一般可直接使用默认地址：

Install the CUDA 8.0 Toolkit?

(y)es/(n)o/(q)uit:

Enter Toolkit Location

[default is /usr/local/cuda-8.0 ]:

按 n 键不选择安装 CUDA samples (我们只是通过 TensorFlow 调用 CUD A，不直接 写 CUDA 代码)：

Install the CUDA 8.0 Samples?

(y)es/(n)o/(q)uit:

最后等待安装程序完成。

接下来安装 cuDNN,cuDNN是 NVIDIA 推出的深度学习中 CNN 和 RNN 的高度优化 的实现。因为底层使用了很多先进技术和接口(没有对外开源)，因此比其他 GPU 上的 神经网络库性能要高不少，目前绝大多数的深度学习框架都使用 cuDNN 来驱动 GPU 计 算。我们先从官网下载 cuDNN ( [https://developer.nvidia.com/rdp/cudnn-download),](https://developer.nvidia.com/rdp/cudnn-download),%e8%bf%99%e4%b8%80%e6%ad%a5)[这一步](https://developer.nvidia.com/rdp/cudnn-download),%e8%bf%99%e4%b8%80%e6%ad%a5) 可能需要先注册 NVIDIA 的账号并等待审核(需要一段时间)。

接下来再安装 cuDNN，我们到 cuda 的安装目录执行解压命令： cd /usr/local

sudo tar -xzvf ^/downloads/cudnn-8.0-linux-x64-v5.1.tgz

这样就完成了 cuDNN的安装，但我们可能还需要在系统环境里设置 CUDA 的路径： vim -/.bashrc

export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:/usr/local/cuda-8.0/extras/

CUPTI/lib64:$LD_LIBRARY_PATH

export CUDA_HOME=/usr/local/cuda-8.0

export PATH=/usr/local/cuda-8.0/bin:$PATH

source 〜/.bashrc

接下来，我们开始安装 TensorFlow。对 GPU 版的 TensorFlow，官网也提供了预编译 的包，但是这个预编译版对本地的各种依赖环境支持可能不是最佳的，如果读者试用过没 有任何兼容性问题，可以直接安装预编译版的 TensorFlow：

export TF_BINARY_URL=<https://storage.googleapis.com/tensorflow/linux/gpu/ten> sorflow_gpu-1.0.0rc0-cp35-cp35m-linux_x86_64.whl

pip install --upgrade $TF_BINARY_URL

如果预编译的版本不支持当前的 CUDA、cuDNN版本，或者存在其他兼容性问题， 可以进行编译安装，和前面提到的 CPU 版本的编译安装类似，我们需要先安装 gcc 和 bazel, 接下来下载 TensorFlow 1.0.0-rc0的代码，然后使用配置程序(./configure )进行编译配置， 前面几步和 CPU 版本的安装完全一致，直到选择是否支持 CUDA 这一步：

Do you wish to build TensorFlow with CUDA support? [y/N]

我们按 y 键选择支持 GPU，接下来选择指定的 gcc 编译器，一般选默认设置就好。

Please specify which gcc should be used by nvcc as the host compiler. [Defau It is /usr/bin/gcc]:

接下来选择要使用的 CUDA 版本、CUDA安装路径、cuDNN版本和 cuDNN 的安装 路径，这里使用的是 CUDA 8.0版本，所以 CUDA SDK Version设置为 8.0，路径设置为 /usr/local/cuda-8.0, cuDNN Version 设置为 5.1，cuDNN 路径也设置为/usr/local/cuda-8.0:

Please specify the Cuda SDK version you want to use_, e.g. 7.0. [Leave empty to use system default]:

Please specify the location where CUDA toolkit is installed. Refer to README. md for more details. [Default is /usr/local/cuda]:

Please specify the Cudnn version you want to use. [Leave empty to use system default]:

Please specify the location where cuDNN library is installed. Refer to READM E.md for more details. [Default is /usr/local/cuda]:

最后将选择 GPU 的 compute capability （ CUDA的计算兼容性），不同的 GPU 可能有 不同的 compute capability，我们可以在官网查到具体数值，比如 GTX 1080和新 titan X 是 6.1，而 GTX 980 和旧版的 GTX Titan X 是 5.2。

Please note that each additional compute capability significantly increases your build time and binary size.

[Default is: "3.5,5.2"]:

至此，配置完成，配置程序可能会开始下载对应的需要其他库的代码仓库，我们耐心 等待一会儿就好。

接下来，开始编译 GPU 版本的 TensorFlow，执行下面这个命令，注意和 CPU 版本的 编译相比，这里多了一个-config=cuda:

bazel build --copt=-march=native -c opt --config=cuda //tensorflow/tools/pip —package:build_pip_package

编译大概需要花费一■段时间，之后执行命令生成 pip 安装包并进行安装：

bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg pip install /tmp/tensorflow_pkg/tensorflow-1.0.0rc0-cp35-cp35m-linux_x86_64. whl

##### 3.2 TensorFlow 实现 Softmax Regression 识别手写数字

3.1节介绍了安装 TensorFlow，接下来我们京尤以一个机器学习领域的 Hello World任 务——MNIST 手写数字识别来探索 TensorFlow。MNIST21 ( Mixed National Institute of Standards and Technology database)是一个非常简单的机器视觉数据集，如图 3-1所示， 它由几万张 28 像素 x28 像素的手写数字坦成，这些图片只包含灰度值信息。我们的任务 就是对这些手写数字的图片进行分类，转成 0 ~ 9 —共 10 类。

图 3-1 MNIST手写数字图片示例

首先对 MNIST 数据进行加载，TensorFlow为我们提供了一个方便的封装，可以直接 加载 MNIST 数据成我们期望的格式，在 ipython 命令行或者 spyder 中直接运行下面的代 码。本节代碍主要来自 TensorFlow 的开源实现 220

from tensorflow.examples.tutorials.mnist import input_data

mnist = input_data.read_data_sets("MNIST_data/n, one_hot=True)

然后查看 mnist 这个数据集的情况，可以看到训练集有 55000 个样本，测试集有 10000 个样本，同时验证集有 5000 个样本。每一个样本都有它对应的标注信息，即 label。我们 将在训练集上训练模型，在验证集上检验效果并决定何时完成训练，最后我们在测试集评

测模型的效果(可通过准确率、召回率、Fl-score等评测)。

print(mnist.train.images.shape, mnist.train.labels.shape)

print(mnist.test.images.shape』 mnist.test.labels.shape)

print(mnist.validation.images.shape, mnist.validation.labels.shape)

前面提到我们的图像是 28 像素。28像素大小的灰度图片，如图 3-2所示。空白部分 全部为 0，有笔迹的地方根据颜色深浅有 0 到 1 之间的取值。同时，我们可以发现毎个样 本有 784 维的特征，也就是 28x28 个点的展开成 1 维的结果(28x28=784 )。因此，这里 丢弃了图片的二维结构方面的信息，只是把一张图片变成一个很长的 1 维向量。读者可能 会问，图片的空间结构信息不是很有价值吗，为什么我们要丢弃呢？因为这个数据集的分 类任务比较简单，同时也是我们使用 TensorFlow 的第一次尝试，我们不需要建立一个太 复杂的模型，所以简化了问题，丢弃空间结构的信息。后面的章节将使用卷积神经网络对 空间结构信息进行利用，并取得更高的准确率。我们将图片展开成 1 维向量时，顺序并不 重要，只要每一张图片都是用同样的顺序进行展开的就可以。

![img](06TensorFlow9e18_c4875a088c74095baibbt-38.jpg)



图 3-2手写数字灰度信息示例



匯

##### ■

题

##### ■

BE



•連

我们的训练数据的特征是一个 55000x784 的 Tensor，第一个维度是图片的编号，第 二个维度是图片中像素点的编号，如图 3-3所示。同时训练的数据 Label 是一个 55000x10 的 Tensor，如图 3-4所示，这里是对 10 个种类进行了 one-hot编码，Label是一个 10 维的 向量，只有 1 个值为 1，其余为 0。比如数字 0，对应的 Label 就是［1，0,0,0,0,0,0,0,0,0］，数 字 5 对应的 Label 就是［0,0,0,0,0，1，0,0,0,0］，数字 zz 就代表对应位置的值为 1。

图 3-3 MNIST训练数据的特征

55000

图 3-4 MNIST训练数据的 Label

准备好数据后，接下来就要设计算法了，这里使用一个叫作 Softmax Regression的算 法训练手写数字识别的分类模型。我们的数字都是 0 ~ 9之间的，所以一共有 10 个类别， 当我们的模型对一张图片进行预测时,Softmax Regression会对每一种类别估算一个概率： 比如预测是数字 3 的概率为 80%，是数字 5 的概率为 5%，最后取概率最大的那个数字作 为模型的输出结果。

当我们处理多分类任务时，通常需要使用 Softmax Regression模型。即使后面章节的 卷积神经网络或者循环神经网络，如果是分类模型，最后一层也同样是 Softmax Regression。 它的工作原理很简单，将可以判定为某类的特征相加，然后将这些特征转化为判定是这一 类的概率。上述特征可以通过一些简单的方法得到’比如对所有像素求一个加权和，而权 重是模型根据数据自动学习、训练出来的。比如某个像素的灰度值大代表很可能是数字 n 时，这个像素的权重就很大；反之，如果某个像素的灰度值大代表不太可能是数字 n 时， 这个像素的权重就可能是负的。图 3-5所示为这样的一些特征，其中明亮区域代表负的权 重，灰暗区域代表正的权重。

0    12    3    4

5    6    7    8    9

图 3-5不同数字可能对应的特征权重

我们可以将这些特征写成如下公式：f代表第/类，代表一张图片的第 J 个像素。仏 是 bias，顾名思义就是这个数据本身的一些倾向，比如大部分数字都是 0，那么 0 的特征 对应的 bias 就会很大。

feature£ = Wtj'Xj +

接下来对所有特征计算 softmax，结果如下。简单说就是都计算一个 exp 函数，然后 再进行标准化(让所有类别输出的概率值和为 I )。

softmax(x) = normalize(exp(x))

其中判定为第 f 类的概率就可由下面的公式得到。

softmax(x)£=^^

Zjexp(xj

我们先对各个类的特征求 exp 函数，然后对它们标准化，使得和为 1，特征的值越大 的类，最后输出的概率也越大；反之，特征的值越小的类，输出的概率也越小。最后的标 准化操作保证所有的概率没有为 0 或者为负数的，同时它们的和为 1，也满足了概率的分 布。如果将整个计算过程可视化，结果如图 3-6所示。

softmQJx

图 3-6 Softmax Regression 的流程



一®



接着，如果将图 3-6中的连线变成公式，结果如图 3-7所示，最后将元素相乘变成矩 阵乘法，结果如图 3-8所示。

| 2/1  |          | + Wlt2X2 + 叭，3工 3 +                |
| ---- | -------- | ------------------------------------ |
| V2   | =softmax | W2,iXi + W2,2 工 2 + ^2,3^3 + b2      |
| 2/3  |          | 灰 3，1 工 1 + 灰 3,2 工 2 + ^3,3^3 + ^3 |

图 3-7 Softmax Regression元素乘法示例

| yi   |          |      |      |       |       |      |      |      | 乂   |      |      |
| ---- | -------- | ---- | ---- | ----- | ----- | ---- | ---- | ---- | ---- | ---- | ---- |
| V2   | =softmax |      | 恥,1 | 1^2,2 | W2,3  |      | ^2   | +    | ^2   |      |      |
| V3   |          |      |      | IV3,2 | 1^3,3 |      | 工 3  |      | 石 3  |      |      |

图 3-8 Softmax Regression矩阵乘法示例

上述鉅阵运算表达写成公式的话，可以用下面这样简洁的一行表达。

y — softmax(l4/x + h)

接下来京尤使用 TensorFlow 实现一个 Softmax Regression。其实在 python 中，当还没 有 TensorFlow 时，通常使用 NumPy 做密集的运算操作。因为 NumPy 是使用 C 和一部分 fortran语言编写的，并且调用 openblas、mkl等矩阵运算库，因此效率很高。其中每一个 运算操作的结果都要返回到 python 中，但不同语言之间传输数据可能会带来比较大的延 迟 0TensorFlow 同样也把密集的复杂运算搬到 python 外执行，不过做得更彻底。TensorFlow 通过定义一个计算图将所有的运算操作全部运行在 python 外面，比如通过 c++运行在 CPU上或者通过 CUDA 运行在 GPU 上，而不需要每次把运算完的数据传回 python。

首先载入 TensorFlow 库，并创建一个新的 InteractiveSession，使用这个命令会将这个 session注册为默认的 session，之后的运算也默认跑在这个 session 里，不同 session 之间 的数据和运算应该都是相互独立的。接下来创建一个 Placeholder，即输入数据的地方 o Placeholder的第一个参数是数据类型，第二个参数［None，784］代表 tensor 的 shape，也就 是数据的尺寸，这里 None 代表不限条数的输入，784代表每条输入是一个 784 维的向量。

import tensorflow as tf

sess = tf.InteractiveSession()

x = tf.placeholder(tf.float32［None) 784］)

接下来要给 Softmax Regression模型中的 weights 和 biases 创建 Variable 对象，第 1 章中提到 Variable 是用来存储模型参数的。不同于存储数据的 tensor 一旦使用掉就会消失， Variable在模型训练迭代中是持久化的(比如一直存放在显存中)，它可以长期存在并且 在每轮迭代中被更新。我们把 weights 和 biases 全部初始化为 0，因为模型训练时会自动 学习合适的值，所以对这个简单模型来说初始值不太重要。不过对复杂的卷积网络、循环 网络或者比较深的全连接网络，初始化的方法就比较重要，甚至可以说至关重要。注意这 里 W 的 shape 是［784，10］，784是特征的维数，而后面的 10 代表有 10 类，因为 Label 在 one-hot编码后是 10 维的向量。

W = tf.Variable(tf.zeros([784j 10]))

b = tf.Variable(tf.zeros([10]))

接下来就要实现 Softmax Regression算法，我们回忆一下上面提到的公式： y = softmax(lVx + b)。改写成 TensorFlow 的语言就是下面这行代码。 y = tf.nn.softmax(tf.matmul(x, W) + b)

Softmax是 tf.nn下面的一个函数，而 tf.nn则包含了大量神经网络的组件，tf.matmul 是 TensorFlow 中的矩阵乘法函数。我们使用一行简单的代码就定义了 Softmax Regression, 语法和直接写数学公式很像。然而 TensorFlow 最厉害的地方还不是定义公式，而是将 forward和 backward 的内容都自动实现(无论 CPU 或是 GKJ 上)，只要接下来定义好 loss, 训练时将会自动求导并进行悌度下降，完成对 Softmax Regression模型参数的自动学习。

为了训练模型，我们需要定义一个 loss function来描述模型对问题的分类精度。Loss 越小，代表模型的分类结果与真实值的偏差越小，也就是说模型越精确。我们一开始给模 型填充了全零的参数，这样模型会有一个初始的 loss，而训练的目的是不断将这个 loss 减小，直到达到一个全局最优或者局部最优解。对多分类问题，通常使用 cross-entropy 作为 loss function。Cross-entropy 最早出自信息论(Information Theory )中的信息嫡(与 压缩比率等有关)，然后被用到很多地方，包括通信、纠错码、博弈论、机器学习等。 Cross-entropy的定义如下，其中 y 是预测的概率分布，乂是真实的概率分布(即 Label 的 one-hot编码)，通常可以用它来判断模型对真实概率分布估计的准确程度。

"y,(y) =

在 TensorFlow 中定义 cross-entropy也很容易，代码如下。

y_ = tf.placeholder(tf.float32j [None/ 10])

cross_entropy = tf.reduce_mean(-tf.reduce_sum(y_ * tf.log(y),

reduction_indices=[l]))

先定义一个 placeholder，输入是真实的 label，用来计算 cross-entropy。这里的 y_ * tf.log(y)也就是前面公式中的 y\log(y£)，tf.reduce_sum也就是求和的 Z，而 tf.reduce_mean则用来对每个 batch 数据结果求均值。

现在我们有了算法 Softmax Regression的定义，又有了损失函数 cross-entropy的定义，

只需要再定义一个优化算法即可开始训练。我们釆用常见的随机梯度下降 SGD( Stochastic Gradient Descent )。定义好优化算法后，TensorFlow就可以根据我们定义的整个计算图(我 们前面定义的各个公式已经自动构成了计算图)自动求导，并根据反向传播(Back Propagation )算法进行训练，在每一轮迭代时更新参数来减小 loss。在后台 TensorFlow 会 自动添加许多运算操作(Operation)来实现刚才提到的反向传播和梯度下降，而给我们提 供的就是一个封装好的优化器，只需要每轮迭代时 feed 数据给它就好。我们直接调用 tf.train.GradientDescentOptimizer，并设置学习速率为 0.5，优化目标设定为 cross-entropy, 得到进行训练的操作 train_step。当然，TensorFlow中也有很多其他的优化器，使用起来 也非常方便，只需要修改函数名即可。

train_step = tf.train.GradientDescentOptimizer(0.5).minimize(cross_entropy)

下一步使用 TensorFlow 的全局参数初始化器 tf.global_variables_initializer，并直接执 行它的 run 方法。

tf.global一 variables_initializer().run()

最后一步，我们开始迭代地执行训练操作 train_SteP。这里每次都随机从训练集中抽 取 100 条样本构成一个 mini-batch，并 feed 给 placeholder，然后调用 train_step对这些样 本进行训练。使用一小部分样本进行训练称为随机梯度下降，与每次使用全部样本的传统 的梯度下降对应。如果每次训练都使用全部样本，计算量太大，有时也不容易跳出局部最 优。因此，对于大部分机器学习问题，我们都只使用一小部分数据进行随机梯度下降，这 种做法绝大多数时候会比全样本训练的收敛速度快很多。

for i in range(1000):

batch_xsj batch_ys = mnist.train.next_batch(100)

train_step.run({x: batch_xSj y_:, batch_ys})    .    .

现在我们已经完成了训练，接下来就可以对模型的准确率进行验证。下面代码中的 tf.argmax是从一个 tensor 中寻找最大值的序号，tf.argmax(y, 1)就是求各个预测的数字中 概率最大的那一个，而 tf.argmax(y_，1)则是找样本的真实数字类别。而 tf.equal方法则用 来判断预测的数字类别是否就是正确的类别，最后返回计算分类是否正确的操作 correct_predition o

correct_prediction = tf.equal(tf.argmax(y, 1), tf.argmax(y_J 1))

我们统计全部样本预测的 accuracy，这里需要先用 tf.cast将之前 correct_prediction输 出的 bool 值转换为 float32，再求平均。

accuracy = tf.reduce_mean(tf.castCcorrec^prediction^ tf.float32))

我们将测试数据的特征和 Label 输入评测流程 accuracy，计算模型在测试集上的准确 率，再将结果打印出来。使用 Softmax Regression对 MNIST 数据进行分类识别，在测试 集上平均准确率可达 92%左右。

print(accuracy.eval({x: moist.test.images, y_: mnist.test.labels}))

通过上面的这个简单例子，我们使用 TensorFlow 实现了一个简单的机器学习算法 Softmax Regression，这可以算作是一个没有隐含层的最浅的神经网络。我们来回忆一下 整个流程，我们做的事情可以分为 4 个部分。

(1 )定义算法公式，也就是神经网络 forward 时的计算。

(2)    定义 loss，选定优化器，并指定优化器优化 loss。

(3)    迭代地对数据进行训练。

(4 )在测试集或验证集上对准确率进行评测。

这几个步骤是我们使用 TensorFlow 进行算法设计、训练的核心步骤，也将会贯穿之 后其他类型神经网络的章节。需要注意的是，TensorFlow和 Spark 类似，我们定义的各个 公式其实只是 Computation Graph，在执行这行代码时，计算还没有实际发生，只有等调 用 run 方法，并 feed 数据时计算才真正执行。比如 cross_entropy、train_step、accuracy等 都是计算图中的节点，而并不是数据结果，我可以通过调用 nm 方法执行这些节点或者说 运算操作来获取结果。

我们再来看看 Softmax Regression达到的效果，准确率为 92%，虽然是一个还不错的 数字，但是还达不到实用的程度。手写数字的识别的主要应用场景是识别银行支票，如果 准确率不够高，可能会引起严重的后果。后面我们将讲解使用多层感知机和卷积网络，来 解决 MNIST 手写数字识别问题的方法。事实上，MNIST数字识别也算是卷积神经网络的 首个经典应用，LeCun的 LeNet5 在 20 世纪 90 年代就已经提出，而且可以达到 99%的准 确率，可以说是领先时代的重大突破。可惜后面因为计算能力制约，卷积神经网络的研究 一直没有太大突破，神经网络也一度被 SVM 等超越而陷入低谷。在 20 世纪初的很多年 里，神经网络几乎被大家遗忘，相关研究一直不受重视，这一段是深度学习的一次冰期（神 经网络的研究一共有三次大起大落）。2006年，Hinton等人提出逐层预训练来初始化权重 的方法及利用多层 RBM 堆叠的神经网络 DBN，神经网络才逐渐重回大家视野。Hinton 揭示了神经网络的最大价值在于对特征的自动提取和抽象，它免去了人工提取特征的烦琐， 可以自动找出复杂且有效的高阶特征。这一点类似人的学习过程，先理解简单概念，再逐 渐递进到复杂概念，神经网络每加深一层，可以提取的特征就更抽象。随着 2012 年 Hinton 学生的研究成果 AlexNet 以巨大优势摘得了当年 ImageNet ILSVRC比赛的第一名，深度 学习的热潮被再次点燃。ImageNet是一个非常著名的图片数据集，大致有几百万张图片 和 1000 类（大部分是动物，约有几百类的动物）。官方会每年举办一次大型的比赛，有图 片分类、目标位置检测、视频检测、图像分割等任务。在此之前，参赛读物都是做特征工 程，然后使用 SVM 等模型进行分类。而 AlexNet 夺冠后，每一年 ImageNet ILSVRC的 冠军都是依靠深度学习、卷积神经网络，而且趋势是层数越深，效果越好。2015年，微 软研究院提出的 ResNet 甚至达到惊人的 152 层深，并在分类准确率上有了突破性的进展。 至此，深度学习在复杂机器学习任务上的巨大优势正式确立，现在基本在任何问题上，仔 细设计的神经网络都可以取得比其他算法更好的准确率和泛化性，前提是有足够多的数据。

接下来的章节，我们会继续使用其他算法在 MNIST 数据集上进行训练，事实上，现 在的 Softmax Regression加入隐含层变成一个正统的神经网络后，再结合 Dropout、Adagrad、 ReLU等技术准确率就可以达到 98%。引入卷积层、池化层后，也可以达到 99%的正确率。 而目前基于卷积神经网络的 state-of-the-art的方法已经可以达到 99.8%的正确率。
