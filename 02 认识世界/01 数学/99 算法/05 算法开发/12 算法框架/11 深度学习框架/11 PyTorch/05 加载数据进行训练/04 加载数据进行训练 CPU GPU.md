---
title: 04 加载数据进行训练 CPU GPU
toc: true
date: 2019-12-06
---
# 加载数据进行训练

举例：

```py
import torchvision
import torchvision.transforms as transforms

import torch
import torch.nn as nn
import torch.nn.functional as F
import torch.optim as optim




transform = transforms.Compose([transforms.ToTensor(),
                                transforms.Normalize((0.5, 0.5, 0.5), (0.5, 0.5, 0.5))])

trainset = torchvision.datasets.CIFAR10(root='./data',
                                        train=True,
                                        download=True,
                                        transform=transform)
trainloader = torch.utils.data.DataLoader(trainset,
                                          batch_size=4,
                                          shuffle=True,
                                          num_workers=1)

testset = torchvision.datasets.CIFAR10(root='./data',
                                       train=False,
                                       download=True,
                                       transform=transform)
testloader = torch.utils.data.DataLoader(testset,
                                         batch_size=4,
                                         shuffle=False,
                                         num_workers=1)

classes = ('plane', 'car', 'bird', 'cat',
           'deer', 'dog', 'frog', 'horse', 'ship', 'truck')


class Net(nn.Module):
    def __init__(self):
        super(Net, self).__init__()
        self.conv1 = nn.Conv2d(3, 6, 5)
        self.pool = nn.MaxPool2d(2, 2)
        self.conv2 = nn.Conv2d(6, 16, 5)
        self.fc1 = nn.Linear(16 * 5 * 5, 120)
        self.fc2 = nn.Linear(120, 84)
        self.fc3 = nn.Linear(84, 10)

    def forward(self, x):
        x = self.pool(F.relu(self.conv1(x)))
        x = self.pool(F.relu(self.conv2(x)))
        x = x.view(-1, 16 * 5 * 5)
        x = F.relu(self.fc1(x))
        x = F.relu(self.fc2(x))
        x = self.fc3(x)
        return x


net = Net()
print(net)


criterion = nn.CrossEntropyLoss()
optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)



if __name__=="__main__":

    # 找到 GPU
    # device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")
    device = torch.device('cpu')
    print(device)
    # 将网络移动到 GPU
    net.to(device)

    for epoch in range(10):
        running_loss = 0.0
        for i, data in enumerate(trainloader, 0):
            inputs, labels = data# 获取输入
            inputs, labels = inputs.to(device), labels.to(device)# 将数据移动到 GPU

            optimizer.zero_grad()# 梯度置 0
            outputs = net(inputs)# 正向传播
            loss = criterion(outputs, labels)# 求损失
            loss.backward()# 反向传播
            optimizer.step()# 使用梯度优化网络参数

            # 将每个 batch 的 loss 加总起来
            running_loss += loss.item()
            # 每 2000 个 batch 打印一次这 2000 次的平均 loss，同时清零这个 running_loss 再次从 0 统计。
            if i % 2000 == 1999:
                print('[%d, %5d] loss: %.3f' %
                      (epoch + 1, i + 1, running_loss / 2000))
                running_loss = 0.0

    print('Finished Training')
```


输出：

```
Files already downloaded and verified
Files already downloaded and verified
Net(
  (conv1): Conv2d(3, 6, kernel_size=(5, 5), stride=(1, 1))
  (pool): MaxPool2d(kernel_size=2, stride=2, padding=0, dilation=1, ceil_mode=False)
  (conv2): Conv2d(6, 16, kernel_size=(5, 5), stride=(1, 1))
  (fc1): Linear(in_features=400, out_features=120, bias=True)
  (fc2): Linear(in_features=120, out_features=84, bias=True)
  (fc3): Linear(in_features=84, out_features=10, bias=True)
)
Files already downloaded and verified
Files already downloaded and verified
Net(
  (conv1): Conv2d(3, 6, kernel_size=(5, 5), stride=(1, 1))
  (pool): MaxPool2d(kernel_size=2, stride=2, padding=0, dilation=1, ceil_mode=False)
  (conv2): Conv2d(6, 16, kernel_size=(5, 5), stride=(1, 1))
  (fc1): Linear(in_features=400, out_features=120, bias=True)
  (fc2): Linear(in_features=120, out_features=84, bias=True)
  (fc3): Linear(in_features=84, out_features=10, bias=True)
)
[1,  2000] loss: 2.229
[1,  4000] loss: 1.893
[1,  6000] loss: 1.672
[1,  8000] loss: 1.574
[1, 10000] loss: 1.534
[1, 12000] loss: 1.481

... 略

Files already downloaded and verified
Files already downloaded and verified
Net(
  (conv1): Conv2d(3, 6, kernel_size=(5, 5), stride=(1, 1))
  (pool): MaxPool2d(kernel_size=2, stride=2, padding=0, dilation=1, ceil_mode=False)
  (conv2): Conv2d(6, 16, kernel_size=(5, 5), stride=(1, 1))
  (fc1): Linear(in_features=400, out_features=120, bias=True)
  (fc2): Linear(in_features=120, out_features=84, bias=True)
  (fc3): Linear(in_features=84, out_features=10, bias=True)
)
[10,  2000] loss: 0.773
[10,  4000] loss: 0.777
[10,  6000] loss: 0.820
[10,  8000] loss: 0.835
[10, 10000] loss: 0.859
[10, 12000] loss: 0.859
Finished Training
```


