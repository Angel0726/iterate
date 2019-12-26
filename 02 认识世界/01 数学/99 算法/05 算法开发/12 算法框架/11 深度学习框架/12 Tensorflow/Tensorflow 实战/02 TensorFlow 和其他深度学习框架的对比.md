---
title: 02 TensorFlow 和其他深度学习框架的对比
toc: true
date: 2018-06-26 20:29:25
---
#### TensorFlow和其他深度 学习框架的对比

##### 2.1主流深度学习框架对比

深度学习研究的热潮持续高涨，各种开源深度学习框架也层出不穷，其中包括 TensorFlow、Caffe' Keras9、CNTK?' Torch711、MXNet' Leaf13、Theano14、DeepLeaming415、 Lasagne16、Neon17，等等。然而 TensorFlow 劫杀出重围，在关注度和用户数上者 P 占据绝 对优势，大有一统江湖之势。表 2-1所示为各个开源框架在 GitHub 上的数据统计（数据 统计于 2017 年 1 月 3 日），可以看到 TensorFlow 在 star 数量、fork数量、contributor数量 这三个数据上都完胜其他对手。究其原因，主要是 Google 在业界的号召力确实强大，之 前也有许多成功的开源项目，以及 Google 强大的人工智能研发水平，都让大家对 Google 的深度学习框架充满信心，以至于 TensorFlow 在 2015 年 11 月刚开源的第一个月就积累 了 10000+的 star。其次，TensorFlow确实在很多方面拥有优异的表现，比如设计神经网 络结构的代码的简洁度，分布式深度学习算法的执行效率，还有部署的便利性，都是其得 以胜出的亮点。如果一直关注着 TensorFlow 的开发进度，就会发现基本上每星期 TensorFlow都会有 1 万行以上的代码更新，多则数万行。产品本身优异的质量、快速的

迭代更新、活跃的社区和积极的反馈，形成了良性循环，可以想见 TensorFlow 未来将继 续在各种深度学习框架中独占鳌头。

表 2-1各个开源框架在 GitHub 上的数据统计

| 框    架      | 机    构        | 支持语言         | Stars | Forks | Contributors |
| ------------- | --------------- | ---------------- | ----- | ----- | ------------ |
| TensorFlow    | Google          | python/ c++/Go/… | 41628 | 19339 | 568          |
| Caffe         | BVLC            | C-H-Zpython      | 14956 | 9282  | 221          |
| Keras         | fchollet        | python           | 10727 | 3575  | 322          |
| CNTK          | Microsoft       | c++              | 9063  | 2144  | 100          |
| MXNet         | DMLC            | python/c++/R/…   | 7393  | 2745  | 241          |
| Torch7        | Facebook        | Lua              | 6111  | 1784  | 113          |
| Theano        | U. Montreal     | python           | 5352  | 1868  | 271          |
| Deepleaming4J | DeepLearning4J  | Java/Scala       | 5053  | 1927  | 101          |
| Leaf          | AutumnAI        | Rust             | 4562  | 216   | 14           |
| Lasagne       | Lasagne         | python           | 2749  | 761   | 55           |
| Neon          | NervanaSy stems | python           | 2633  | 573   | 52           |

观察表 2-1还可以发现，Google、Microsoft、Facebook等巨头都参与了这场深度学习 框架大战，此外，还有毕业于伯克利大学的贾扬清主导开发的 Caffe，蒙特利尔大学 Lisa Lab团队开发的 Theano，以及其他个人或商业组织贡献的框架。另外，可以看到各大主 流框架基本都支持 python，目前 python 在科学计算和数据挖掘领域可以说是独领风骚。 虽然有来自 R、Julia等语言的竞争压力，但是 python 的各种库实在是太完善了’ Web开 发、数据可视化、数据预处理、数据库连接、爬虫等无所不能，有一个完美的生态环境。 仅在数据挖据工具链上，python 京尤有 NumPy、SciPy、Pandas、Scikit-leam、XGBoost 等 组件，做数据采集和预处理都非常方便，并且之后的模型训练阶段可以和 TensorFlow 等 基于 python 的深度学习框架完美衔接。

表 2-2和图 2-1所示为对主流的深度学习框架 TensorFlow、Caffe、CNTK、Theano, Torch在各个维度的评分，本书 2.2节会对各个深度学习框架进行比较详细的介绍。

表 2-2主流深度学习框架在各个维度的评分

|            | 模型设计 | 接口 | 部署 | 性能 | 架构设计 | 总体评分 |
| ---------- | -------- | ---- | ---- | ---- | -------- | -------- |
| TensorFlow | 80       | 80   | 90   | 90   | 100      | 88       |
| Caffe      | 60       | 60   | 90   | 80   | 70       | 72       |

续表

|               | 模型设计 | 接口 | 部署 | 性能 | 架构设计 | 总体评分 |
| ------------- | -------- | ---- | ---- | ---- | -------- | -------- |
| CNTK          | 50       | 50   | 70   | 100  | 60       | 66       |
| Theano        | 80       | 70   | 40   | 50   | 50       | 58       |
| Torch         | 90       | 70   | 60   | 70   | 90       | 76       |
| MXNet         | 70       | 100  | 80   | 80   | 90       | 84       |
| DeepLeaming4J | 60       | 70   | 80   | 80   | 70       | 72       |

图 2-1主流深度学习框架对比图

##### 2.2各深度学习框架简介

在本节，我们先来看看目前各流行框架的异同，以及各自的特点和优势。

###### 2.2.1 TensorFlow

