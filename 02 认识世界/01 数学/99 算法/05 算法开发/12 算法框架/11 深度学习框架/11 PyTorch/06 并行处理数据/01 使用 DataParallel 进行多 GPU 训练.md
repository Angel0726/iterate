---
title: 01 使用 DataParallel 进行多 GPU 训练
toc: true
date: 2019-12-06
---
# 可以补充进来的

- 之前我以为多 GPU 需要各种设置的，现在知道了对应的方式。

# 使用 DataParallel 进行多 GPU 训练

举例：

```py
import torch
import torch.nn as nn
from torch.utils.data import Dataset, DataLoader

# 参数设置
input_size = 5
output_size = 2

batch_size = 30
data_size = 100



class RandomDataset(Dataset):
    def __init__(self, size, length):
        self.len = length
        self.data = torch.randn(length, size)

    def __getitem__(self, index):
        return self.data[index]

    def __len__(self):
        return self.len

rand_loader = DataLoader(dataset=RandomDataset(input_size, data_size),
                         batch_size=batch_size,
                         shuffle=True)


class Model(nn.Module):
    def __init__(self, input_size, output_size):
        super(Model, self).__init__()
        self.fc = nn.Linear(input_size, output_size)

    def forward(self, input):
        output = self.fc(input)
        print("\tIn Model: input size", input.size(),
              "output size", output.size())
        return output

model = Model(input_size, output_size)



device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")

if torch.cuda.device_count() > 1:
  print("Let's use", torch.cuda.device_count(), "GPUs!")
  # dim = 0 [30, xxx] -> [10, ...], [10, ...], [10, ...] on 3 GPUs
  model = nn.DataParallel(model)

model.to(device)


for data in rand_loader:
    input = data.to(device)
    output = model(input)
    print("Outside: input size", input.size(),
          "output_size", output.size())
```

输出：

```
In Model: input size torch.Size([30, 5]) output size torch.Size([30, 2])
Outside: input size torch.Size([30, 5]) output_size torch.Size([30, 2])
In Model: input size torch.Size([30, 5]) output size torch.Size([30, 2])
Outside: input size torch.Size([30, 5]) output_size torch.Size([30, 2])
In Model: input size torch.Size([30, 5]) output size torch.Size([30, 2])
Outside: input size torch.Size([30, 5]) output_size torch.Size([30, 2])
In Model: input size torch.Size([10, 5]) output size torch.Size([10, 2])
Outside: input size torch.Size([10, 5]) output_size torch.Size([10, 2])
```

如果你有 3 个 GPU，你将看到：**（未测试）**


```
Let's use 3 GPUs!
    In Model: input size torch.Size([10, 5]) output size torch.Size([10, 2])
    In Model: input size torch.Size([10, 5]) output size torch.Size([10, 2])
    In Model: input size torch.Size([10, 5]) output size torch.Size([10, 2])
Outside: input size torch.Size([30, 5]) output_size torch.Size([30, 2])
    In Model: input size torch.Size([10, 5]) output size torch.Size([10, 2])
    In Model: input size torch.Size([10, 5]) output size torch.Size([10, 2])
    In Model: input size torch.Size([10, 5]) output size torch.Size([10, 2])
Outside: input size torch.Size([30, 5]) output_size torch.Size([30, 2])
    In Model: input size torch.Size([10, 5]) output size torch.Size([10, 2])
    In Model: input size torch.Size([10, 5]) output size torch.Size([10, 2])
    In Model: input size torch.Size([10, 5]) output size torch.Size([10, 2])
Outside: input size torch.Size([30, 5]) output_size torch.Size([30, 2])
    In Model: input size torch.Size([4, 5]) output size torch.Size([4, 2])
    In Model: input size torch.Size([4, 5]) output size torch.Size([4, 2])
    In Model: input size torch.Size([2, 5]) output size torch.Size([2, 2])
Outside: input size torch.Size([10, 5]) output_size torch.Size([10, 2])
```

说明，随机数据生成部分：

- `RandomDataset` 用于模拟生成数据提供给 DataLoader，当使用真实数据时，也可以类似的进行提供。完善对应的三个函数即可。

说明，网络搭建部分：

- `self.fc = nn.Linear(input_size, output_size)` 网络搭建了单层的全连接网络，使用外接传入的 `input_size` 和 `output_size` 参数。


说明，多 GPU 使用部分：

- `if torch.cuda.device_count() > 1:` 判断是否由多 GPU。
- `model = nn.DataParallel(model)` 将使用 `nn.DataParallel` 来包装我们的模型。`DataParallel` 会自动的划分数据，并将作业发送到多个 GPU 上的多个模型。并在每个模型完成作业后，收集合并结果并返回。`DataParallel` 能在任何模型（CNN，RNN，Capsule Net等）上使用。<span style="color:red;">Capsule Net 在 PyTorch 里面都有吗？要补充下。</span>
- `model.to(device)` 把模型放到 GPU 上。

说明，输出的 `torch.Size` 说明：

- 单 GPU 时，数据总量为 100，单个 batch 为 30 ，因此一个 epoch ，输出的 batch size 为 30,30,30,10。
- 3 个 GPU 时，数据总量为 100，单个 batch 为 30 ，因此一个 epoch，输出的 batch size 的总和为 30,30,30,10，每个 batch size 按照 GPU 合数进行划分：30=10+10+10 ，10=4+4+2。
- **因此，多个 GPU 时，我们只需要把它看做一个大显存的 GPU 即可。因此，如果单个 GPU 我们设置 batch size 为 32，多个 GPU 的话 batch size 可以设置为 32*n。**


不清楚的地方：

- <span style="color:red;">为什么 `model.to(device)` 对应的 `device` 还是单个的 `cuda:0`？是写错了吗？确认下。</span>






# 相关

- [pytorch-handbook](https://github.com/zergtant/pytorch-handbook) **Authors**: [Sung Kim](https://github.com/hunkim) and [Jenny Kang](https://github.com/jennykang)
