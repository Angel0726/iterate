---
title: 分享 Python 非极大值抑制方法运行得飞快的秘诀
toc: true
date: 2019-11-17
---
# 分享 Python 非极大值抑制方法运行得飞快的秘诀


![img](https://mmbiz.qpic.cn/mmbiz_jpg/bicdMLzImlibSibAtf224A4yf7icWuF3YltvVloFOQu90uzibHozreOnM13SKouvK42vJJp1F76ibUe32zQzK29qVb7g/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

我有一个困惑：我不能停止对目标检测的思考。

你知道的，昨晚在我在看《行尸走肉》时，不是享受僵尸野蛮和被迫吃人，或引人入胜的故事情节，我只想建立一个目标检测系统来对僵尸进行识别。

这个检测系统会很有用吗？可能不会。

我是说，如果一个僵尸跟在你后面那将是很明显的：光是那阵恶臭就会告诉你这是一个死人（嘿，看看这个一语双关）散发出来的，更不用说狰狞的牙齿和挥动的手臂。我们也可能会陷入那些从僵尸喉咙里发出的「脑子.... 脑子...」呻吟声中。

就像我说的，如果有一个僵尸在你身后，你当然不需要计算机视觉系统来告诉你这件事。但这只是一个每天都在我脑海里流淌的例子罢了。

为了给你一些相关信息，两个星期前，我在帖子中展示了如何使用直方图的方向梯度和线性支持向量机来建立一个目标检测系统。厌倦了 OpenCV Haar 复杂的结构和糟糕的性能，更不要说那么长的训练时间，因此我自己动手编写了自己的 Python 目标检测框架。

到目前为止，它运行得非常好，而且实现起来非常有趣。

但是在构建目标检测系统——重叠候选框这个不可回避的问题你必须处理。这是会发生的，没有任何办法可以绕过它。但事实上，这是一个很好的迹象，表明你的目标检测器正在进行合理的微调，所以我甚至不说它是一个「问题」。

为了处理这些需要移除的重叠候选框（对同一个对象而言），我们可以对 Mean Shift 算法进行非极大值抑制。虽然 Dalal 和 Triggs 更喜欢 Mean-Shift 算法，我却发现 Mean Shift 给出了低于平均值的结果。

在收到我朋友 Tomasz Malisiewicz 博士（目标检测方面的专家）的建议之后，我决定将他 Matlab 上实现的非最大抑制方法移植到 Python 上。

上周我向你们展示了如何实施 FelZeZZWalb 等方法。这周我要向你们展示 Malisiewicz 的方法使我运行速度快 100 倍的方法。

注：我本来打算在十一月发布这篇博客，但由于我糟糕的拖延症，我花了很多时间才把这篇文章写出来。不过无论如何，它现在已经在网上了！

那么提速是从哪里来的呢？我们是如何获得这么快的抑制时间的呢？

继续阅读去找出答案。



##   **在Python上的非极大值抑制方法（更快）**

在我们开始之前，如果你还没有读过上周关于非极大值抑制的帖子，我建议你先看一下那个帖子。

如果你已经看过那个帖子，那么在你最喜欢的编辑器中新建一个文件，命名为 nms.py，让我们开始创建一个更快的非极大值抑制实现方法：

```py
# import the necessary packages
import numpy as np

# Malisiewicz et al.
def non_max_suppression_fast(boxes, overlapThresh):
	# if there are no boxes, return an empty list
	if len(boxes) == 0:
		return []

	# if the bounding boxes integers, convert them to floats --
	# this is important since we'll be doing a bunch of divisions
	if boxes.dtype.kind == "i":
		boxes = boxes.astype("float")

	# initialize the list of picked indexes
	pick = []

	# grab the coordinates of the bounding boxes
	x1 = boxes[:,0]
	y1 = boxes[:,1]
	x2 = boxes[:,2]
	y2 = boxes[:,3]

	# compute the area of the bounding boxes and sort the bounding
	# boxes by the bottom-right y-coordinate of the bounding box
	area = (x2 - x1 + 1) * (y2 - y1 + 1)
	idxs = np.argsort(y2)

	# keep looping while some indexes still remain in the indexes
	# list
	while len(idxs) > 0:
		# grab the last index in the indexes list and add the
		# index value to the list of picked indexes
		last = len(idxs) - 1
		i = idxs[last]
		pick.append(i)

		# find the largest (x, y) coordinates for the start of
		# the bounding box and the smallest (x, y) coordinates
		# for the end of the bounding box
		xx1 = np.maximum(x1[i], x1[idxs[:last]])
		yy1 = np.maximum(y1[i], y1[idxs[:last]])
		xx2 = np.minimum(x2[i], x2[idxs[:last]])
		yy2 = np.minimum(y2[i], y2[idxs[:last]])

		# compute the width and height of the bounding box
		w = np.maximum(0, xx2 - xx1 + 1)
		h = np.maximum(0, yy2 - yy1 + 1)

		# compute the ratio of overlap
		overlap = (w * h) / area[idxs[:last]]

		# delete all indexes from the index list that have
		idxs = np.delete(idxs, np.concatenate(([last],
			np.where(overlap > overlapThresh)[0])))

	# return only the bounding boxes that were picked using the
	# integer data type
	return boxes[pick].astype("int")
```

请花几秒钟时间仔细检查这个代码，将其与上周提出的 FelZeZZWalb 方法进行比较。

代码看起来几乎一样，对吧？

所以你可能会问自己：「这 100 倍加速是从哪里来的？」

**答案是我们移除了一个内部循环结构。**

上周提出的实现方法需要一个额外的内部循环来计算边界区域的大小和重叠区域的比率。

在本文中取而代之的是，Malisiewicz 博士用矢量化代码替换了这个内部循环，这就是我们在应用非极大值抑制时能够实现更快速度的原因。

与其像上周那样我一个人逐行逐行地阅读代码，不如让我们一起来看一下其中关键的部分。

我们这个更快的非极大值抑制函数第 6-22 行基本与上周相同。我们通过抓取检测框的（x，y）坐标，计算它们的面积，并根据每个框的右下 y 坐标将他们分类到框列表中。

 第 31-55 行包含我们的加速过程，其中第 41-55 行特别重要。我们不再使用内部 for 循环来对单独对每个框进行循环，而是使用 np.maximum 和 np.minimum 对代码进行矢量化，这使得我们能够在坐标轴上找到最大值和最小值而不仅仅是一个数。

注意：你在这里必须使用 np.maximum 和 np.minimum——它们允许您混合标量和向量。然而 np.max 和 np.min 函数就没有这样的功能，当你使用它们时，你会发现有一些非常严重的 bug 需要查找和修复。当我把算法从 Matlab 移植到 Python 时，我花了很长时间来解决这个问题。第 47 行和第 48 行也被矢量化，在这里我们计算每个矩形的宽度和高度来进行检查。相似的，第 51 行上的重叠率也被矢量化。从那里，我们只需删除我们的 IDX 列表中的所有条目，这些条目都大于我们提供的重叠阈值。通常重叠阈值在 0.3-0.5 之间。

Malisiewicz 等人提出的方法与 FelZeZnZWalb 等的基本相同。但通过使用矢量化代码，我们能够在非极大值抑制上实现 100 倍加速！



##   **运行更快的非极大值抑制方法**

让我们继续并研究几个例子。我们从这张照片的顶部的一个恐怖的小女孩僵尸开始：

![img](https://mmbiz.qpic.cn/mmbiz_jpg/bicdMLzImlibSibAtf224A4yf7icWuF3YltvVloFOQu90uzibHozreOnM13SKouvK42vJJp1F76ibUe32zQzK29qVb7g/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

图 1：图像中有 3 个检测边界框，但非极大值抑制方法让其中的两个重叠框消失。

事实上，我们的人脸检测器在真实、健康的人脸上训练的有多好可以推广到僵尸面孔上，这真的很有趣。当然，他们仍然是「人类」的面孔，但由于所有的血液和残损，看到一些奇怪结果时我也不会感到惊奇。

![img](https://mmbiz.qpic.cn/mmbiz_jpg/bicdMLzImlibSibAtf224A4yf7icWuF3YltvaUYA047htxO1sicXz9JHphUFVnq0FzdibhViayyG7SkD0L97LpZhSlfQw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

图 2：这个图看起来我们的人脸检测器没有推广的很好——对检测器而言，这个僵尸的牙齿看起来像是一张脸。

当谈论奇怪结果的时候，这看起来像是我们的人脸检测器检测到了右边的僵尸的嘴巴/牙齿区域。如果我在僵尸图像上显式地训练 HOG+线性 SVM 人脸检测器，也许结果会更好。

![img](https://mmbiz.qpic.cn/mmbiz_jpg/bicdMLzImlibSibAtf224A4yf7icWuF3Yltv9eacx7IXn18HmMNO1wDSVJul2vU6hu03bicjISAwVQQoV8RBrhjA7mA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

图 3：在面部周围检测到6个检测框，但是通过应用快速非极大值抑制算法，我们能够正确地将检测框的数量减少到1个。

在最后一个例子中，我们可以再次看到，我们的非极大值抑制算法是正确的——即使有六个原始检测框被 HOG+线性 SVM 检测器检测到，应用非极大值抑制算法正确地抑制了其他五个检测框，给我们留下了最后的检测结果。



##   **总结**

在这篇博客中，我们对 Malisiewicz 等人提出利用非极大值抑制的方法进行评价。

这种方法和 Felzenszwalb 等人提出的方法几乎一样，但是通过移除一个内部循环函数和利用矢量化代码，我们能够得到一种更快的替代方法。

如果你不是那么赶时间，请不要忘了感谢 Tomasz Malisiewicz 博士！



原文链接：

https://www.pyimagesearch.com/2015/02/16/faster-non-maximum-suppression-python/


# 相关

- [丧尸目标检测：和你分享 Python 非极大值抑制方法运行得飞快的秘诀](https://mp.weixin.qq.com/s?__biz=MjM5ODU3OTIyOA==&mid=2650672457&idx=1&sn=5d778c887ba2da967e4490c4d2b330ff&chksm=bec2303a89b5b92c98ebdc1dfbce9527dac887287c1b643ba7c32d37325bebfd015b918194a8&mpshare=1&scene=1&srcid=0804seeAFUjblgrBXP33cg3Z#rd)