TensorFlow是相对高阶的机器学习库，用户可以方便地用它设计神经网络结构，而 不必为了追求高效率的实现亲自写 c++或 CUDA18 代码。它和 Theano 一样都支持自动求 导，用户不需要再通过反向传播求解梯度。其核心代码和 Caffe —样是用 c++编写的，使 用 c++简化了线上部署的复杂度，并让手机这种内存和 CPU 资源都紧张的设备可以运行 复杂模型(python则会比较消耗资源，并且执行效率不高)。除了核心代码的 c++接口， TensorFlow 还有官方的 python、Go 和 Java 接口，是通过 SWIG ( Simplified Wrapper and Interface Generator)实现的，这样用户就可以在一个硬件配置较好的机器中用 python 进 行实验，并在资源比较紧张的嵌入式环境或需要低延迟的环境中用 c++部署模型。SWIG 支持给 C/c++代码提供各种语言的接口，因此其他脚本语言的接口未来也可以通过 SWIG 方便地添加。不过使用 python 时有一个影响效率的问题是，每一个 mini-batch要从 python 中 feed 到网络中，这个过程在 mini-batch的数据量很小或者运算时间很短时，可能会带 来影响比较大的延迟。现在 TensorFlow 还有非官方的 Julia、Node.js、R的接口支持，±也 址如下。

Julia： github.com/malmaud/TensorFlow.jl

Node.js： github.com/node-tensorflow/node-tensorflow

R： github.com/rstxidio/tensorflow

TensorFlow也有内置的 TF.Learn和 TF.Slim等上层组件可以帮助快速地设计新网络， 并且兼容 Scikit-leam estimator 接口，可以方便地实现 evaluate、grid search 'cross validation 等功能。同时 TensorFlow 不只局限于神经网络，其数据流式图支持非常自由的算法表达， 当然也可以轻松实现深度学习以外的机器学习算法。事实上，只要可以将计算表示成计算 图的形式，就可以使用 TensorFlow。用户可以写内层循环代码控制计算图分支的计算， TensorFlow会自动将相关的分支转为子图并执行迭代运算。TensorFlow也可以将计算图 中的各个节点分配到不同的设备执行，充分利用硬件资源。定义新的节点只需要写一个 python函数，如果没有对应的底层运算核，那么可能需要写 c++或者 CUDA 代码实现运 算操作。

在数据并行模式上，TensorFlow和 Parameter Server很像，但 TensorFlow 有独立的 Variable node，不像其他框架有一个全局统一的参数服务器，因此参数同步更自由。 TensorFlow和 Spark 的核心者 P 是一个数据计算的流式图，Spark面向的是大规模的数据， 支持 SQL 等操作’而 TensorFlow 主要面向内存足以装载模型参数的环境，这样可以最大 化计算效率。

TensorFlow的另外一个重要特点是它灵活的移植性，可以将同一份代码几乎不经过 修改就轻松地部署到有任意数量 CPU 或 GPU 的 PC、服务器或者移动设备上。相比于 Theano, TensorFlow还有一个优势就是它极快的编译速度，在定义新网络结构时，Theano

通常需要长时间的编译，因此尝试新模型需要比较大的代价，而 TensorFlow 完全没有这 个问题。TensorFlow还有功能强大的可视化组件 TensorBoard，能可视化网络结相和训练 过程，对于观察复杂的网络结构和监控长时间、大规模的训练很有帮助。TensorFlow针 对生产环境高度优化，它产品级的高质量代码和设计都可以保证在生产环境中稳定运行， 同时一旦 TensorFlow 广泛地被工业界使用，将产生良性循环，成为深度学习领域的事实 标准。

除了支持常见的网络结构［卷积神经网络（Convolutional Neural Network, CNN）、 循环神经网络（Recurent Neural Network, RNN ）］外，TensorFlow还支持深度强化学习 乃至其他计算密集的科学计算（如偏微分方程求解等）。TensorFlow此前不支持 symbolic loop，需要使用 python 循环而无法进行图编译优化，但最近新加入的 XLA 已经开始支持 JIT和 AOT，另外它使用 bucketing trick也可以比较高效地实现循环神经网络＜^1180戊 1（^ 的一个薄弱地方可能在于计算图必须构建为静态图，这让很多计算变得难以实现，尤其是 序列预测中经常使用的 beam search。

TensorFlow的用户能够将训练好的模型方便地部署到多种硬件、操作系统平台上， 支持 Intel 和 AMD 的 CPU，通过 CUDA 支持 NVIDIA 的 GPU （最近也开始通过 OpenCL 支持 AMD 的 GPU，但没有 CUDA 成熟），支持 Linux 和 Mac，最近在 0.12版本中也开 始尝试支持 Windows。在工业生产环境中，硬件设备有些是最新款的，有些是用了几年 的老机型，来源可能比较复杂，TensorFlow的异梅性让它能够全面地支持各种硬件和操 作系统。同时，其在 CPU 上的矩阵运算库使用了 Eigen而不是 BLAS 库，能够基于 ARM 架构编译和优化，因此在移动设备（Android和 iOS）上表现得很好。

TensorFlow在最开始发布时只支持单机，而且只支持 CUDA 6.5和 cuDNN v2，并且 没有官方和其他深度学习框架的对比结果。在 2015 年年底，许多其他框架做了各种性能 对比评测，每次 TensorFlow 都会作为较差的对照组出现。那个时期的 TensorFlow 真的不 快，性能上仅和普遍认为很慢的 Theano 比肩，在各个框架中可以算是垫底。但是凭借 Google强大的开发实力，很快支持了新版的 cuDNN （目前支持 cuDNN v5.1），在单 GPU 上的性能追上了其他框架。表 2-3 所示为 <https://github.com/soumith/convnet-benchmarks> 给 出的各个框架在 AlexNet 上单 GPU 的性能评测。

表 2-3各深度学习框架在 AlexNet 上的性能对比

