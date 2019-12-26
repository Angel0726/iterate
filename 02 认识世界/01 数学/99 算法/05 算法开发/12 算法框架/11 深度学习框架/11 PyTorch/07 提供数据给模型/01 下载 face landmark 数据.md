---
title: 01 下载 face landmark 数据
toc: true
date: 2019-12-06
---
# 下载 face landmark 数据

软件包准备：

-  `scikit-image`: For image io and transforms <span style="color:red;">这个之前没有听说过。</span>
-  `pandas`: For easier csv parsing

数据准备：

- 要处理的数据：人脸数据，有对应的 68 点的 landmark 标记
- 数据下载：<https://download.pytorch.org/tutorial/faces.zip>
- 数据存放地址：'data/faces/'
- 数据格式：cvs



举例：

```py
from __future__ import print_function, division
import os
import torch
import pandas as pd
from skimage import io, transform
import numpy as np
import matplotlib.pyplot as plt
from torch.utils.data import Dataset, DataLoader
from torchvision import transforms, utils

# Ignore warnings
import warnings
warnings.filterwarnings("ignore")

# plt.ion()  # interactive mode

landmarks_frame = pd.read_csv('data/faces/face_landmarks.csv')

n = 65
img_name = landmarks_frame.iloc[n, 0]
landmarks = landmarks_frame.iloc[n, 1:].as_matrix()
landmarks = landmarks.astype('float').reshape(-1, 2)  # 将 x,y 组成一对

print('Image name: {}'.format(img_name))
print('Landmarks shape: {}'.format(landmarks.shape))
print('First 4 Landmarks:\n {}'.format(landmarks[:4]))


def show_landmarks(image, landmarks):
    plt.imshow(image)
    plt.scatter(landmarks[:, 0], landmarks[:, 1], s=10, marker='.', c='r')
    # plt.pause(0.001)  # pause a bit so that plots are updated


plt.figure()
show_landmarks(io.imread(os.path.join('data/faces/', img_name)), landmarks)
plt.show()
```



输出：

```
[32 65 33 76 34 86 34 97 37 107 41 116 50 122 61 126 72 127 83 126 95 123
 107 119 115 111 118 101 120 91 122 80 122 68 39 52 45 46 53 44 61 46 68
 49 82 49 90 45 98 44 106 46 112 52 74 57 74 63 74 69 74 75 67 83 70 84 74
 85 78 84 82 83 47 61 51 57 58 57 63 61 57 63 51 63 87 62 93 58 98 58 103
 61 99 63 93 63 55 98 63 96 70 94 75 95 80 94 86 95 94 99 86 103 79 105 74
 105 69 105 62 103 58 99 70 98 74 98 79 98 91 99 79 99 74 99 69 99]
[[ 32.  65.]
 [ 33.  76.]
 .. 略
 [ 79. 105.]
 [ 74. 105.]
 [ 69. 105.]
 [ 62. 103.]
 [ 58.  99.]
 [ 70.  98.]
 [ 74.  98.]
 [ 79.  98.]
 [ 91.  99.]
 [ 79.  99.]
 [ 74.  99.]
 [ 69.  99.]]
Image name: person-7.jpg
Landmarks shape: (68, 2)
First 4 Landmarks:
 [[32. 65.]
 [33. 76.]
 [34. 86.]
 [34. 97.]]
```

显示的图片：

<center>

![](http://images.iterate.site/blog/image/20190628/Mtvag7YI6ecW.png?imageslim){ width=55% }
</center>


说明：

- 读取 CSV 文件，并将标记存放到 一个 (N, 2) 数组中，N
是 landmark 的个数。
