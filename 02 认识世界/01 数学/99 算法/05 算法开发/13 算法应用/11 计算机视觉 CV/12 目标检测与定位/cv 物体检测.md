---
title: cv 物体检测
toc: true
date: 2018-08-16 19:00:26
---
# cv 物体检测

终于讲到我想要知道的东西了，一直想知道那种再行人走动的时候把人圈出来的是怎么做到的。以前我做过那种投影 flash 到地面上与走过的人互动的，一直想用这种厉害的东西。。但是最后还是选择了一个最简单的方法。。因此非常想知道。


# 物体检测问题概述


物体识别


![](http://images.iterate.site/blog/image/180727/lg976294Cl.png?imageslim){ width=55% }

下面这四张图片分别对应的四种不同的要求：越来越难**  ****厉害  想知道这些都是怎么做到的。**

图片识别                       图片识别+定位                          物体检测                                图像分割

Classification                  Localization                         Object Detection            Instance Segmentation

![](http://images.iterate.site/blog/image/180727/7bAh7g3A9E.png?imageslim){ width=55% }

左边两种应用的场景特点：




  * Single object, single class


  * Large-scale, millions level


右边两种应用的场景的特点：


  * Multiple objects and classes


  * Thousands level


其实还有更细致的 ：Matting **这个现在是怎么做的？**




# 物体识别的数据库与比赛






  * The renowned ImageNet ILSVRC Challenge http://image-net.org/ 最著名的 100多万张图片。 可能训练 1 周    物体检测的标准比赛


  * COCO Common Objects Dataset http://mscoco.org/ COCO只有 20 多万张，基本是真实世界里的，场景非常复杂 可能训练 3~4天


  * SUN http://groups.csail.mit.edu/vision/SUN/


  * Pascal VOC:http://host.robots.ox.ax.uk/pascal/VOC/ 这个只有 20 类，但是可以快速做实验，训练 1 天差不多。  **VOC就是 Visual Object Class**


  * CIFAR 这个数据量比较小，非常小的 32*32 的，主要研究 network architecture，即研究网络的结构怎么变化的 。**什么是 network architecture？ **




# 比较关键的几个 Deep Models：


![](http://images.iterate.site/blog/image/180727/J3fHGd4EhD.png?imageslim){ width=55% }





# 经典方法：  DPM


Deformable Parts Model

**这个方法到底是怎么做的？现在还这么做吗？**


![](http://images.iterate.site/blog/image/180727/IdmiKAf367.png?imageslim){ width=55% }


![](http://images.iterate.site/blog/image/180727/km3mIBkH3b.png?imageslim){ width=55% }
![](http://images.iterate.site/blog/image/180727/kB8fa28CId.png?imageslim){ width=55% }


涉及到两篇论文：




  * Felzenszwalbet al, “Object Detection with Discriminatively Trained Part Based Models”, PAMI 2010 [link](http://www.rossgirshick.info/)


  * Girschicket al, “Deformable Part Models are Convolutional Neural Networks”, CVPR 2015



![](http://images.iterate.site/blog/image/180727/0gH5gFfmd0.png?imageslim){ width=55% }

提出了一系列非常经典的做法，包括：
1. 如何应用 stochastic gradient descent (SGD) 到 training 里。
2. NMS (non-maximum suppression) 对后期 testing 的处理非常重要。**什么是 NMS？极大值抑制，非常有用的，所有的与识别相关的都要用到 NMS。**
3. Data mining hard examples这些概念至今仍在使用。**即 score为 1 的下次就不再参与训练，训练 0.5左右的。这些就是 hard 的。实际中是怎么操作的？**






# Deep Learning


为什么要用卷积？




  * 本质是：因为图像有 invariance，即图像的不变性，跟文字是不同的，文字是有时序性的，而图像倒着正着都是鸟，所以我可以用相同的参数，就是卷积，在图像上的不同位置来滑动，因为图像有 invariance，所以我才可以用卷积操作。**这个要注意，之前不知道为什么可以用卷积**


  * 其次才是可以降低计算量和参数量




![](http://images.iterate.site/blog/image/180727/53741a1gJ3.png?imageslim){ width=55% }

以前，怕纯 CNN 不带好，因此最后总是加上 FC，但是现在很多都是纯 CNN 的 model 了，只不过最后的 CNN 用的是 1*1的卷积。1*1的卷积有什么意义呢？1*1不会减少参数的，**不知道。**

Loss over the whole dataset:


![](http://images.iterate.site/blog/image/180727/G770EhJlbH.png?imageslim){ width=55% }

后面的是为了防止过拟合

In each solver iteration, we use a stochastic approximation of this objective, drawing a mini-batch of N << |D| instances:


![](http://images.iterate.site/blog/image/180727/g8Ec2HgihI.png?imageslim){ width=55% }




## Deep Learning - 参数更新、loss设计：




![](http://images.iterate.site/blog/image/180727/Imd9ea31A7.png?imageslim){ width=55% }

Data term: error averagedover instances

Regularizationterm: penalizelarge weights to improve generalization

Stochastic Gradient Descent (known as Solver)


![](http://images.iterate.site/blog/image/180727/e0B1DddII5.png?imageslim){ width=55% }



![](http://images.iterate.site/blog/image/180727/CbcDL8CBlk.png?imageslim){ width=55% }

对于一个 AlexNet 来说，
solver.prototxt里的设计如下：


![](http://images.iterate.site/blog/image/180727/33e2AjH4d1.png?imageslim){ width=55% }

http://caffe.berkeleyvision.org/doxygen/classcaffe_1_1HingeLossLayer.html






## 总结：深度学习三要素（when you read papers）


读文论就是读这三个方面，而任何一个方面，你做的好了，都可以发论文。




  1. 模型 (structure, VGG, GoogleNet-BN, ResNet, etc.)


  2. 数据 (dataset statistics, ImageNet, COCO, SUN) 关注的数据量，还有比如人脸检测，从网上下载的有一些合成的图片，假的人脸照片，怎么把这些去除掉？对数据的要求和本身的分析是比较重要的。


  3. 算法


    1. 训练过程 (loss, back-prop, sampling)


    2. 测试过程 (scale, NMS, post-processing)








# 怎么用深度学习做物体检测

是怎么联系的？是怎么想到的？实际上一篇论文 submit 的时候就已经过时了，这时候你就要想对于这个论文，what can you do in the future。因此每一篇论文的 motivation 是怎么想到的，这个是比较重要的。


## 怎么用深度学习做物体检测？


问题：给出一张图，识别图中的物体类别及位置。**提示：用分类做检测？**


![](http://images.iterate.site/blog/image/180727/dKciGl8gEa.png?imageslim){ width=55% }



![](http://images.iterate.site/blog/image/180727/fD9BH1GC44.png?imageslim){ width=55% }











其实它是这么做的：




  1. 先做区域提名（Region Proposal），也就是找出一些可能的感兴趣区域（Region Of Interest, ROI）。


  2. 然后使用分类模型，对 ROI 进行分类，比如说花这个框分类到花的概率是 0.8 ，那么我们就知道这个框里面可能是花。这个分类可以用 ResNet 等等。


所以，就把识别的问题 detect，转换为了分类的问题 classification。**利害呀** 可见，本身其实没有这么神秘。实际上第一步中会找到很多框 2000多个？只是把后面分类得分高的几个画了出来。


## 那么，区域提名（Region Proposal）是怎么做出来的呢？怎么画出的目标框？


一种方法是 Selective search method: bottom-up segmentation；  it merges regions at multiple scales and converts regions to boxes.   这个是**非 DL 的方法**。 ** super pixel 与 selective search 有什么关系？**

有一个方法是：super pixel ，将图像上的像素进行聚类，逐渐的聚的越来越大，比如根据 color 的 distance 或者 hot 的 distance。**到底实际是怎么聚的？有没有代码？**


###


computer vision 最大的哲学理念就是 multiple scale 这个，即多尺度。它能解决很多问题，一个是看到一个物体在不同的视角；一个是把 data **tation做的非常好了，就是一张图变成很多张图。**这个还想再了解下。**


![](http://images.iterate.site/blog/image/180727/6ggIJa51C0.png?imageslim){ width=55% }

Uijlingset al, “Selective Search for Object Recognition”, IJCV 2013




## 一些  region proposals 方法


做物体检测的一些常用的方法。如果工程里用，最简单粗暴，说明天就要交这个任务，SelectiveSearch是最有效的，是非 DL 的方法。

而 DL 的方法，就比如 fast RCNN 等很多。


![](http://images.iterate.site/blog/image/180727/0JD5afFhc8.png?imageslim){ width=55% }


这个表在下面这个文档中：

Hosang et al, “What makes for effective detection proposals?”, PAMI 2015


## 那么用 DL 怎么做 Region Proposal？


上面提到的 Selective Search 实际上不是 DL 的方法

Region Proposasl Network

这是一个 feature map，有一个中间点，有 9 个 anchor，anchor box rpn **什么是 rpn？没明白这张图。**


![](http://images.iterate.site/blog/image/180727/1bH370ik44.png?imageslim){ width=55% }

其实就是 iteratively/ recursively 做一遍 sliding window, 把 feature map上的每一个点，遍历搜索，设计出两种 loss： **没有很明白**


![](http://images.iterate.site/blog/image/180727/LcK4jhC5Ii.png?imageslim){ width=55% }

DL 的重要方法：RCNN，Fast-RCNN，Faster-RCNN：




# RCNN 的整体流程


pipeline 就是整体的流程

R是 region 的意思。为什么要 warped 一下？因为当时大家的网络最后一层都是一个 FC，它的输入是 fixed 的，由于要求这最后的输入是一样的，所以最前面的输入也需要一样。后面发展到全卷积网络就不需要这个 warped 的操作。由于当时全都是加一个 FC，因此加了 warped。**这也就是为什么 1*1的卷积有他自己的用处，因为它虽然等于 FC，但是允许你输入任意变化。****利害**


![](http://images.iterate.site/blog/image/180727/4JeGj6L7KE.png?imageslim){ width=55% }

当时它只把 DL 作为特征提取，然后做 SVM，而且 forward 2000遍，


## Step 1: Train (or download) a classification model for ImageNet (ResNet-101)


![](http://images.iterate.site/blog/image/180727/738G9lIcE9.png?imageslim){ width=55% }


## Step 2: Fine-tune model for detection






  * Instead of 1000 ImageNet classes, want 20 object classes + background （21是因为有个 background）


  * Throw away final fully-connected layer, reinitialize from scratch


  * Keep training model using positive / negative regions from detection images




![](http://images.iterate.site/blog/image/180727/0BLB89ifhi.png?imageslim){ width=55% }




## Step 3: Extract features






  * Extract region proposals for all images


  * For each region: warp to CNN input size, run forward through CNN, (save pool5 features to disk) **什么是 pool5 features ？ 为什么要存起来？**


  * Have a big hard drive: features are ~200GB for PASCAL dataset! **为什么会要这么多的存储空间？**


虽然有些比较笨重的缺点，但是当时是第一个用 DL 的方法来做 object detection 的。


![](http://images.iterate.site/blog/image/180727/99feB6AGDF.png?imageslim){ width=55% }




## Step 4: Train one binary SVM per class to classify region features


相当于把 softmax 用 SVM 来做


![](http://images.iterate.site/blog/image/180727/6DjkgJ7hj7.png?imageslim){ width=55% }




## Step 5 (bbox regression):


For each class, train a linear regression model to map from cached features to offsets to GT boxes to make up for “slightly wrong” proposals。

即只要有 offset，他就把框动一动


![](http://images.iterate.site/blog/image/180727/1Hm0aE9beB.png?imageslim){ width=55% }

RCNN results：


![](http://images.iterate.site/blog/image/180727/jd6C9315b4.png?imageslim){ width=55% }

两个结论：




  * 如果加了 bbox reg 会有提高


  * 网络越深，效果越好 。（当然，库也要大，至少 10 万张图）




## RCNN bottlenecks 瓶颈






  1. Slow at test-time: need to run full forward pass of CNN for each region proposal;


  2. SVMs and regressors are post-hoc: CNN features not updated in response to SVMs and regressors; **post-hoc 是什么？即不是 end-to-end的，不是嵌在深度学习这个框架里面的，而是后面单独弄的一个。**


  3. Complex multistage training pipeline.











# Fast-RCNN




## 测试的过程 test


所有的 2000 个框，都共享一个 feature 层，在 conv5 的 feature map 的时候再把它分开。这样大大减轻了计算量。就不用 2000 次了。**这个到底实际是怎么做的？**在 conv5之后的一些卷积或者 FC 也还是 2000 个 forward，但是相比之前的 RCNN，运算量已经减少很多了。

然后他提出了一个方法 RoI Pooling，它实际上就是 feature 层的 wraped 操作。

而且他把 SVM 嵌到 DL 里面了，**这个地方没明白？怎么嵌入的？而且从 FCs 出来的两个是什么？**

**图上的 SPP 是什么？ 而且在 CONV 的过程中，RoI是如何跟进的？**

**而且对于 RoI 的 BP 是怎么做的？**


![](http://images.iterate.site/blog/image/180727/JLcAab532E.png?imageslim){ width=55% }

Solution：Share computation of convolutional layers between proposals.


## 训练 train


loss 是 smooth L1 和 softmax 加起来的。


![](http://images.iterate.site/blog/image/180727/mDg3B4JlG9.png?imageslim){ width=55% }

Train end-to-end：

解决了 RCNN 重复计算每个区域的 feature 问题。本质上是提供了 ROI-pooling layer, 使任意大小的输入都能以固定大小输出。由于没有 wraped 操作，所以没有破坏长宽比。**没有破坏吗？**


## Fast-RCNN ROI-pooling details：how to back-prop？


**这一节没看懂，很重要**


![](http://images.iterate.site/blog/image/180727/fjJLb1Eha1.png?imageslim){ width=55% }


![](http://images.iterate.site/blog/image/180727/kFiebCa9h2.png?imageslim){ width=55% }


为什么又两项求和呢？原来是 28*28 的，

i是上一层的位置，j是这一层的位置 ，虽然是二维的，但是它拉成一个向量了，为了简写就这样了。

回顾一下深度学习课程中，如何计算层与层之间的梯度的？


![](http://images.iterate.site/blog/image/180727/E6gieII954.png?imageslim){ width=55% }


## Fast-RCNN bbox regression loss

![](http://images.iterate.site/blog/image/180727/EK8f0j70hg.png?imageslim){ width=55% }

t是 target v是 prediction，**没明白**


![](http://images.iterate.site/blog/image/180727/G7K3JA6iAB.png?imageslim){ width=55% }

https://github.com/ShaoqingRen/caffe/blob/062f2431162165c658a42d717baf8b74918aa18e/src/caffe/layers/smooth_L1_loss_layer.cu

那么具体的 target (Ground Truth)坐标怎么算呢？


![](http://images.iterate.site/blog/image/180727/lDjl5chEff.png?imageslim){ width=55% }

真实坐标减去已经有的坐标，然后除以宽度。我们预测的就是这个宽度

其实是预测差值。x - x_a (given box), x 是新的位置，t_x是偏移量。**没明白？没有讲的很细。**





# Faster RCNN



![](http://images.iterate.site/blog/image/180727/JH71CJEbGE.png?imageslim){ width=55% }

又 4 个 loss，rpn里面两个，一个是 regression 一个是 classification 然后 classificer 里面又两个 loss，也是一个 regression 和一个 classification。

与 Fast 的区别，fast没有 rpn 这个过程，faster把 selective search也嵌入到 network 里面了，forward一次，2000个框就有了，fast提框需要 20s，faster需要 1~2s这 2000 个框就有了。

使用 CNN 来直接生成 region-proposal。不用外部模型！但两个网络如何 share parameter?

https://github.com/rbgirshick/py-faster-rcnn


## RPN具体细节：


rpn只有两类 前景和后景？ **什么？**


### Train 过程






  1. 对于一张图，scale到某一尺度上(from 500 -> 800)


  2. 计算 anchor 大小，生成 output map:


    * 600 x 800 -> 35 x 24 x 15 (anchors) ~ 2w anchors  其中 35是 600 除以 16，15是自己定义的， 2W个框经过 NMS 就 2000 个了。**什么是 NMS？**





  3. 计算 bbox_regression_target, 准备 input_blob


  4. Forward, backward, 更新网络参数。


找到这四步骤对应的函数，对于理解整个算法非常重要。


![](http://images.iterate.site/blog/image/180727/7F03EkdeEG.png?imageslim){ width=55% }

![](http://images.iterate.site/blog/image/180727/1K13Im20Ch.png?imageslim){ width=55% }


### Test 过程






  1. 对于一张图，scale到某一尺度上(from 500 -> 800)

  2. Forward一遍，得出每个点的 score, bbox_regressiontarget, 计算 box 大小，scale back to original image.

  3. NMS等后续过程，凝练出 top_k个置信度很高的 box, 计算 recall, 得出结论。


![](http://images.iterate.site/blog/image/180727/GhkiBfjI83.png?imageslim){ width=55% }




## RPN实验结果

![](http://images.iterate.site/blog/image/180727/E157Ch0iFJ.png?imageslim){ width=55% }

![](http://images.iterate.site/blog/image/180727/dmHJF8774D.png?imageslim){ width=55% }



# 最新方法概述：


  * Fast-RCNN
    * Counterparts (Grid-based CNN,RCNN minus R,etc.)
  * YOLO: Unified real-time object detection (cvpr'16)
  * SSD:Single-shot multi-box detector(eccv'16)
  * Inside-Outside Net (cvpr'16)
  * Adaptive Object Detection using Adjacency-Zoom Prediction
  * Region-based FCN (NIPS'16)




## 最新方法：RFCN：


Region-based Fully Convolutional Networks




  * Motivation:previous methods require a costly pre-region subnet to compute the losses/class score.


  * Now:a position-sensitive score map mechanism


    * Fully convolutional with all computations shared on the entire image.


    * Solves the dilemma that detection is transition variance while classification is not .





Code available: [https://github.com/daijifengoo1/r-fcn](https://github.com/daijifeng001/r-fcn)


![](http://images.iterate.site/blog/image/180727/15Fc5ei52b.png?imageslim){ width=55% }

每个颜色代表不同的位置选择区域。
The bank of kxk score maps correspond to a kxk spatial grid describing relative positions.

The loss of training:


![](http://images.iterate.site/blog/image/180727/I1hmGAhG26.png?imageslim){ width=55% }



In particular, the classification loss:




![](http://images.iterate.site/blog/image/180727/mEg1aFJE5F.png?imageslim){ width=55% }

问题 1: RFCN跟 DPM 的关系，如何理解？

Position-sensitive score maps and ROI pooling


![](http://images.iterate.site/blog/image/180727/CHFKJK34eL.png?imageslim){ width=55% }



![](http://images.iterate.site/blog/image/180727/EE3kJaHI4I.png?imageslim){ width=55% }



![](http://images.iterate.site/blog/image/180727/HFkEK7JE1F.png?imageslim){ width=55% }

Figure 1: a positive box of person class


![](http://images.iterate.site/blog/image/180727/KGmK5FllH5.png?imageslim){ width=55% }

Figure 2: a negative box of person class


![](http://images.iterate.site/blog/image/180727/AiJB7aL5eB.png?imageslim){ width=55% }

问题 2: RCNN, Fast-RCNN, RFCN 在 feature-map 层面是如何联系起来的？


* RCNN是从一开始就把 2000 个框全部分开了，就是 2000 个框都是不同的 feature。
* Fast-RCNN 包括 Faster-RCNN 是拦腰截断，从 image 的输入到中间这块，只有一张图，forward一次，然后通过 ROI 把 2000 个框分开了。
* RFCN 它在最后一层分开。在 FastScale 上效果还比较好，但是在 ImageNet 上效果比 Fast-RCNN还差一些。




![](http://images.iterate.site/blog/image/180727/HGie3a24kg.png?imageslim){ width=55% }








# 值得思考的问题：




### Joint Training

![](http://images.iterate.site/blog/image/180727/e5E6DDjH3E.png?imageslim){ width=55% }

能不能一下把 4 个 loss 一起 train？这样 train 好像效果不好。


### Multi-depth Loss


有么有不同深度的 loss？


### Stacked-Hourglass Architecture

* 使用模块进行网络设计
* 先降采样，再升采样的全卷积结构
* 跳级结构辅助升采样
* 中继监督训练


![](http://images.iterate.site/blog/image/180727/6CFDEgfI8g.png?imageslim){ width=55% }

能不能用高层信息来弥补低层的信息？先训练 RCNN，后训练 Fast-RCNN

https://blog.csdn.net/shenxiaolu1984/article/details/51428392








# COMMENT：


下载 faster-rcnn 代码 (链接下页提供)，下载 Pascal VOC数据库。




  * 跑通整个 detection 的流程


  * 熟悉每个步骤的代码，例如：


    * function [input_blobs, random_scale_inds] = proposal_generate_minibatch(conf, image_roidb)


    * function [image_roidb, bbox_means, bbox_stds] = proposal_prepare_image_roidb(conf, imdbs,roidbs)





在此基础之上，将 stage 2的 multi-class detection, 改为 fore/background分类问题，实现 stage 1 RPN -> stage 2 proposal refinement, aka, cascade.

参考解答：https://github.com/hli2020/faster_rcnn  这个里面有一些注释。

**没有做，自己做过之后补充进来。**


#


一些参考资料




  1. Fast-RCNN


    * (Caffe+ MATLAB): https://github.com/rbgirshick/fast-rcnn





  2. Faster-RCNN


    * (Caffe+ MATLAB): https://github.com/ShaoqingRen/faster_rcnn


    * (Caffe+ python): https://github.com/rbgirshick/py-faster-rcnn





  3. YOLO   http://pjreddie.com/darknet/yolo/


  4. LocNet, AttractioNet (CVPR’16)   https://github.com/gidariss/LocNet




**都要仔细看过然后补充进来。**



**这个 Neural Style还是要自己试过之后，将代码和生成的图片都补充进来。**


#


**视频讲的很多地方没有很明白，再听一遍。**

**而且感觉有很多的东西可以学。**

**而且计算机视觉的课程也要总结下。**



RFCN能不能满足 real-time的？现在最快的应该是 1s 处理 4 帧，比 Faster 要快，但是相比自动驾驶和无人机，还是不能够 real-time的。

准确度要求的话：如果有准又快 RFCN就可以，再准一点点，就用 Faster-RCNN 但是会慢一点点。





没有基础的就先看看 Caffee 的 tutorial 里面有猫那个例子。

然后看 CS231n 真门课就从 0 基础开始学的。而且讲的很好，有汉化版本吧。





目标物体放在非常复杂的背景下，怎么解决？用 COCO train 不要用 VOC train





# 相关：

1. 七月在线 深度学习
2. [DPM（Deformable Parts Model）](http://www.52ml.net/15680.html) **这个还没看**
3. [Selective Search](https://blog.csdn.net/szj_huhu/article/details/78157982)    **还没看 Region Proposal 相关的**