| Library (库)           | Class (类)               | 总时间(ms) | 前馈时间(ms) | 反馈时间(ms) |
| ---------------------- | ------------------------ | ---------- | ------------ | ------------ |
| CuDNN[R4]-fpl6 (Torch) | cudnn.SpatialConvolution | 71         | 25           | 46           |
| Nervana-neon-fp 16     | ConvLayer                | 78         | 25           | 52           |
| CuDNN[R4]-fp32 (Torch) | cudnn.SpatialConvolution | 81         | 27           | 53           |
| TensorFlow             | conv2d                   | 81         | 26           | 55           |
| Nervana-neon-fp32      | ConvLayer                | 87         | 28           | 58           |
| fbfft (Torch)          | fbnn.SpatialConvolution  | 104        | 31           | 72           |
| Chainer                | Convolution2D            | 177        | 40           | 136          |
| cudaconvnet2*          | ConvLayer                | 177        | 42           | 135          |
| CuDNN[R2] *            | cudnn.SpatialConvolution | 231        | 70           | 161          |
| Caffe (native)         | ConvolutionLayer         | 324        | 121          | 203          |
| Torch-7 (native)       | SpatialConvolutionMM     | 342        | 132          | 210          |
| CL-nn (Torch)          | SpatialConvolutionMM     | 963        | 388          | 574          |
| Caffe-CLGreenT ea      | ConvolutionLayer         | 1442       | 210          | 1232         |

目前在单 GPU 的条件下，绝大多数深度学习框架都依赖于 cuDNN，因此只要硬件计 算能力或者内存分配差异不大，最终训练速度不会相差太大。但是对于大规模深度学习来 说，巨大的数据量使得单机很难在有限的时间完成训练。这时需要分布式计算使 GPU 集 群乃至 TPU 集群并行计算，共同训练出一个模型，所以框架的分布式性能是至关重要的。 TensorFlow在 2016 年 4 月开源了分布式版本，使用 16 块 GPU 可达单 GPU 的 15 倍提速， 在 50 块 GPU 时可达到 40 倍提速，分布式的效率很高。目前原生支持的分布式深度学习 框架不多，只有 TensorFlow、CNTK、DeepLeaming4J、MXNet 等。不过目前 TensorFlow 的设计对不同设备间的通信优化得不是很好，其单机的 reduction 只能用 CPU 处理，分布 式的通信使用基于 socket 的 RPC，而不是速度更快的 RDMA，所以其分布式性能可能还 没有达到最优。

Google 在 2016 年 2 月开源了 TensorFlow Serving19，这个组件可以将 TensorFlow 训 练好的模型导出，并部署成可以对外提供预测服务的 RESTM 接口，如图 2-2所示。有了 这个组件，TensorFlow就可以实现应用机器学习的全流程：从训练模型、调试参数，到 打包模型，最后部署服务，名副其实是一个从研究到生产整条流水线都齐备的框架。这里 引用 TensorFlow 内部开发人员的描述：“TensorFlow Serving是一个为生产环境而设计的 高性能的机器学习服务系统。它可以同时运行多个大规模深度学习模型，支持模型生命周 期管理、算法实验，并可以高效地利用 GPU 资源，让 TensorFlow 训练好的模型更快捷方 便地投入到实际生产环境”。除了 TensorFlow以外的其他框架都缺少为生产环境部署的考 虑，而 Google 作为广泛在实际产品中应用深度学习的巨头可能也意识到了这个机会，因 此开发了这个部署服务的平台。TensorFlow Serving可以说是一副王牌，将会帮 TensorFlow 成为行业标准做出巨大贡献。

图 2-2 TensorFlow Serving 架构

TensorBoard是 TensorFlow 的一组 Web 应用，用来监控 TensorFlow 运行过程，或可 视化 Computation Graph oTensorBoard 目前支持 5 种可视化:标氫 scalars )、图片(images )、 音频(audio )、直方图(histograms )和计算图(Computation Graph )。TensorBoard 的 Events Dashboard可以用来持续地监控运行时的关键指标，比如 loss、学习速率(learning rate) 或是验证集上的准确率(accuracy ); Image Dashboard则可以展示训练过程中用户设定保 存的图片，比如某个训练中间结果用 Matplotlib 等绘制(plot)出来的图片；Graph Explorer 则可以完全展示一个 TensorFlow 的计算图，并且支持缩放拖曳和查看节点属性。 TensorBoard的可视化效果如图 2-3和图 24 所示。

input new regex    e •



Split On Underscores。费 I XTypc

Selected Runs:

25 data



xenlropy    （1）



图 2-3 TensorBoard的 loss 标量的可视化

fsoftmaxJinMrJ

Iocal4



a、勺

EiponcM



Reshaped-3] ©



local3



4Y5

-.' uponent

ysCKnt.

Co3»fn!



poo!2



norm2

. j^atrn .



conv2



•:    graoenL

\-    '.Cn>d»ent

' M«C



norml

'ysOrnl



PO0H



###### f conv1 }



（shuff te_batch〕



图 2-4 TensorBoard的模型结构可视化

TensorFlow拥有产品级的高质量代码，有 Google 强大的开发、维护能力的加持，整 体架构设计也非常优秀。相比于同样基于 python 的老牌对手 Theano,TensorFlow更成熟、



更完善，同时 Theano 的很多主要开发者都去了 Google开发 TensorFlow (例如书籍 Deep 的作者 Ian Goodfellow，他后来去了 OpenAI )。Google作为巨头公司有比高校或

者个人开发者多得多的资源投入到 TensorFlow 的研发，可以预见，TensorFlow未来的发 展将会是飞速的，可能会把大学或者个人维护的深度学习框架远远甩在身后。

###### 2.2.2 Caffe

官方网址：caffe.berkeleyvision.org/

GitHub: github. com/BVLC/caffe

Caffe 全称为 Convolutional Architecture for Fast Feature Embedding，是一个被广泛 使用的开源深度学习框架(在 TensorFlow 出现之前一直是深度学习领域 GitHub star最多 的项目)，目前由伯克利视觉学中心(Berkeley Vision and Learning Center, BVLC )进 行维护。Caffe的创始人是加州大学伯克利的 Ph.D。贾扬清，他同时也是 TensorFlow 的作 者之一，曾工作于 MSRA、NEC和 Google Brain，目前就职于 Facebook FAIR实验室。 Caffe的主要优势包括如下几点。

(1)    容易上手，网络结构都是以配置文件形式定义，不需要用代码设计网络。

(2)    训练速度快，能够训练 state-of-the-art的模型与大规模的数据。