说明：

- `criterion = nn.CrossEntropyLoss()` 我们使用的交叉熵作为损失函数。
- `optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)` 使用带动量的随机梯度下降。


说明，使用 GPU 进行训练：

- `device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")` 找到 GPU
- `net.to(device)` 将网络移动到 GPU 内，会递归遍历所有模块并将模块的参数和缓冲区转换成 CUDA 张量。
- `inputs, labels = inputs.to(device), labels.to(device)` 将要处理的数据移动到 GPU 内。

不是很清楚的：

- <span style="color:red;">为什么会在 trainloader 的时候吧 net 打印出来呢？而且，为什么会打印两次 Files already downloaded and verified ？</span>
- <span style="color:red;">`for i, data in enumerate(trainloader, 0):` 这个 enumerate 的使用没有明白。</span>
- <span style="color:red;">`.to` 这种移动方式的效率是高还是低？</span>



## 问题，为什么上面的程序使用 GPU 比 CPU 还要慢呢？是用法不对吗？


慢的原因可能在于数据的加载，当把 `batch_size=4` 改为  `batch_size=32` 时，GPU 要比 CPU 快的多。 </span>

举例：

```py
import torchvision
import torchvision.transforms as transforms

import torch
import torch.nn as nn
import torch.nn.functional as F
import torch.optim as optim

import time

transform = transforms.Compose([transforms.ToTensor(),
                                transforms.Normalize((0.5, 0.5, 0.5), (0.5, 0.5, 0.5))])

trainset = torchvision.datasets.CIFAR10(root='./data',
                                        train=True,
                                        download=True,
                                        transform=transform)
trainloader = torch.utils.data.DataLoader(trainset,
                                          batch_size=32,
                                          shuffle=True,
                                          num_workers=1)

testset = torchvision.datasets.CIFAR10(root='./data',
                                       train=False,
                                       download=True,
                                       transform=transform)
testloader = torch.utils.data.DataLoader(testset,
                                         batch_size=32,
                                         shuffle=False,
                                         num_workers=1)

classes = ('plane', 'car', 'bird', 'cat',
           'deer', 'dog', 'frog', 'horse', 'ship', 'truck')


class Net(nn.Module):
    def __init__(self):
        super(Net, self).__init__()
        self.conv1 = nn.Conv2d(3, 6, 5)
        self.pool = nn.MaxPool2d(2, 2)
        self.conv2 = nn.Conv2d(6, 16, 5)
        self.fc1 = nn.Linear(16 * 5 * 5, 120)
        self.fc2 = nn.Linear(120, 84)
        self.fc3 = nn.Linear(84, 10)

    def forward(self, x):
        x = self.pool(F.relu(self.conv1(x)))
        x = self.pool(F.relu(self.conv2(x)))
        x = x.view(-1, 16 * 5 * 5)
        x = F.relu(self.fc1(x))
        x = F.relu(self.fc2(x))
        x = self.fc3(x)
        return x


net = Net()
print(net)

criterion = nn.CrossEntropyLoss()
optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)

if __name__ == "__main__":

    # 找到 GPU
    device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")
    # device = torch.device('cpu')
    print(device)
    # 将网络移动到 GPU
    net.to(device)

    for epoch in range(10):
        running_loss = 0.0
        for i, data in enumerate(trainloader, 0):
            inputs, labels = data
            # 将数据移动到 GPU
            inputs, labels = inputs.to(device), labels.to(device)

            optimizer.zero_grad()
            outputs = net(inputs)
            loss = criterion(outputs, labels)
            loss.backward()
            optimizer.step()

            running_loss += loss.item()
            if i % 200 == 199:
                print('%s [%d, %5d] loss: %.3f' %
                      (time.time(), epoch + 1, i + 1, running_loss / 200))
                running_loss = 0.0

    print('Finished Training')
```

使用 GPU 时部分输出：

```
1575532355.461639 [1,   200] loss: 2.304
1575532356.631253 [1,   400] loss: 2.297
1575532357.8488886 [1,   600] loss: 2.287
1575532359.0163412 [1,   800] loss: 2.257
1575532360.2622814 [1,  1000] loss: 2.156
1575532361.4836802 [1,  1200] loss: 2.074
1575532362.6871197 [1,  1400] loss: 2.006
```

使用 CPU 时部分输出：

```
1575532481.806594 [1,   200] loss: 2.305
1575532484.5445862 [1,   400] loss: 2.301
1575532487.1784022 [1,   600] loss: 2.298
1575532489.785711 [1,   800] loss: 2.291
1575532492.389222 [1,  1000] loss: 2.269
1575532494.9623854 [1,  1200] loss: 2.192
1575532497.5460587 [1,  1400] loss: 2.101
```

测试环境：

- GPU：1 * NVIDIA GeForce GTX 1080 Ti
- CPU：12 * Intel(R) Core(TM) i7-7800X CPU @ 3.50GHz

可见，从 200 个 batch 到 1400 个 batch 使用时间：

- GPU：7s
- CPU：16s

GPU 还是挺快的。






# 相关

- [pytorch-handbook](https://github.com/zergtant/pytorch-handbook)
