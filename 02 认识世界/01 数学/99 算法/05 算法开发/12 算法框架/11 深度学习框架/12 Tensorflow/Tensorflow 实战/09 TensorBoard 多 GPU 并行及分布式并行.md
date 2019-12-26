---
title: 09 TensorBoard 多 GPU 并行及分布式并行
toc: true
date: 2018-06-26 20:29:21
---
![img](06TensorFlow9e18_c4875a088c74095baibbt-113.jpg)



多 GPU



#### 并行及分布式并行

##### 9.1    Tensor 巳 oard

TensorBoard是 TensorFlow 官方推出的可视化工具，如图 9-1所示，它可以将模型训 练过程中的各种汇总数据展示出来，包括标量（Scalars ）、图片（Images ）、音频（Audio \ 计算图（Graphs ）、数据分布（Distributions ）、直方陳 Histograms ）和嵌入向量（Embeddings ）。 我们在使用 TensorFlow 训练大型深度学习神经网络时，中间的计算过程可能非常复杂， 因此为了理解、调试和优化我们设计的网络，可以使用 TensorBoard 观察训练过程中的各 种可视化数据。如果要使用 TensorBoard 展示数据，我们需要在执行 TensorFlow 计'算图的 过程中，将各种类型的数据汇总并记录到日志文件中。然后使用 TensorBoard 读取这些日 志文件，解析数据并生成数据可视化的 Web 页面，让我们可以在浏览器中观察各种汇总 数据。下面我们将通过一个简单的 MNIST 手写数字识别的例子，讲解各种类型数据的汇 总和展示的方法。

Write a regex to creaR a tag group    X

accuracy—，

cross_entropy_1