(3)    组件模块化，可以方便地拓展到新的模型和学习任务上。

Caffe的核心概念是 Layer，每一个神经网络的模块都是一个 Layer。Layer接收输入 数据，同时经过内部计算产生输出数据。设计网络结构时，只需要把各个 Layer 拼接在一 起构成完整的网络(通过写 protobuf 配置文件定义)。比如卷积的 Layer，它的输入就是 图片的全部像素点，内部进行的操作是各种像素值与 Layer 参数的 convolution 操作，最 后输出的是所有卷积核 filter 的结果。每一个 Layer 需要定义两种运算，一种是正向(forward) 的运算，即从输入数据计算输出结果，也就是模型的预测过程；另一种是反向(backward)

的运算，从输出端的 gradient 求解相对于输入的 gradient，即反向传播算法，这部分也就 是模型的训练过程。实现新 Layer 时，需要将正向和反向两种计算过程的函数都实现，这 部分计算需要用户自己写 c++或者 CUDA (当需要运行在 GPU 时)代码，对普通用户来 说还是非常难上手的。正如它的名字 Convolutional Architecture for Fast Feature Embedding所描述的，Caffe最开始设计时的目标只针对于图像，没有考虑文本、语音或 者时间序列的数据，因此 Caffe 对卷积神经网络的支持非常好，但对时间序列 RNN.LSTM 等支持得不是特别充分。同时，基于 Layer 的模式也对 RNN 不是非常友好，定义 RNN 结构时比较麻烦。在模型结构非常复杂时，可能需要写非常冗长的配置文件才能设计好网 络，而且阅读时也比较费力。

Caffe的一大优势是拥有大量的训练好的经典模型（AlexNet、VGG、Inception ）乃至 其他 state-of-the-art （ResNet 等）的模型，收藏在它的 Model Zoo （ github.com/BVLC/ caffe/wiki/Model-Zoo ）o因为知名度较高，Caffe被广泛地应用于前沿的工业界和学术界, 许多提供源码的深度学习的论文都是使用 Caffe 来实现其模型的。在计算机视觉领域 Caffe 应用尤其多，可以用来做人脸识别、图片分类、位置检测、目标追踪等。虽然 Caffe 主要 是面向学术圈和研究者的，但它的程序运行非常稳定，代码质量比较高，所以也很适合对 稳定性要求严格的生产环境，可以算是第一个主流的工业级深度学习框架。因为 Caffe 的 底层是基于 C#的，因此可以在各种硬件环境编译并具有良好的移植性，支持 Linux, Mac和 Windows 系统，也可以编译部署到移动设备系统如 Android 和 iOS 上。和其他主 流深度学习库类似，Caffe也提供了 python语言接口 pycaffe，在接触新任务，设计新网 络时可以使用其 python 接口简化操作。不过，通常用户还是使用 Protobuf 配置文件定义 神经网络结构，再使用 command line进行训练或者预测。Caffe的配置文件是一个 JSON 类型的.prototxt文件，其中使用许多顺序连接的 Layer 来描述神经网络结构。Caffe的二进 制可执行程序会提取这些.prototxt文件并按其定义来训练神经网络。理论上，Caffe的用 户可以完全不写代码’只是定义网络结构就可以完成模型训练了。Caffe完成训练之后， 用户可以把模型文件打包制作成简单易用的接口，比如可以封装成 python 或 MATLAB 的 API。不过在.prototxt文件内部设计网络节构可能会比较受限，没有像 TensorFlow 或者 Keras那样在 python 中设计网络结构方便、自由。更重要的是，Caffe的配置文件不能用 编程的方式调整超参数，也没有提供像 Scikit-learn那样好用的 estimator 可以方便地进行 交叉验证、超参数的 Grid Search等操作。Caffe在 GPU 上训练的性能很好（使用单块 GTX 1080训练 AlexNet 时一天可以训练上百万张图片），但是目前仅支持单机多 GPU 的训练， 没有原生支持分布式的训练。庆幸的是，现在有很多第三方的支持，比如雅虎开源的 CaffeOnSpark，可以借助 Spark 的分布式框架实现 Caffe 的大规模分布式训练。

###### 2.2.3 Theano

官方网址：http://www.deepleaming.net/software/theano/

GitHub: github.com/Theano/Theano

Theano诞生于 2008 年，由蒙特利尔大学 Lisa Lab团队开发并维护，是一个高性能 的符号计算及深度学习库。因其出现时间早，可以算是这类库的始祖之一，也一度被认为 是深度学习研究和应用的重要标准之一。Theano的核心是一个数学表达式的编译器，专 门为处理大规模神经网络训练的计算而设计。它可以将用户定义的各种计算编译为高效的 底层代码，并链接各种可以加速的库，比如 BLAS、CUDA等。Theano允许用户定义、 优化和评估包含多维数组的数学表达式，它支持将计算装载到 GPU （ Theano在 GPU 上性 能不错，但是 CPU 上较差）。与 Scikit-leam —样，Theano也很好地整合了 NumPy，对 GPU的透明让 Theano 可以较为方便地进行神经网络设计，而不必直接写 CUDA 代码。 Theano的主要优势如下。

（1 ）集成 NumPy，可以直接使用 NumPy 的 ndarray, API接口学习成本低。

（2）    计算稳定性好，比如可以精准地计算输出值很小的函数（像 log（l+x））。

（3）    动态地生成 C 或者 CUDA 代码，用以编译成高效的机器代码。

因为 Theano 非常流行，有许多人为它编写了高质量的文档和教程，用户可以方便地 查找 Theano 的各种 FAQ，比如如何保存模型、如何运行模型等。不过 Theano 更多地被 当作一个研究工具，而不是当作产品来使用。虽然 Theano 支持 Linux、Mac和 Windows, 但是没有底层 c++的接口，因此模型的部署非常不方便，依赖于各种 python 库，并且不 支持各种移动设备，所以几乎没有在工业生产环境的应用。Theano在调试时输出的错误 信息非常难以看懂，因此 DEBUG 时非常痛苦。同时，Theano在生产环境使用训练好的 模型进行预测时性能比较差，因为预测通常使用服务器 CPU （生产环境服务器一•般没有 GPU，而且 GPU 预测单条样本延迟高反而不如 CPU ），但是 Theano 在 CPU 上的执行性 能比较差。

