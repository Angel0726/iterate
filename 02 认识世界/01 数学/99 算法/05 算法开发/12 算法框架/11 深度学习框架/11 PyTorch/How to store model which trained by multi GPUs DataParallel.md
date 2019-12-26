---
title: How to store model which trained by multi GPUs DataParallel
toc: true
date: 2019-06-10
---



# How to store model which trained by multi GPUs(DataParallel)?

这个问题最近遇到了，发现，经过 DataParallel 之后，创建的模型中的 key 的名称比指定的名称多了 model. 这个开头， 也就是说：

the model is nested in the module of DataParallel。

嗯，解决方法是，在加载的时候，使用 `model.module.state_dict()` 而不是 `model.state_dict()` 。







# 相关

- [How to store model which trained by multi GPUs(DataParallel)?](https://discuss.pytorch.org/t/how-to-store-model-which-trained-by-multi-gpus-dataparallel/6526)
- [DataParallel optim and saving correctness](https://discuss.pytorch.org/t/dataparallel-optim-and-saving-correctness/4054)
- [solved KeyError: ‘unexpected key “module.encoder.embedding.weight” in state_dict’](https://discuss.pytorch.org/t/solved-keyerror-unexpected-key-module-encoder-embedding-weight-in-state-dict/1686)