[| Spirt on underscores



I j Data download links

Toohip sorting method: default



Smoothing



Horizontal Axis



cross_entropy_l

ax

2^

20；

CCCO 2CC.C -<33.0 6000 320 0 ：.0Xk



dropout

图 9-1 TensorBoard-基于 Web 的 TensorFlow 数据可视化工具

我们首先载入 TensorFlow，并设置训练的最大步数为 1000，学习速率为 0.001, dropout 的保留比率为 0.9。同时，设置 MNIST 数据的下载地址 data_dir和汇总数据的日志存放路 径 log_dirQ这里的日志路径 log_dir非常重要，会存放所有汇总数据供 TensorBoard 展示。 本节代码主要来自 TensorFlow 的开源实现 67。

import tensorflow as tf

from tensorflow.examples.tutorials.mnist import input_data

max_steps=1000

learning_rate=0.001

dropout=0.9

data_dip=*/tmp/tensor千 low/mnist/input_data1

log_dir='/tmp/tensorflow/mnist/logs/mnist_with_summaries1

我们使用 input_data.read_data_sets下载 MNIST 数据，并创建 TensorFlow 的默认 Session。

mnist = input_data.read_data_sets(data_dirjOne_hot=True)

sess = tf.InteractiveSession()

为了在 TensorBoard 中展示节点名称，我们设计网络时会经常使用 with tf.name_scope 限定命名空间，在这个 with 下的所有节点都会被自动命名为 input/xxx这样的格式。下面 定义输入 x 和 y 的 placeholder，并将输入的一维数据变形为 28x28 的图片储存到另一个

tensor，这样就可以使用 tf.summary.image将图片数据汇总给 TensorBoard 展示了。 with tf.name一 scope(1 input’)：

x = tf.placeholder(tf.float32? [None, 784]j name='x-input') y_ = tf .placeholder (tf.float32 [None, 10] namely-input')

with tf.name_scope('input_reshape'):

image_shaped_input = tf.reshape(x_, [-1} 2S} 28} 1]) tf .summary, image ('input1 image_shaped_input^ 10)

同时，定义神经网络模型参数的初始化方法，权重依然使用我们常用的 truncated_normal进行初始化，偏置则赋值为 0.10

def weight_variable(shape):

initial = tf.truncated_normal(shapestddev=0.1) return tf.Variable(initial)

def bias_variable(shape):

initial = tf.constant(0.1, shape=shape) return tf.Variable(initial)

再定义对 Variable 变量的数据汇总函数，我们计算出 Variable 的 mean、stddev、max 和 min ，对这些标量数据使用 tf.summary.scalar进行记录和汇总。同时，使用 tf.summary.histogram直接记录变量 var 的直方图数据。

def variable_summaries(var):

with tf.name_scope(1 summaries'):

mean = tf.reduce_mean(var) tf.summary.scalar('mean*mean) with tf.name_scope(1stddev*):

stddev = tf.sqrt(tf.reduce_mean(tf.square(var - mean))) tf. summary .scalar (* stddev、stddev) tf. summary, scalar (’max、tf. reduce_max(var)) tf.summary.scalar('min、 tf.reduce_min(var)) tf. summary. histogram(' histogram、var)

然后我们设计一个 MLP 多层神经网络来训练数据，在每一层中都会对模型参数进行 数据汇总。因此，我们定义创建一层神经网络并进行数据汇总的函数 nnjayer。这个函数 的输入参数有输入数据 input_tensor、输入的维度 input_dim、输出的维度 output_dim和层 名称 layer_name，激活函数 act 则默认使用 ReLU。在函数内，先是初始化这层神经网络 的权重和偏重，并使用前面定义的 variable_summaries对 variable 进行数据汇总。然后对 输入做矩阵乘法并加偏置，再将未进行激活的结果使用 tf.summary.histogram统计直方图。 同时，在使用激活函数后，再使用 tf.summary.histogram统计一次。

def nn_layer(input_tensorj input_dim_, output_dimj layer_name, act=tf.nn.relu):

with tf.name_scope(layer_name): with tf.name_scope(* weights1):

weights = weight_variable([input一 dim, output_dim]) variable_summaries(weights)

with tf.name_scope(* biases *):

biases = bias_variable([output_dim]) variable_summaries(biases)

with tf.name_scope(*Wx_plus_b'):

preactivate = tf.matmul(input_tensorj weights) + biases tf.summary.histogram(1pre_activations'preactivate)

activations = act(preactivate^ name='activation’) tf.summary.histogram('activations'activations) return activations

我们使用刚刚定义好的 nnjayer 创建一层神经网络，输入维度是图片的尺寸 ( 784=28x28 )，输出的维度是隐藏节点数 500。再创建一个 Dropout 层，•并使用 tf.summary.scalar记录 keep_prob。然后再使用 nnjayer 定义神经网络的输出层，其输入维 度为上一层的隐含节点数 500，输出维度为类别数 10，同时激活函数为全等映射 identity, 即暂不使用 Softmax，在后面会处理。

hiddenl = nn_layer(x, 784, 500, ’layerl’)

with tf.name_scope('dropout'):

keep_prob = tf.placeholder(tf.float32)

tf.summary.scalar(*dropout_keep_probability'7 keep_prob) dropped = tf.nn.dropout(hiddenl, keep_prob)

y = nn_layer(dropped500^ 10, 'layer2'j act=tf.identity)

这里使用 tf.nn.softmax_cross_entropy_with_logits()对前面输出层的结果进行 Softmax 处理并计算交叉熵损失 cross_entropy。我们计算平均的损失，并使用 tf.summary.scalar进 行统计汇总。

with tf.name_scope(* cross_entropy*):

diff = tf.nn.softmax_cross_entropy_with_logits(logits=ylabels=y_) with tf.name_scope('total'):

cross_entropy = tf.reduce_mean(diff) tf.summary.scalar(*cross_entropy*』cross_entropy)

下面使用 Adma 优化器对损失进行优化，同时统计预测正确的样本数并计算正确率 accuray，再使用 tf.summary.scalar 对 accuracy 进行统计汇总。 with tf.name_scope('train'):

train_step = tf.train.AdamOptimizer(learning_rate).minimize(cross_entropy) with tf.name_scope('accuracy'):

with tf.name_scope('correct_prediction'):

correct_prediction = tf.equal(tf.argmax(y, 1)tf.argmax(y_, 1))

with tf.name_scope('accuracy*):

accuracy = tf.reduce_mean(tf.cast(correct_prediction^ tf.float32)) tf. summary. scalar( 'accuracy、accuracy)

因为我们之前定义了非常多的 tf.summary的汇总操作，逐一执行这些操作太麻烦， 所以这里使用 tf.summary.merger_all()直接获取所有汇总操作，以便后面执行。然后，定义 两个 tf.summary.FileWriter (文件记录器)在不同的子目录，分别用来存放训练和测试的 日志数据。同时，将 Session 的计算图 sess.graph加入训练过程的记录器，这样在 TensorBoard 的 GRAPHS 窗口中就能展示整个计算图的可视化效果。最后使用 tf.global_variables_initializer().run()初始化全部变量。

merged = tf.summary.merge_all()    -

train_writer = tf.summary.FileWriter(log_dir + '/train'sess.graph) test_writer = tf.summary.FileWriter(log_dir + '/test')

tf.global_variables_initializer().run()

接下来定义 feed_dict的损失函数。该函数先判断训练标记，如果训练标记为 True, 则从 mnist.train中获取一个 batch 的样本，并设置 dropout 值；如果训练标记为 False，则 获取测试数据，并设置 keep」)rOb为 1，即等于没有 dropout 效果。

def feed_dict(train): if train:

xsj ys = mnist.train.next_batch(100) k = dropout

else:

xSj ys = mnist.test.imagesmnist.test.labels k = 1.0

return {x: xs_, y_:    keep_prob: k}

最后一步，实际执行具体的训练、测试及日志记录的操作。首先使用 tf.train.Saver() 创建模型的保存器。然后进入训练的循环中，每隔 10 步执行一次 merged (数据汇总)、 accuracy (求测试集上的预测准确率)操作，并使用 test_writer.add_sumamry将汇总结果 summary和循环步数 i 写入日志文件；同时每隔 100 步，使用 tf.RunOptions定义 TensorFlow 运行选项，其中设置 tracejevel 为 FULL_TRACE，并使用 tf.RunMetadata()定义 TensorFlow 运行的元信息，这样可以记录训练时运算时间和内存占用等方面的信息。再执行 merged 数据汇总操作和 train_step训练操作，将汇总结果 summary 和训练元信息 run_metadata添 加到 train_writer。平时，则只执行 merged 操作和 train_step操作，并添加 summary 到 train_writer。所有训练全部结束后，关闭 train_writer和 test_writer。

saver = tf.train.Saver()    .    .    .

for i in range(max_steps):

if i % 10 == 0:

summary^ acc = sess.run([merged^ accuracy], feed_dict=feed_dict(False))

test_writer.add_summary(summaryJ i)

print("Accuracy at step %s: %s* % (ij acc))

else:

if i % 100 == 99:

run_options = tf.RunOptions(trace_level=tf.RunOptions.FULL_TRACE)

pun_metadata = tf.RunMetadata()

summary, _ = sess.run([mergedj train一 step], feed_dict=feed_dict(True), options=run_optionSj run_metadata=run_metadata)

train_writer.add_run_metadata(run_metadataJ 'step%03d' % i) train_writer.add_summary(summaryj i) saver.save(sessj log_dir+"/model.ckpt"J i) print( 'Adding run metadata for、i)

else:

summaryj 一 = sess.run([merged, train_step]j feed_dict=feed_dict(True)) train_writer.add_summary(summary? i)

train_writer.close()

test_writer.close()

之后切换到 Linux 命令行下，。执行 TensorBoard 程序，并通过-logdir指定 TensorFlow 日志路径，然后 TensorBoard 就可以自动生成所有汇总数据可视化的结果了。 tensorboard --logdir=/tmp/tensorflow/mnist/logs/mnist_with_summaries

执行上面的命令后，出现一条提示信息，复制其中的网址到浏览器，就可以看到数据 可视化的图表了。

Starting TensorBoard b'39* on port 6006

(You can navigate to <http://192.168.233.101:6006>)

首先打开标量 SCALARS 的窗口，并单击打开 accuracy 的图表，如图 9-2所示。其中 可以看到两条曲线，分别是 train 和 test 中 accuray 随训练士数变化的趋势。我们可以调整 Smoothing参数，控制对曲线的平滑处理，数值越小越接近实际值，但波动较大；数值越 大则曲线越平缓。单击图表左下方的按钮，’可以放大这个图片，单击它右边的按鈕则可以 调整坐标轴的范围，以便更清楚地展示。

切换到图像 IMAGES 窗口，如图 9-3所示，可以看到 MNIST 数据集中的图片。不只 是原始数据，所有在 tf.sumamry.imageO中汇总的图片数据都可以在这里看到，包括进行 了各种光学畸变后的图片，或是神经网络的中间节点的输出。

C ① 192.168.233.101:6S<?G

Write a regex to create u tag group X

i~~) Split on underscores

门 Data download links

Tooltip sorting method: default -

accuracy」

accuracy.!



Smoothing

-• 0.6

Horizontal Axis

200.G ftC0.0 6CC.C SCC-G : COCK



Runs



Vff.ie & reoex io fiiic* roni



cross_entropy_1

dropout

layerl

Iayer2



图 9-2 TensorBoard SCALARS变量展示效果



图 9-3 TensorBoard IMAGES图片展示效果

进入计算图 GRAPHS 窗口，可以看到整个 TensorFlow 计算图的结构，如图 9-4所示。 这里展示了网络 forward 的 inference 的流程，以及 backward 训练更新参数的流程。我们 在代码中创建的只有 forward 正向过程：input -> layerl dropout -> layer2 cross_entropy、accuracy的，而训练中 backward 的求解梯度、更新参数等操作是 TensorFlow 帮我们自动创建的。图中实线代表数据上的依赖关系，虚线代表控制条件上的依赖关系。 单击某个节点的窗口，可以查看它的属性、输入及输出，并且可以看到输出 tensor 的尺寸。

我们也可以单击节点右上角的“+”号按钮，展开这个 node 的内部细节。例如，单击 layer2 可以看到内部的 weights、biases，矩阵乘法操作、向量加法操作，以及激活函数计算的操 作，这些操作都归属于 tf.name_scope('layer2')这个命名空间(name scope)。所有在一个 命名空间中的节点都会被折叠在一起，在设计网络时，我们要尽可能精细地使用命名空间 对节点名称进彳亍规范，这样会展示出更清晰的结构。同时，在 TensorBoard 中，我们可以 右键单击一个节点并选择删除它，这不会真的在计算图中中删除它，但是可以简化我们的 视图，以便更好地观察网络结构。我们也可以切换配色风格，一种是基于结构的，相同的 结构的节点有一样的颜色；另一种是基于运算硬件的，在同一个运算硬件上的节点有一样 的颜色。同时，我们可以单击左边面板的 Session runs，选择我们之前记录过 run_metadata 的训练元信息，这样可以查看某轮迭代计算的时间消耗、内存占用等情况。

Main Graph



![img](06TensorFlow9e18_c4875a088c74095baibbt-120.jpg)



Fit lo screen

Download PNG

Run train    ■

⑴

Session _

runs(10)

Upload

Trace inputs S>

Color ® Stiuctyre O Device

coiws same sobsvucuxe (    ) iKwou* sobstnxtixe

pcsvffA



i inpuUesh...；

K

Graph r pxpa；xj»b»f) Nanwtpac**

OfiNod*

UnconrMjcted tvtts* Canmacwi tvrtts*

| Q    CoflM»nt                                      | 1 input 厂                       |
| -------------------------------------------------- | -------------------------------- |
| B5    SutrmaryDataflow «dQ«Cont'ol dependency edge |                                  |
| 图 9-4                                              | TensorBoard GRAPHS计算图展示效果 |

切换到 DISTRIBUTIONS 窗口，如图 9-5所示，可以查看之前记录的各个神经网络层 输出的分布，包括在激活函数前的结果及在激活函数后的结果。这样能观察到神经网络节 点的输出是否有效，会不会存在过多的被屏蔽的节点(dead neurons)。

![img](06TensorFlow9e18_c4875a088c74095baibbt-122.jpg)



也可以将 DISTRIBUTIONS 的图示结构转为直方图的形式。单击 HISTOGRAMS 窗 口，如图 9-6所示，可以将每一步训练后的神经网络层的输出的分布以直方图的形式展示 出来。

Vi.-iic a rcgtx io crcste a tog group    X

门 Split on underscores



layer1/Wx_piusJj/pre_»ctivailonB



Histogram Mode



OVERLAY



Offs« Time Axis

Runs



a    U> fillet f 曲 s

S C W*"



layer 1 /Wx_plus_b/pfe_activaiions



A

\- -t-.

-H 4    ?    ? t



layer Vbieses/summaries/hisiogram



图 9-6 TensorBoard HISTOGRAMS直方图的展示效果

单击 EMBEDDINGS 窗口，如图 9-7所示，可以看到降维后的嵌入向量的可视化效果， 这是 TensorBoard 中的 Embedding Projector功能。虽然在 MNIST 数据的训练中是没有嵌 入向量的，但是只要我们使用 tf.save.Saver保存了整个模型，就可以让 TensorBoard 自动

对模型中所有二维的 Variable 进行可视化（TensorFlow中只有 Variable 可以被保存，而 Tensor不可以，因此我们需要把想可视化的 Tensor 转为 Variable ）。我们可以选择 T-SNE 或者 PCA 等算法对数据的列（特征）进行降维，并在 3D 或者 2D 的坐标中进行可视化展 示。如果我们的模型是 Word2Vec 计算或 Language Model，那么 TensorBoard 的 EMEBEDDINGS可视化功能会变得非常有用。

DATA

•iO 》B ■ Points: 784 Dimension: 500

A

test



layerl/weighis/VariaWc

JJ3 Spbvieizcdala ©

Occipunt    ;t6o4i.-eT>nn.

with    model

:PCA



Component »1    - ComiXKwn''

Component #3    , D

PCA is epprox-fnasc. &

![img](06TensorFlow9e18_c4875a088c74095baibbt-130.jpg)



![img](06TensorFlow9e18_c4875a088c74095baibbt-131.jpg)



图 9-7 TensorBoard EMBEDDINGS向量嵌入展示效果

##### 9.2多 GPU 并行

TensorFlow中的并行主要分为模型并行和数据并行。模型并行需要根据不同模型设 计不同的并行方式，其主要原理是将模型中不同计算节点放在不同硬件资源上运算。比较 通用的且能简便地实现大规模并行的方式是数据并行，其思路我们在第 1 章讲解过，是同 时使用多个硬件资源来计算不同 batch 的数据的梯度，然后汇总梯度进行全局的参数更新。

数据并行几乎适用于所有深度学习模型，我们总是可以利用多块 GPU 同时训练多个 batch数据，运行在每块 GPU 上的模型都基于同一个神经网络，网络结构完全一样，并且 共享模型参数。本节我们主要讲解同步的数据并行，即等待所有 GPU 都计算完一个 batch 数据的梯度后，再统一将多个梯度合在一起，并更新共享的模型参数，这种方法类似于使 用了一个较大的 batch。使用数据并行时，GPU的型号、速度最好一致，这样效率最高。

而异步的数据并行，则不等待所有 GPU 都完成一次训练，而是哪个 GPU 完成了训练， 就立即将梯度更新到共享的模型参数中。通常来说，同步的数据并行比异步的模式收敛速 度更快，模型的精度更高。

下面就讲解使用多 GPU 的同步数据并彳于来训练卷积神经网络的例子，使用的数据集 为 CIFAR-10。首先载入各种依赖的库，其中包括 TensorFlow Models中 cifarlO 的类（我 们在第 5 章下载了这个库，现在只要确保 python 执行路径在 models/tutorials/image/cifar 10 下即可），它可以下载 CIFAR-10数据并进行一些数据预处理。本节我们不再重头设计一 个 CNN，而是直接使用一个现成的 CNN，并侧重于讲解如何使用数据并行训练这个 CNN。 本节代码主要来自 TensorFlow 的开源实现 6S。

import os.path

import re

import time

import numpy as np

import tensorflow as tf

import cifarl0

我们设置 batch 大小为 128，最大步数为 100 万步（中间可以随时停止，模型定期保 存），使用的 GPU 数量为 4 （取决于当前机器上有多少可用显卡）。

batch_size=128

max_steps=1000000

num_gpus=4

然后定义计算损失的函数 tower_loss。我们先使用 cifarl0.distorted_inputs产生数据增 强后的 images 和 labels，并调用 cifarlO.inference生成卷积网络（注意，我们需要为每个 GPU生成单独的网络，这些网络的结构完全一致，并且共享模型参数）。通过 cifarlO.inference生成的卷积网络和 5.3节中的卷积网络一致，读者若想了解网络结构的具 体细节，可参考 5.3节中的内容。然后，根据卷积网络和 labels，调用 cifarlO.loss计算損 失函数（这里不直接返回 loss，而是储存到 collection 中），并用 tf.get_collection（'losses',scope） 获取当前这个 GPU 上的 loss （通过 scope 限定了范围），再使用 tf.add_n将所有损失叠加 到一起得到 total_loss。最后返回 totaljoss 作为函数结果。

def towep_loss（scope）:

images, labels = cifarlO.distorted_inputs（）

logits = cifarl0.inference(images)

_ = cifarl0.loss(logitslabels) losses = tf.get_collection('losses'j scope) total_loss = tf .addjXlosseSj name='total_loss *) return total_loss

下面定义函数 average_gradients，它负责将不同 GPU 计算出的梯度进行合成。函数 的输入参数 tower_grads是梯度的双层列表，外层列表是不同 GPU 计算得到的梯度，内层 列表是某个 GPU 内计算的不同 Variable 对应的梯度，最内层元素为(grads, variable)，即 tower_grads的基本兀素为二元组(梯度，变量)。其具体形式为［［(gradO_gpuO，varO_gpuO), (gradl_gpuO, varl_gpuO)，...］，［ (gradO_gpul, varO_gpul), (gradl_gpul, varl一 gpul)”..］，...］ o 我们先创建平均梯度的列表 aVerage_gradS，它负责将梯度在不同 GI>U间进行平均。然后 使用 zip(*tower_grads)将这个双层列表转置，变成［［(gradO_gpuO，var0_gpu0)3 (gradO_gpul, varO_gpul)，…］，［(gradl_gpuO, varl_gpuO), (gradl_gpul, varl_gpul)，…］，…］的形式，然后使 用循环遍历其元素。每个循环中获取的元素 grad_and_vars，是同一个 Variable 的梯度在不 同 GPU 上的计算结果，即［(gradO_gpuO, varO_gpuO), (gradO_gpul, varO_gpul)，...］。对同一 个 Variable 的梯度在不同 GRU 计算出的副本，需要计算其梯度的均值，如果这个梯度是 一个 7V 维的向量，需要在每个维度上都进行平均。我们先使用 tf.expand_dims给这些梯度 添加一个冗余的维度 0，然后把这些梯度放到列表 grad 中，接着使用 tf.concat将它们在维 度 0 上合并，最后使用 tf.reduce_mean针对维度 0 上求平均，即将其他维度全部平均。最 后将平均后的梯度跟 Variable 组合得到原有的二元组(梯度，变量)格式，并添加到列表 average_grads中。当所有梯度都求完均值后，我们返回 average_grads。

def average_gradients(tower_grads): average_grads =［］

for grad_and_vars in zip(*tower_grads): grads =［］

for gj _ in grad_and_vars:

expanded_g = tf,expand_dims(gJ 0) grads.append(expanded_g)

grad = tf,concat(grads^ 0) grad = tf.reduce_mean(grad^ 0)

v = grad_and_vars[0][1] grad_and_var = (grad^ v) average_grads.append(grad_and_var)

return average_grads

下面定义训练的函数。先设置默认的计算设备为 CPU，用来进行一些简单的计算。 然后使用 global_step记录全局训练的步数，并计算一个 epoch 对应的 batch 数，以及学习 速率衰减需要的步数 decay_stepso我们使用 tf.train.exponential_decay创建随训练步数衰 减的学习速率，这里第 1 个参数为初始学习速率，第 2 个参数为全局训练的步数，第 3 个参数为每次衰减需要的步数，第 4 个参数为衰减率，staircase设为 True 代表是阶梯式 的衰减。然后设置优化算法为 GradientDescent，并传入随步数衰减的学习速率。 def train():

with tf.Graph().as_default()^ tf.device('/cpu:0'): global_step = tf .get_variable( *global_step', []_,

initializer=tf.constant_initializer(0) trainable=False)

num_batches_per_epoch = cifarlO.NUM_EXAMPLES_PER_EPOCH_FOR__TRAIN / \ batch_size

decay一 steps = int(num_batches_per一 epoch * cifarl0.NUM_EPOCHS_PER_DECAY)

lr = tf.train.exponential_decay(cifarl0.INITIAL__LEARNIN6_RATEj global_stepj

decay一 steps    .

cifarlO.LEARNING_RATE_DECAY_FACTORJ staircase=True)

opt = tf.train.GradientDescentOptimizer(lr)

我们定义储存各 GPU 计算结果的列表 towerjads。然后创建一个循环，循环次数为 GPU数量，在每一个循环内，使用 tf.device限定使用第几个 GPU，如 gpuO、gpul，然后 使用 tf.name_scope将命名空间定义为 tower_0、tower_l的形式。对每一个 GPU，使用前

面定义好的函数 tower_loss 获取其损失，然后调用 tf.get_variable_scope().reuse_variables() 重用参数，让所有 GPU 共用一个模型及完全相同的参数。再使用 opt.compute_gradients(loss) 计算单个 GPU 的梯度，并将求得的梯度添加到梯度列表 towei^grads。最后使用前面写好 的函数 average_gradients计算平均梯度，并使用 opt.apply_gradients更新模型参数。这样 就完成了多 GPU 的同步训练和参数更新。

tower_grads =[]

for i in range(num_gpus):

with tf.device(1/gpu:%d* % i):

with tf.name_scope('%s_%d* % (cifarl0.TOWER_NAMEi)) as scope: loss = tower_loss(scope) tf.get_variable_scope().reuse_variables() grads = opt.compute_gradients(loss) tower_grads.append(grads)

grads = average_gradients(tower_grads)

apply_gradient_op = opt.apply_gradients(grads., global__step=global_step)

我们创建模型的保存器 saver，将 Session 的 allow_softjplacement参数设置为 True(有 些操作只能在 CPU 进行，不使用 soft_placement可能导致运行出错)，初始化全部参数， 并调用 tf.train.start_queue_runners()准备好大量的数据增强后的训练样本，防止后面的训练 被阻塞在生成样本上。

saver = tf.train.Saver(tf.all_variables()) init = tf.global_variables_initializer()

sess = tf.Session(config=tf.ConfigProto(allow_soft_placement=True)) sess.run(init)

tf.train.start_queue_runners(sess=sess)

下面进入训练的循环，最大迭代次数为 maX_stepS。在每一步中执行一次更新梯度的 操作 apply_gradient_op (即一次训练操作)和计算损失的操作 loss，同时使用 time.time() 记录耗时。每隔 10 步，展示一次当前 batch 的 loss，以及每秒钟可训练的样本数和每个 batch训练所需要花费的时间。每隔 1000 步，使用 Saver 保存整个模型文件。

for step in range(max_steps):

start_time = time.time()

loss_value = sess.run([apply_gradient_op?

loss])



duration = time.time() - start_time if step % 10 == 0:

num一 examples_per_step = batch_size * num_gpus examples_per_sec = num_examples_per_step / duration sec_per_batch = duration / num_gpus

format_str = ('step %d， loss = %.2f (%.lf examples/sec; %.3f ’

'sec/batch)1)

print (format_str % (step^ loss_value3 examples_per_sec4 sec_per_batch))

if step % 1000 == 0 or (step + 1) == max_steps:

saver.save(sess' /tmp/cifarl0_train/model.ckpt*, global一 step=step)

我们将主函数后全部定义完后，使用 cifarl0.maybe_download_and_extract()下载完整 的 CIFAR-10数据，并调用 trainO 函数开始训练。

cifarl0,maybe_download_and_extract()

train()

下面展示的结果即为训练过程中显示的日志，loss从最开始的 4 点几，到第 70 万步 时，大致降到了 0.07。我们的训练速度很快，平均每个 batch 的耗时仅为 0.021s，平均每 秒可以训练 6000 个样本，差不多正好是单 GPU 的 4 倍。因此在单机多 GPU 的情况下, 使用 TensorFlow 实现的数据并行效率是非常高的。

step 729470, loss = 0.07 (6043.4 step 729480, loss = 0.07 (6200.1 step 729490) loss = 0.08 (6055.5 step 729500, loss = 0.09 (5986.7 step 729510, loss =0.07 (6075.3 step 729520, loss = 0.06 (6630-1 step 729530, loss = 0.09 (6788.4



examples/sec; 0.021 sec/batch) examples/sec; 0.021 sec/batch) examples/sec; 0.021 sec/batch) examples/sec; 0.021 sec/batch) examples/sec; 0.021 sec/batch) examples/sec; 0.019 sec/batch) examples/sec; 0*019 sec/batch)



step 729540, step 729550, step 729560, step 729570, step 729580,



loss = 0.08

loss = 0.06

loss = 0.08

loss = 0.08

loss = 0.07



(6464.4 examples/sec; (6548.5 examples/sec; (6900.3 examples/sec; (6381.3 examples/sec; (6101.0 examples/sec;



0.020 sec/batch) 0.020 sec/batch) 0.019 sec/batch) 0.020 sec/batch) 0.021 sec/batch)



##### 9.3分布式并行

TensorFlow的分布式并行基于 gRPC 通信框架，其中包括一个 master 负责创建 Session, 还有多个 worker 负责执行计算图中的任务。我们需要先创建一个 TensorFlow Cluster对象， 它包含了一组 task (每个 task —般是一台单独的机器)用来分布式地执行 TensorFlow 的 计算图。一个 Cluster 可以切分为多个 job，一个 job 是指一类特定的任务，比如 parameter server (ps)、worker，每一个 job 里可以包含多个 task。我们需要为每一个 task 创建一个 server，然后连接到 Cluster 上，通常每个 task 会执行在不同的机器上，当然也可以一台机 器上执行多个 task (控制不同的 GPU )。Cluster对象通过 tf.train.ClusterSpec来初始化， 初始化信息是一个 python 的 diet，例如 tf.train.ClusterSpec({HpsH: ["192.168.233.201:2222"]， ,'worker,,:[n192.168.233.202:2222","192.168.233.203:2222n]})，这代表设置了一个 parameter server和两个 worker，分别在三台不同机器上。对每个 task，我们需要给它定义自己的身 份，比如对这个 ps 我们将设置 server = tf.train. Server(cluster, job_name=Hps", task_index=0)，将这台机器的 job 定义为 ps，并且是 ps 中的第 0 台机器。此外，通过在 程序中使用诸如 with tf.device("/job:worker/task:7")，可以限定 Variable 存放在哪个 task 或 哪台机器上。

TensorFlow的分布式有几种模式，比如 In-graph replication模型并行，将模型的计算 图的不同部分放在不同机器上执行；而 Befween-graph replication则是数据并行，每台机 器使用完全相同的计算图，但是计算不同的 batch 数据。此外，我们还有异步并行和同步 并行，异步并行指每机器独立计算梯度，一旦计算完就更新到 parameter server中，不等 其他机器；同步并行指等所有机器都完成对梯度的计算后，将多个梯度合成并统一更新模 型参数。一般来说，同步并行训练时，loss下降的速度更快，可达到的最大精度更高，但 是同步并行有木桶效应，速度取决于最慢的 SP 个机器，所以当设备速度一致时，效率比较 局 O

下面我们就用 TensorFlow 实现包含 1 个 paramter server和 2 个 worker 的分布式并行 训练程序，并以 MNIST 手写数据识别任务作为示例。这里需要写一个完整的 python 文件， 并在不同机器上以不同的 task 执行。首先载入 TensorFlow 和所有依赖库。本节代码主要 来自 TensorFlow 的开源实现 69。

import math

import tempfile

import time

import tensorflow as tf

from tensorflow.examples.tutorials.mnist import input_data

这里使用 tf.app.flags定义标记，用以在命令行执彳亍 TensorFlow 程序时设置参数。在 命令行中指定的参数会被 TensorFlow 读取，并直接转为 flags。设定数据储存目录 data_dir 默认为/tmp/mnist-data，隐藏节点数默认为 100，训练最大步数 train_steps默认为 1000000， batch size默认为 100，学习速率为默认 0.01。

flags = tf.app.flags

flags.DEFINE_string(ndata_dirnj "/tmp/mnist-data"j

"Directory for storing mnist data")

flags .DEFINE_integer(Mhidden_unitsn_, 100

"Number of units in the hidden layer of the NN")

flags. DEFINE_integer(,'train_steps,,J 1000000,

nNumber of (global) training steps to perform")

flags.DEFINE_integer(,'batch_size"^ 100) "Training batch size")

flags.DEFINE_float(Hlearning_raten0.01, "Learning rate")

然后设定是否使用同步并行的标记 sync_replicas默认为 False，在命令行执行时可以 设为 True 开启同步并行。同时，设定需要累计多少个梯度来更新模型的值默认为 None， 这个参数代表进行同步并行时，一共积攒多少个 batch 的梯度才进行一次参数更新，设为 None则使用 worker 的数量，即所有 worker 都完成一个 batch 的训练后再更新模型参数。

flags.DEFINE_boolean(.’sync_replicas"_, False,

"Use the sync一 replicas (synchronized replicas) mode," "wherein the parameter updates from workers are *' "aggregated before applied to avoid stale gradients")

flags.DEFINE_integer("replicas_to_aggregate"j Nonej

"Number of replicas to aggregate before parameter " "update is applied (For sync_replicas mode only;" "default: num_workers)")

再定义 ps 的地址，这里默认为 192.168.233.201:2222，读者应该根据集群的实际情况 配置，下同。将 worker 的地址设置为 192.168.233.202:2222 和 192.168.233.203:2222。同 时，设置 job_name和 task_index的 FLAG，这样在命令行执行时，可以输入这两个参数。

flags.DEFINE_string(,,ps_hosts,,J "192.168.233.201:2222\

"Comma-separated list of hostname:port pairs")

flags.DEFINE_string(nworker_hosts"?

"192.168.233.202：2222,192.168.233.203:2222n,

"Comma-separated list of hostname:port pairs")

flags.DEFINE_string("job_name"j None,"job name: worker or ps")

flags,DEFINE_integer("task_index", None,

"Worker task index_, should be >= 0. task_index=0 is "

•’the master worker task the performs the variable " "initialization ")

将 flags.FLAGS直接命名为 FLAGS，简化使用。同时，设置图片尺寸 IMAGE_PIXELS 为 28。

FLAGS = flags.FLAGS

IMAGE_PIXELS = 28

接下来编写程序的主函数 main，首先使用 input_data.read_data_sets下载并读取 MNIST 数据集，并设置为 one_hot编码格式。同时，检测命令行输入的参数，确保有 job_name 和 task_index这两个必备的参数。显示出 job_name和 task_index，并将 ps 和 worker 的所 有地址解析成列表 ps_spec和 worker_spec0

def mai门(unused_argv):

mnist = input__data.read_data_sets(FLAGS.data_dirj one_hot=True)

if FLAGS.job_name is None or FLAGS.job一 name ==

raise ValueError("Must specify an explicit 'job_name'")

if FLAGS.task_index is None or FLAGS.task_index

raise ValueError("Must specify an explicit 'task_index'")

print("job name = %s" % FLAGS.job_name) print(”task index = %d" % FLAGS.task_index)

ps_spec = FLAGS.ps_hosts.split(n/') worker_spec = FLAGS. worker_hosts. split

先计算总共的 worker 数量，然后使用 tf.train.ClusterSpec生成一个 TensorFlow Cluster 的对象，传入的参数是 ps 的地址信息和 worker 的地址信息。再使用 tf.train.Server创建当 前机器的 server，用以连接到 Cluster。如果当前节点是 parameter server，则不再进行后 续的操作，而是使用 server.join等待 worker 工作。

num一 workers = len(worker_spec)

cluster = tf.train.ClusterSpec({"ps": ps_spec^ "worker": worker_spec}) server = tf.train.Server(

clustery job_name=FLAGS.job_namej task_index=FLAGS.task_index) if FLAGS.job_name == "ps":

server.join()

这里判断当前机器是否为主节点，即 taskjndex 是否为 0。然后定义当前机器的 worker_device，格式为"job:worker/task:0/gpu:0"。我们假定有两台机器，并且每台机器有 1块 GPU，则总共需要两个 worker。如果一台机器有多块 GPU，可以通过一个 task 管理 多个 GPU 或者使用多个 task 分别管理。下面使用 tf.train.replica_device_setter()设置 worker 的资源，其中 worker_device为计算资源，ps_device为存储模型参数的资源。我们通过 replica_device_setter将模型参数部署在独立的 ps 服务器“/job:ps/cpu:0”，并将训练操作部 署在”/job:worker/task:0/gpu:0n，即本机的 GPU。最后再创建记录全局训练步数的变量 global_stepo

is_chief = (FLAGS.task_index == 0)

worker_device = "/job:worker/task:%d/gpu:0" % FLAGS.task_index with tf.device(

tf.train.replica_device_setter( worker_device=worker_device_,

ps_device="/job:ps/cpu:0"j cluster=cluster)):

global_step = tf.VariableCOj name="global_step"trainable= False)

接下来，定义神经网络模型，本节的神经网络和 4.4节的 MLP 全连接网络基本一致。

下面使用 tf.truncated_normal初始化权重，使用 tf.zeros初始化偏置，创建输入的 placeholder, 并使用 tf.nn.xwjplus_b对输入 x 进行矩阵乘法和加偏置操作，再用 ReLU 激活函数处理， 得到第一个隐层的输出 hid。然后使用 tf.nn.xw_plus_b和 tf.nn.softmax对第一层的输出 hid 进行处理，得到网络的最终输出 y。最后计算损失 cross_entropy，并定义优化器为 Adam。

hid_w = tf.Variable(

tf.truncated_normal([IMAGE_PIXELS*IMAGE_PIXELSJ FLAGS.hidden_units], stddev=1.0 / IMA6E_PIXELS)? name="hid_w")

hid_b = tf.Variable(tf.zeros([FLAGS.hidden_units])name= ”hid_b")

sm_w = tf.Variable(

tf. truncated一 normal ([FLAGS. hidden_units, 10]

stddev=1.0 / math.sqrt(FLAGS.hidden_units))name="sm_wn)

sm_b = tf.Variable(tf.zeros([10]name="sm_b")

x = tf.placeholder(tf.float32, [None, IMAGE_PIXELS * IMAGE_PIXELS]) y_ = tf.placeholder(tf.float32j [None, 10])

hid_lin = tf.nn.xw_plus_b(x, hid_wJ hid_b) hid = tf.nn.relu(hid_lin)

y = tf.nn.softmax(tf.nn.xw_plus_b(hid^ sm_w> sm_b))

cross_entropy = -tf.reduce_sum(y_ * tf.log(tf.clip_by_value(y^ le-10?

1.0)))

opt = tf.train.AdamOptimizer(FLAGS.learning_rate)

我们判断是否设置了同步训练模式 symLjeplicas，如果是同步模式，则先获取同步更 新模型参数所需要的副本数 replicas_to_aggregate；如果没有单独设置，则使用 worker 数

作为默认值。然后使用 tf.train.SyncReplicasOptimizer创建同步训练的优化器，它实质上是 对原有优化器的一个扩展，我们传入原有优化器及其他参数(replicas_to_aggregate、 total_num_replicas、replica_id等)，它就会将原有优化器改造为同步的分布式训练版本。 最后，使用普通的(即异步的)或同步的优化器对损失 crossjntropy 进行优化。

if FLAGS.sync_replicas:

if FLAGS.replicas_to一 aggregate is None:

replicas_to_aggregate = num_workers else:

replicas_to_aggregate = FLAGS.replicas_to_aggregate

opt = tf.train.SyncReplicasOptimizer( opt,

replicas_to_aggregate=replicas__to_aggregatej total_num_replicas=num__workersj replica_id=FLAGS.task_index^ name=,'mnist_sync_replicas")

train一 step = opt.minimize(cross_entropy4 global_step=global_step)

如果是同步训练模式，并且为主节点，则使用 opt.get_chief_queue_runner创建队列执 行器，并使用 opt.get_init_tokens_op创建全局参数初始化器。

if FLAGS.sync_replicas and is_chief:

chief一 queue_runner = opt.get_chief_queue一 runner() init_tokens_op = opt. get_init_tokens__op ()

下面生成本地的参数初始化操作 init_op ，创建临时的训练目录，并使用 tf.train_Supervisor创建分布式训练的监督器，传入的参数包括 is_chief、train_dir、init_op 等。这个 Supervisor 会管理我们的 task 参与到分布式训练。

init_op = tf.global_variables_initializer() train_dir = tempfile.mkdtemp() sv = tf.train.Supervisor(is_chief=is_chief

logdir=train_dirj init_op=init_opJ

recovery_wait_secs=l? global_step=global_step)

然后设置 Session 的参数，其中 allow_soft_placement设为 True 代表当某个操作在指 定的 device 不能执行时，可以转到其他 device 执行。

sess_config = tf.ConfigProto( a 11 o w_s oft_p 1 a c em e n t=T r u e log_device_placement=Falsej device_filters=["/job:ps '

"/job:worker/task:%d" % FLAGS.task_index])

如果为主节点，则显示初始化 Session，其他节点则显示等待主节点的初始化操作。 然后执行 sv.prepate_or_wait_for_session()，若为主节点则会创建 Session，若为分支节点则 会等待。

if is_chief:

print("Worker %d: Initializing session..." % FLAGS.task_index) else:

print("Worker %d: Waiting for session to be initialized…,’ %

FLAGS.task_index)

sess = sv.prepare_o「_wait_for_session(server.targetconfig=sess__config)

print("Worker %d: Session initialization complete." % FLAGS.task_index)

接着，如果处于同步模式并且是主节点，则调用 sv.start_queue_runners执行队列化执 行器 chief_queue_runner，并执行全局的参数初始化器 init_tokens_opD

if FLAGS.sync_replicas and is_chief:

print(HStarting chief queue runner and running init_tokens_op") sv.start_queue_runners(sess\, [chief_queue一 runner])

sess.run(init_tokens_op)

下面就正式到了训练过程。我们记录 worker 执行训练的启动时间，初始化本地训练 的步数 local_step，然后进入训练循环。在每一步训练中，我们从 mnist.train.next_batch读 取一个 batch 的数据，并生成 feed_dict，再调用 train_step执行一次训练。当全局训练步

数达到我们预设的最大值后，停止训练。

time_begin = time.time() print("Training begins @    " % time_begin)

local一 step = 0 while True:

batch_xSj batch_ys = mnist.train.next_batch(FLAGS.batch_size) train_feed = {x: batch_xs_, y_: batch_ys}

step = sess.run( [train_stepj global_step], feed_dict=train_feed) local_step += 1

now = time.time()

print("%f: Worker %d: training step %d done (global step: %d)" % (nowFLAGS.task_index_, local_step, step))

if step >= FLAGS.train_steps: break

训练结束后，我们展示总训练时间，并在验证数据上计算预测结果的损失 cross_entropy，并展示出来。至此，我们的主函数 main 全部结束。

time_end = time.time() print("Training ends @ %fn % time_end) training_time = time_end - time_begin

print("Training elapsed time: %f s" % training_time)    -

val_feed = {x: mnist.validation.imagesy一： mnist.validation.labels}

val_xent = sess.run(cross_entropyJ feed_dict=val_feed)

print("After %d training step(s), validation cross entropy = %g" %

(FLAGS.train_stepSj val_xent))

这是代码的最后一部分，在主程序中执行 tf.app.runO并启动 main()函数，我们将全部 代码保存为文件 distributed.py。我们需要在 3 台不同的机器上分别执行 distributed.py启动

3个 task，在每次执行 distributed.py时我们需要传入 job_name和 task_index指定 worker 的身份。

if _name_ == "_main_": tf.app.run()

我们分别在三台机器 192.168.233.201、192.168.233.202 和 192.168.233.203 上执行下 面三行代码。第一台机器执行第一行代码，第二台机器执行第二行代码，下同。这样我们 Z就在三台机器上分别启动了一个 parameter server及两个 worker。

python distributed.py --job_name=ps --task_index=0

python distributed.py --job_name=worker --task_index=0

python distributed.py --job_name=worker --task_index=l

如果我们想使用同步模式，只需要将上面的代码加上-sync_replicas=True，就可以自 动开启同步训练。注意，此时 global_step和异步不同，异步时，全局步数是所有 worker 训练步数之和，同步时则是指有多少轮并行训练。

python distributed.py --job_name=ps --task一 index=0 --sync_replicas=True python distributed.py --job_name=worker --task_index=0 --sync_replicas=True python distributed.py --job_name=worker --task_index=l --sync_replicas=True

下面是我们在 parameter server上显示出的日志。我们在 192.168.233.201:2222上顺利 开启了 PS的服务。

I tensorflow/core/distributed_runtime/rpc/grpc_channel.cc:197] Initialize Gr pcChannelCache for job ps -> {0 -> localhost:2222}

I tensorflow/core/distributed_runtime/rpc/grpc_channel.cc:197] Initialize Gr pcChannelCache for job worker -> {0 -> 192.168.233.202:2223, 1 -> 192.168.23 3.203:2224}

I tensorflow/core/distributed_runtime/rpc/grpc_server_lib.cc:206] Started se rver with target: grpc://localhost:2222

下面是 workerO 在 192.168.233.202上的训练日志。

1484195706.167773:    Worker    0:    training    step    5657    done    (global    step:    10285)

1484195706.178822:    Worker    0:    training    step    5658    done    (global    step:    10287)

1484195706.189648:    Worker    0:    training    step    5659    done    (global    step:    10289)

1484195706.200894: Worker 0: training step 5660 done (global step: 10291) 1484195706.212560: Worker 0: training step 5661 done (global step: 10293) 1484195706.224736: Worker 0: training step 5662 done (global step: 10295) 1484195706.237565: Worker 0: training step 5663 done (global step: 10297) 1484195706.252718: Worker 0: training step 5664 done (global step: 10299)

下面是 workerl 在 192.168.233.203上的训练日志。

1484195714.332566: Worker 1: training step 5269 done (global step: 11569) 1484195714.345961: Worker 1: training step 5270 done (global step: 11571) 1484195714.359124: Worker 1: training step 5271 done (global step: 11573) 1484195714.372848: Worker 1: training step 5272 done (global step: 11575) 1484195714.386048: Worker 1: training step 5273 done (global step: 11577) 1484195714.398567: Worker 1: training step 5274 done (global step: 11579) 1484195714.411631: Worker 1: training step 5275 done (global step: 11581) 1484195714.424619: Worker 1: training step 5276 done (global step: 11583)

至此，我们在三台机器上的数据并行模式的分布式训练的示例就结束了，读者可以看 到用 TensorFlow 实现分布式训练非常简单。我们可以复用单机版本的网络结构，只是在不 同机器上训练不同 batch 的数据，并使用 parameter server统一管理模型参数。另夕卜，分布 式 TensorFlow 的运行效率也非常高，在 16 台机器上可以获得 15 倍于单机的速度，非常 适合大规模神经网络的训练。