Theano在单 GPU 上执行效率不错，性能和其他框架类似。但是运算时需要将用户的 python代码转换成 CUDA 代码，再编译为二进制可执行文件，编译复杂模型的时间非常 久。此外，Theano在导入时也比较慢，而且一旦设定了选择某块 GPU，就无法切换到其 他设备。目前，Theano在 CUDA 和 cuDNN 上不支持多 GPU，只在 OpenCL 和 Theano 自己的 gpuarray 库上支持多 GPU 训练，速度暂时还比不上 CUDA 的版本，并且 Theano 目前还没有分布式的实现。不过，Theano在训练简单网络（比如很浅的 MLP）时性能可 能比 TensorFlow 好，因为全部代码都是运行时编译，不需要像 TensorFlow 那样每次 feed mini-batch数据时都得通过低效的 python 循环来实现。    •

Theano是一个完全基于 python （ c++/CUDA代码也是打包为 python 字符串）的符号 计算库。用户定义的各种运算，Theano可以自动求导，省去了完全手工写神经网络反向 传播算法的麻烦，也不需要像 Caffe 一样为 Layer 写 c++或 CUDA 代码。Theano对卷积 神经网络的支持很好，同时它的符号计算 API 支持循环控制（内部名 scan），让 RNN 的 实现非常简单并且高性能，其全面的功能也让 Theano 可以支持大部分 state-of-the-art的网 络。Theano派生出了大量基于它的深度学习库，包括一系列的上层封装，其中有大名鼎 鼎的 Keras，Keras对神经网络抽象得非常合适，以至于可以随意切换执行计算的后端（目 前同时支持 Theano 和 TensorFlow ）oKeras比较适合在探索阶段快速地尝试各种网络结构， 组件都是可插拔的模块，只需要将一个个组件（比如卷积层、激活函数等）连接起来，但 是设计新模块或者新的 Layer 就不太方便了。障 Keras 外，还有学术界非常喜爱的 Lasagne, 同样也是 Theano 的上层封装，它对神经内网络的每一层的定义都非常严谨。另外，还有 scikit-neuralnetwork、noleam这两个基于 Lasagne 的上层封装，它们将神经网络抽象为兼 容 Scikit-leam接口的 classifier 和 regressor，这样就可以方便地使用 Scikit-leam中经典的 fit、transform、score 等操作。除此之外，Theano 的上层封装库还有 blocks、deepy、pyleam2 和 Scikit-theano，可谓是一个庞大的家族。如果没有 Theano，可能根本不会出现这么多好 用的 python 深度学习库。同样，如果没有 python 科学计算的基石 NumPy，就不会有 SciPy、 Scikit-leam和 Scikit-image，可以说 Theano 就是深度学习界的 NumPy，是其他各类 python 深度学习库的基石。虽然 Theano 非常重要，但是直接使用 Theano 设计大型的神经网络还 是太烦琐了，用 Theano 实现 Google Inception就像用 NumPy 实现一个支持向量机（SVM ）□ 且不说很多用户做不到用 Theano 实现一个 Inception 网络，即使能做到但是否有必要花这 个时间呢？毕竟不是所有人都是基础科学工作者，大部分使用场景还是在工业应用中。所 以简单易用是一个很重要的特性，这也就是其他上层封装库的价值所在：不需要总是从最 基础的 tensor 粒度开始设计网络，而是从更上层的 Layer 粒度设计网络。

###### 2.2.4 Torch

官方网址：<http://torch.ch/>

GitHub: github.com/torch/torch7

Torch给自己的定位是 LuaJIT 上的一个高效的科学计算库，支持大量的机器学习算法, 同时以 GPU 上的计算优先。Torch的历史非常悠久，但真正得到发扬光大是在 Facebook 开源了其深度学习的组件之后，此后包括 Google、Twitter、NYU、IDIAP、Purdue等组织

都大量使用 Torch。Torch的目标是让设计科学计算算法变得便捷，它包含了大量的机器 学习、计算机视觉、信号处理、并行运算、图像、视频、音频、网络处理的库，同时和 Caffe类似，Torch拥有大量的训练好的深度学习模型。它可以支持设计非常复杂的神经 网络的拓扑图结构，再并行化到 CPU 和 GPU 上，在 Torch 上设计新的 Layer 是相对简单 的。它和 TensorFlow —样使用了底层 c++加上层脚本语言调用的方式，只不过 Torch 使 用的是 Lua。Lua的性能是非常优秀的（该语言经常被用来开发游戏），常见的代码可以 通过透明的 JIT 优化达到 C 的性能的 80%；在便利性上，LUa的语法也非常简单易读，拥 有漂亮和统一的结构，易于掌握，比写 C/c++简洁很多；同时，Lua拥有一个非常直接的 调用 C 程序的接口，可以简便地使用大量基于 C 的库，因为底层核心是 C 写的，因此也 可以方便地移植到各种环境。Lua支持 Linux、Mac，还支持各种嵌入式系统（iOS、Android、 FPGA等），只不过运行时还是必须有 LuaOT 的环境，所以工业生产环境的使用相对较少， 没有 Caffe 和 TensorFlow 那么多。

为什么不简单地使用 python 而是使用 LuaJIT 呢？官方给出了以下几点理由。

（1 ）LuaJIT的通用计算性能远胜于 python，而且可以直接在 LuaJ］；T中操作（^pointers。

（2）    Torch的框架，包含 Lua 是自洽的，而完全基于 python 的程序对不同平台、系统 移植性较差，依赖的外部库较多。

