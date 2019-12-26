---
title: ValueError sampler is mutually exclusive with shuffle
toc: true
date: 2019-05-30
---
# 可以补充进来的

- 嗯，这个知道了，是 `shuffle` 和 `sampler` 的设定冲突了。

# ValueError('sampler is mutually exclusive with shuffle')


In the DataLoader, the "shuffle" is True so sampler should be None object.

```
train_loader = torch.utils.data.DataLoader(
train_dataset, batch_size=opt.batchSize,
shuffle=True, sampler=sampler,
num_workers=int(opt.workers),
collate_fn=dataset.alignCollate(imgH=opt.imgH, imgW=opt.imgW, keep_ratio=opt.keep_ratio))
```


In the code

```
parser.add_argument('--random_sample', action='store_true', help='whether to sample the dataset with random sampler')
```


We should change into

```
parser.add_argument('--random_sample', action='store_true', default=True, help='whether to sample the dataset with random sampler')
```



# 相关

- [ValueError(‘sampler is mutually exclusive with shuffle’) ](https://github.com/meijieru/crnn.pytorch/issues/55)
