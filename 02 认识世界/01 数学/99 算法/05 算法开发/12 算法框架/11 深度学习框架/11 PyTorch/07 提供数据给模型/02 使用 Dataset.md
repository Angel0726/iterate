---
title: 02 使用 Dataset
toc: true
date: 2019-12-06
---


# 使用 Dataset

`torch.utils.data.Dataset` is an abstract class representing a dataset. Your custom dataset should inherit `Dataset` and override the following methods:

-  `__len__` so that `len(dataset)` returns the size of the dataset.
-  `__getitem__` to support the indexing such that `dataset[i]` can be used to get $i$ sample

Let's create a dataset class for our face landmarks dataset. We will read the csv in `__init__` but leave the reading of images to `__getitem__`. This is memory efficient because all the images are not stored in the memory at once but read as required. <span style="color:red;">嗯。好的。</span>

Sample of our dataset will be a dict `{'image': image, 'landmarks': landmarks}`. Our dataset will take an optional argument `transform` so that any required processing can be applied on the sample. We will see the usefulness of `transform` in the next section.<span style="color:red;">嗯。</span>





```python
class FaceLandmarksDataset(Dataset):
    """Face Landmarks dataset."""

    def __init__(self, csv_file, root_dir, transform=None):
        """
        Args:
            csv_file (string): Path to the csv file with annotations.
            root_dir (string): Directory with all the images.
            transform (callable, optional): Optional transform to be applied
                on a sample.
        """
        self.landmarks_frame = pd.read_csv(csv_file)
        self.root_dir = root_dir
        self.transform = transform

    def __len__(self):
        return len(self.landmarks_frame)

    def __getitem__(self, idx):
        img_name = os.path.join(self.root_dir,
                                self.landmarks_frame.iloc[idx, 0])
        image = io.imread(img_name)
        landmarks = self.landmarks_frame.iloc[idx, 1:]
        landmarks = np.array([landmarks])
        landmarks = landmarks.astype('float').reshape(-1, 2)
        sample = {'image': image, 'landmarks': landmarks}

        if self.transform:
            sample = self.transform(sample)

        return sample
```

Let's instantiate this class and iterate through the data samples. We will print the sizes of 4 samples and show their landmarks.




```python
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



class FaceLandmarksDataset(Dataset):
    """Face Landmarks dataset."""

    def __init__(self, csv_file, root_dir, transform=None):
        """
        Args:
            csv_file (string): Path to the csv file with annotations.
            root_dir (string): Directory with all the images.
            transform (callable, optional): Optional transform to be applied
                on a sample.
        """
        self.landmarks_frame = pd.read_csv(csv_file)
        self.root_dir = root_dir
        self.transform = transform

    def __len__(self):
        return len(self.landmarks_frame)

    def __getitem__(self, idx):
        img_name = os.path.join(self.root_dir,
                                self.landmarks_frame.iloc[idx, 0])
        image = io.imread(img_name)
        landmarks = self.landmarks_frame.iloc[idx, 1:]
        landmarks = np.array([landmarks])
        landmarks = landmarks.astype('float').reshape(-1, 2)
        sample = {'image': image, 'landmarks': landmarks}

        if self.transform:
            sample = self.transform(sample)

        return sample


face_dataset = FaceLandmarksDataset(csv_file='data/faces/face_landmarks.csv',
                                    root_dir='data/faces/')



plt.ion()  # interactive mode


def show_landmarks(image, landmarks):
    # plt.cla() # which clears data but not axes
    # plt.clf() # which clears data and axes
    plt.imshow(image)
    plt.scatter(landmarks[:, 0], landmarks[:, 1], s=10, marker='.', c='r')
    plt.pause(0.01)  # pause a bit so that plots are updated

fig = plt.figure()
print(len(face_dataset))

for i in range(len(face_dataset)):
    sample = face_dataset[i]

    print(i, sample['image'].shape, sample['landmarks'].shape)

    ax = plt.subplot(1, 4, i % 4 + 1)
    plt.tight_layout() # 这个是什么？
    ax.clear()
    ax.set_title('Sample #{}'.format(i))
    ax.axis('off')
    show_landmarks(**sample)

    if i == 3:
        plt.show()
```

输出：

```
69
0 (324, 215, 3) (68, 2)
1 (500, 333, 3) (68, 2)
2 (250, 258, 3) (68, 2)
略..
66 (239, 209, 3) (68, 2)
67 (257, 169, 3) (68, 2)
68 (250, 175, 3) (68, 2)
```

显示的画面，是在动态变动的：

<center>

![](http://images.iterate.site/blog/image/20190628/SFytGXw4E01w.png?imageslim){ width=55% }

</center>


<span style="color:red;">`plt.ion()` 这个还是不错的。以前没用过这个，只看到过一次。</span>

<span style="color:red;">关于这个地方的 plt 还是不错的。要整理下。如下：</span>


```py
plt.ion()  # interactive mode


fig = plt.figure()


ax = plt.subplot(1, 4, i % 4 + 1)
plt.tight_layout() # 这个是什么？
ax.clear()
ax.set_title('Sample #{}'.format(i))
ax.axis('off')
show_landmarks(**sample)


def show_landmarks(image, landmarks):
    # plt.cla() # which clears data but not axes
    # plt.clf() # which clears data and axes
    plt.imshow(image)
    plt.scatter(landmarks[:, 0], landmarks[:, 1], s=10, marker='.', c='r')
    plt.pause(0.01)  # pause a bit so that plots are updated
```