（3）    LuaJIT的 FFI 拓展接口非常易学，可以方便地链接其他库到 Torch 中。Torch中 还专门设计了 N-Dimension array type的对象 Tensor, Torch中的 Tensor 是一块内存的视 图，同时一块内存可能有许多视图（Tensor）指向它，这样的设计同时兼顾了性能（直接 面向内存）和便利性。同时，Torch还提供了不少相关的库，包括线性代数、卷积、傅里 叶变换、绘图和统计等，如图 2-5所示。

| torch    image    any lua packagenn    gnuplot    -torchrandomkit    cephes qt    ... |          |
| ------------------------------------------------------------ | -------- |
| TH Tensor Library                                            | luaT     |
| BLAS \| LAPACK J FFTW \| SSE                                 | Lua CAP! |

图 2-5 Torch提供的各种数据处理的库

Torch的 nn 库支持神经网络、自编码器、线性回归、卷积网络、循环神经网络等， 同时支持定制的损失函数及梯度计算。Torch因为使用了 LuaJIT，因此用户在 Lua 中做数

据预处理等操作可以随意使用循环等操作，而不必像在 python 中那样担心性能问题，也 不需要学习 python 中各种加速运算的库。不过，Lua相比 python 还不是那么主流，对大 多数用户有学习成本。Torch在 CTU 上的计算会使用 OpenMP、SSE进行优化，GPU上 使用 CUDA、cutorch、cunn、cuDNN 进行优化，同时还有 cuda-convnet 的 wrapper。Torch 有很多第三方的扩展可以支持 RNN，使得 Torch 基本支持所有主流的网络。和 Caffe 类似 的是，Torch也是主要基于 Layer 的连接来定义网络的。Torch中新的 Layer 依然需要用户 自己实现，不过定义新 Layer 和定义网络的方式很相似，非常简便，不像 Caffe 那么麻烦， 用户需要使用 c++或者 CUDA 定义新 Layer。同时，Torch属于命令式编程模式，不像 Theano、TensorFlow属于声明性编程（计算图是预定义的静态的结构），所以用它实现某 些复杂操作（比如 beam search ）比 Theano 和 TensorFlow 方便很多。

###### 2.2.5 Lasagne

官网网址：<http://lasagne.readthedocs.io/>

GitHub: github.com/Lasagne/Lasagne

Lasagne是一个基于 Theano 的轻量级的神经网络库。它支持前馈神经网络，比如卷 积网络、循环神经网络、LSTM等，以及它们的组合；支持许多优化方法，比如 Nesterov momentum、RMSprop、ADAM等；它是 Theano 的上层封装，但又不像 Keras 那样进行 了重度的封装，Keras隐藏了 Theano中所有的方法和对象，而 Lasagne 则是借用了 Theano 中很多的类，算是介于基础的 Theano 和高度抽象的 Keras 之间的一个轻度封装，简化了 操作同时支持比较底层的操作。Lasagne设计的六个原则是简洁、透明、模块化、实用、 聚焦和专注。

###### 2.2.6 Keras

官方网址：keras.io

GitHub: github.com/fchollet/keras

Keras是一个崇尚极简、高度模块化的神经网络库，使用 python 实现，并可以同时运 行在 TensorFlow 和 Theano 上。它旨在让用户进行最快速的原型实验，让想法变为结果的 这个过程最短。Theano和 TensorFlow 的计算图支持更通用的计算，而 Keras 则专精于深 度学习。Theano和 TensorFlow 更像是深度学习领域的 NumPy，而 Keras 则是这个领域的

Scikit-learn。它提供了目前为止最方便的 API，用户只需要将高级的模块拼在一起，就可 以设计神经网络，它大大降低了编程开销(code overhead)和阅读别人代码时的理解开 销(cognitive overhead)。它同时支持卷积网络和循环网络，支持级联的模型或任意的图 结构的模型(可以让某些数据跳过某些 Layer 和后面的 Layer 对接，使得创建 Inception 等复杂网络变得容易)，从 CPU 上计算切换到 GPU 加速无须任何代码的改动。因为底层 使用 Theano 或 TensorFlow，用 Keras 训练模型相比于前两者基本没有什么性能损耗(还 可以享受前两者持续开发带来的性能提升)，只是简化了编程的复杂度，节约了尝试新网 络结构的时间。可以说模型越复杂，使用 Keras 的收益就越大，尤其是在高度依赖权值共 享' 多模型组合、多任务学习等模型上，Keras表现得非常突出。Keras所有的模块都是 简洁、易懂、完全可配置、可随意插拔的，并且基本上没有任何使用限制，神经网络、损 失函数、优化器、初始化方法、激活函数和正则化等模块都是可以自由组合的。Keras也 包括绝大部分 state-of-the-art 的 Trick，包括 Adam、RMSProp、Batch Normalization、PReLU、 ELU、LeakyReLU等。同时，新的模块也很容易添加，这让 Keras 非常适合最前沿的研究。 Keras中的模型也都是在 python 中定义的，不像 Caffe、CNTK等需要额外的文件来定义 模型，这样就可以通过编程的方式调试模型结构和各种超参数。在 Keras 中，只需要几行 代码就能实现一个 MLP，或者十几行代码实现一个 AlexNet，这在其他深度学习框架中基 本是不可能完成的任务。Keras最大的问题可能是目前无法直接使用多 GPU，所以对大规 模的数据处理速度没有其他支持多 GPU 和分布式的框架快。Keras的编程模型设计和 Torch很像，但是相比 Torch, Keras构建在 python 上，有一套完整的科学计算工具链， 而 Torch 的编程语言 Lua 并没有这样一条科学计算工具链。无论从社区人数，还是活跃度 来看，Keras目前的增长速度都已经远远超过了 Torch。

###### 2.2.7 MXNet

官网网址：mxnet.io

GitHub: github.com/dmlc/mxnet

MXNet 是 DMLC ( Distributed Machine Learning Community )开发的一款开源的、 轻量级、可移植的、灵活的深度学习库，它让用户可以混合使用符号编程模式和指令式编 程模式来最大化效率和灵活性，目前已经是 AWS 官方推荐的深度学习框架 D MXNet的很 多作者者 P 是中国人，其最大的贡献组织为百度，同时很多作者来自 cxxnet、minerva和 pUrine2等深度学习项目，可谓博采众家之长。它是各个框架中率先支持多 GPU 和分布式

