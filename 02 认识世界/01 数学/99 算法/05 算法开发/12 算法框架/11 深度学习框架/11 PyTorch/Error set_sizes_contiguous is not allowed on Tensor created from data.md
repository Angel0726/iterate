---
title: Error set_sizes_contiguous is not allowed on Tensor created from data
toc: true
date: 2019-05-30
---
# 可以补充进来的

- 今天遇到的一个问题。看来源码还是要看的，不然根本不知道里面到底是有怎么实现的。

# RuntimeError: set_sizes_contiguous is not allowed on Tensor created from .data or .detach(), in Pytorch 1.1.0



Im trying to to do this:
`Img_.data.resize_(Img.size()).copy_(Img)`
and got this error:

```
RuntimeError: set_sizes_contiguous is not allowed on Tensor created from .data or .detach()
```


`.data.resize_` was an unsupported operation (infact using .data is being discouraged). It worked in 1.0.1 because we still didn’t finish part of a refactor.

You should now use:

```
with torch.no_grad():
    Img_.resize_(Img.size()).copy_(Img))
```


# 相关

- [RuntimeError: set_sizes_contiguous is not allowed on Tensor created from .data or .detach(), in Pytorch 1.1.0](https://discuss.pytorch.org/t/runtimeerror-set-sizes-contiguous-is-not-allowed-on-tensor-created-from-data-or-detach-in-pytorch-1-1-0/44208)