的，同时其分布式性能也非常高。MXNet的核心是一个动态的依赖调度器，支持自动将 计算任务并行化到多个 GPU 或分布式集群（支持 AWS、Azure、Yam等）。它上层的计算 图优化算法可以让符号计算执行得非常快，而且节约内存，开启 mirror 模式会更加省内 存，甚至可以在某些小内存 GPU 上训练其他框架因显存不够而训练不了的深度学习模型， 也可以在移动设备（Android、iOS ）上运行基于深度学习的图像识别等任务。此外，MXNet 的一个很大的优点是支持非常多的语言封装，比如 c++、python、R、Julia、Scala、Go、 MATLAB和 JavaScript 等，可谓非常全面，基本主流的脚本语言全部都支持了。在 MXNet 中构建一个网络需要的时间可能比 Keras, Torch这类高度封装的框架要长，但是比直接 用 Theano 等要快。MXNet的各级系统架构（下面为硬件及操作系统底层，逐层向上为越 来越抽象的接口）如图 2-6所示。

图 2-6 MXNet系统架构

###### 2.2.8 DIGITS

官方网址：developer.nvidia.com/digits

GitHub: github.com/NVIDIA/DIGITS

DIGITS （Deep Learning GPU Training System ）不是一个标准的深度学习库，它可 以算是一个 Caffe 的高级封装（或者 Caffe 的 Web 版培训系统）。因为封装得非常重，以 至于你不需要（也不能）在 DIGITS 中写代码，即可实现一个深度学习的图片识别模型。 在 Caffe 中，定义模型结构、预处理数据、进行训练并监控训练过程是相对比较烦琐的， DIGITS把所有这些操作都简化为在浏览器中执行。。它可以算作 Caffe 在图片分类上的一 个漂亮的用户可视化界面（GUI ），计算机视觉的研究者或者工程师可以非常方便地设计 深度学习模型、测试准确率，以及调试各种超参数。同时使用它也可以生成数据和训练结 果的可视化统计报表，甚至是网络的可视化结构图。训练好的 Caffe 模型可以被 DIGITS

直接使用，上传图片到服务器或者输入 url 即可对图片进行分类。

k 34

[www.aibbt.com](http://www.aibbt.com) 让未来触手可及



###### 2.2.9 CNTK

官方网址：cntk.ai

GitHub: github.com/Microsoft/CNTK

CNTK ( Computational Network Toolkit)是微软研究院(MSR )开源的深度学习框 架。它最早由 WarZ zAe deep learning craze的演讲人创建，目前已经发展成一个通用的、 跨平台的深度学习系统，在语音识别领域的使用尤其广泛。CNTK通过一个有向图将神经 网络描述为一系列的运算操作，这个有向图中子节点代表输入或网络参数，其他节点代表 各种矩阵运算。CNTK支持各种前馈网络，包括 MLP、CNN、RNN、LSTM、 Sequence-to-Sequence模型等，也支持自动求解梯度。CNTK有丰富的细粒度的神经网络 组件，使得用户不需要写底层的 c++或 CUDA，就能通过组合这些组件设计新的复杂的 Layer。CNTK拥有产品级的代码质量，支持多机、多 GPU 的分布式训练。

CNTK设计是性能导向的，在 CPU、单 GPU、多 GPU，以及 GPU 集群上都有非常 优异的表现。同时微软最近推出的 1-bit compression技术大大降低了通信代价，让大规模 并行训练拥有了很高的效率。CNTK同时宣称拥有很高的灵活度，它和 Caffe—样通过配 置文件定义网络结构，再通过命令行程序执行训练，支持构建任意的计算图，支持 AdaGrad、 RmsProp等优化方法。它的另一个重要特性就是拓展性，CNTK除了内置的大量运算核， 还允许用户定义他们自己的计算节点，支持高度的定制化。CNTK在 2016 年 9 月发布了 对强化学习的支持，同时，除了通过写配置文件的方式定义网络结构，CNTK还将支持其 他语言的绑定，包括 python、c++和 C#，这样用户就可以用编程的方式设计网络结构。 CNTK与 Caffe—样也基于 c++并且跨平台，大部分情况下，它的部署非常简单。PC上 支持 Linux、Mac和 Windows，但是它目前不支持 ARM 架构，限制了其在移动'设备上的 发挥。图 2-7所示为 CNTK 目前的总体架构图。

Evaluate

Compute Gradient

图 2-7 CNTK的总体架构图

CNTK原生支持多 GPU 和分布式，从官网公布的对比评测来看，性能非常不错。在 多 GPU 方面，CNTK相对于其他的深度学习库表现得更突出，它实现了 1-bit SGD和自 适应的 mini-batching。图 2-8所示为 CNTK 官网公布的在 2015 年 12 月的各个框架的性能 对比。在当时，CNTK是唯一支持单机 8 块 GPU 的框架，并且在分布式系统中可以超越 8块 GPU 的性能。

Speed Comparison (Frames/Second, The Higher the Better)

图 2-8 CNTK与各个框架的性能对比

###### 2.2.10 Deeplearning4J

官方网址：http://deepleaming4j.org/

GitHub: github.com/deepleaming4j/deepleaming4j

Deepleaming4J (简称 DL4J )是一个基于 Java 和 Scala 的开源的分布式深度学习库， 由 Skymmd 于 2014 年 6 月发布，其核心目标是创建一个即插即用的解决方案原型。埃森 哲、雪弗兰、博斯咨询和 IBM 等都是 DL4J 的客户 dDL4J 拥有一个多用途的 n-dimensional array的类，可以方便地对数据进行各种操作；拥有多种后端计算核心，用以支持 CPU 及 GPU加速，在图像识别等训练任务上的性能与 Caffe 相当；可以与 Hadoop 及 Spark 自动 整合，同时可以方便地在现有集群(包括但不限于 AWS, Azure等)上进行扩展，同时 DL4J的并行化是根据集群的节点和连接自动优化，不像其他深度学习库那样可能需要用 户手动调整。DL4J选择 Java 作为其主要语言的原因是，目前基于 Java 的分布式计算、 云计算、大数据的生态非常庞大。用户可能拥有大量的基于 Hado 叩和 Spark 的集群，因 此在这类集群上搭建深度学习平台的需求便很容易被 DL4J 满足。同时 JVM 的生态圈内 还有数不胜数的 Library 的支持，而 DL4J 也创建了 ND4J，可以说是 JVM 中的 NumPy, 支持大规模的矩阵运算。此外，DL4J还有商业版的支持，付费用户在出现问题时可以通 过电话咨询寻求支持。

###### 2.2.11 Chainer

官方网址：chainer.org

GitHub: github.com/pfhet/chainer

Chainer是由日本公司 Preferred Networks于 2015 年 6 月发布的深度学习框架。Chainer 对自己的特性描述如下。

(1)    Powerful：支持 CUDA 计算，只需要几行代码就可以使用 GPU 加速，同时只需 少许改动就可以运行在多 GPU 上。

(2)    Flexible：支持多种前馈神经网络，包括卷积网络、循环网络、递归网络，支持 运行中动态定义的网络(Define-by-Run )。

(3 ) Intuitive:前馈计算可以引入 python 的各种控制流，同时反向传播时不受干扰， 简化了调试错误的难度。

绝大多数的深度学习框架是基于“Defme-and-Run”的，也就是说，需要首先定义一 个网络，再向网络中 feed 数据(mini-batch)。因为网络是预先静态定义的，所有的控制 逻辑都需要以 data 的形式插入网络中，包括像 Caffe 那样定义好网络结构文件，或者像 Theano、Torch、TensorFlow等使用编程语言定义网络。而 Chainer 则相反，网络是在实际

运行中定义的，Chainer存储历史运行的计算结果，而不是网络的结构逻辑，这样就可以 方便地使用 python 中的控制流，所以无须其他工作就可以直接在网络中使用条件控制和 循环。

###### 2.2.12 Leaf

官方网址：autumnai.com/leaf/book

GitHub: github. com/autumnai/leaf

Leaf是一个基于 Rust 语言的直观的跨平台的深度学习乃至机器智能框架，它拥有一 个清晰的架构，除了同属 Autumn AI的底层计算库 Collenchyma, Leaf没有其他依赖库。 它易于维护和使用，并且拥有非常高的性能。Leaf自身宣传的特点是为 Hackers 定制的， 这里的 Hackers 是指希望用最短的时间和最少的精力实现机器学习算法的技术极客。它的 可移植性非常好，可以运行在 CPU、GPU和 FPGA 等设备上，可以支持有任何操作系统 的 PC、服务器，甚至是没有操作系统的嵌入式设备，并且同时支持 OpenCL 和 CUDA。 Leaf是 Autumn AI计划的一个重要组件，后者的目标是让人工智能算法的效率提高 100 倍。凭借其优秀的设计，Leaf可以用来创建各种独立的模块，比如深度强化学习、可视 化监控、网络部署、自动化预处理和大规模产品部署等。

Leaf拥有最简单的 API，希望可以最简化用户需要掌握的技术栈。虽然才刚诞生不久， Leaf就已经跻身最快的深度学习框架之一了。图 2-9所示为 Leaf 官网公布的各个框架在 单 GPU 上训练 VGG 网络的计算时间（越小越好）的对比（这是和早期的 TensorFlow 对 比：最新版的 TensorFlow 性能已经非常好了）。

图 2-9 Leaf和各深度学习框架的性能对比（深色为 forawrd，浅色为 backward ）

###### 2.2.13 DSSTNE

GitHub: github.com/amznlabs/amazon-dsstne

DSSTNE （ Deep Scalable Sparse Tensor Network Engine ）是亚马逊开源的稀疏神经 网络框架，在训练非常稀疏的数据时具有很大的优势。DSSTNE目前只支持全连接的神 经网络，不支持卷积网络等。和 Caffe 类似，它也是通过写一个 JSON 类型的文件定义模 型结构，但是支持非常大的 Layer （输入和输出节点都非常多）；在激活函数、初始化方 式及优化器方面基本者岐持了 state-of-the-art的方法，比较全面；支持大规模分布式的 GPU 训练，不像其他框架一样主要依赖数据并行，DSSTNE支持自动的模型并行（使用数据 并行需要在训练速度和模型准确度上做一定的 trade-off，模型并行没有这个问题）。

在处理特征非常多（上亿维）的稀疏训练数据时（经常在推荐、广告、自然语言处理 任务中出现），即使一个简单的 3 个隐层的 MLP （Multi-Layer Perceptron）也会变成一个 有非常多参数的模型（可能高达上万亿）。以传统的稠密矩阵的方式训练方法很难处理这 么多的模型参数，更不必提超大规模的数据量，而 DSSTNE 有整套的针对稀疏数据的优 化，率先实现了对超大稀疏数据训练的支持，同时在性能上做了非常大的改进。

在 DSSTNE 官方公布的测试中，DSSTNE在 MovieLens 的稀疏数据上，在单 M40 GPU 上取得了比 TensorFlow 快 14.8倍的性能提升（注意是和老版的 TensorFlow 比较），如图 2-10所示。一方面是因为 DSSTNE 对稀疏数据的优化；另一方面是 TensorFlow 在数据传 输到 GPU 上时花费了大量时间，而 DSSTNE 则优化了数据在 GPU 内的保留；同时 DSSTNE 还拥有自动模型并行功能，而 TensorFlow 中则需要手动优化，没有自动支持。

MovieLens 20M Sparse Data Epoch Times(s)

lower is benei

![img](06TensorFlow9e18_c4875a088c74095baibbt-36.jpg)



■    M40

■    KSO

large)



.KS20

图 2-10 DSSTNE在稀疏数据上与 TensorFlow 的性能对比
