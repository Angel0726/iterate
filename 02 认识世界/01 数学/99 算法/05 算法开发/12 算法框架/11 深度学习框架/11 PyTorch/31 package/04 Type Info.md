---
title: 04 Type Info
toc: true
date: 2019-06-29
---
# TYPE INFO

The numerical properties of a [`torch.dtype`](https://pytorch.org/docs/stable/tensor_attributes.html#torch.torch.dtype) can be accessed through either the [`torch.finfo`](https://pytorch.org/docs/stable/type_info.html#torch.torch.finfo) or the [`torch.iinfo`](https://pytorch.org/docs/stable/type_info.html#torch.torch.iinfo).



## torch.finfo

- *CLASS*`torch.``finfo`


A [`torch.finfo`](https://pytorch.org/docs/stable/type_info.html#torch.torch.finfo) is an object that represents the numerical properties of a floating point [`torch.dtype`](https://pytorch.org/docs/stable/tensor_attributes.html#torch.torch.dtype), (i.e. `torch.float32`, `torch.float64`, and `torch.float16`). This is similar to [numpy.finfo](https://docs.scipy.org/doc/numpy/reference/generated/numpy.finfo.html).

A [`torch.finfo`](https://pytorch.org/docs/stable/type_info.html#torch.torch.finfo) provides the following attributes:

| Name | Type  | Description                                                  |
| ---- | ----- | ------------------------------------------------------------ |
| bits | int   | The number of bits occupied by the type.                     |
| eps  | float | The smallest representable number such that `1.0 + eps != 1.0`. |
| max  | float | The largest representable number.                            |
| min  | float | The smallest representable number (typically `-max`).        |
| tiny | float | The smallest positive representable number.                  |

NOTE

The constructor of [`torch.finfo`](https://pytorch.org/docs/stable/type_info.html#torch.torch.finfo) can be called without argument, in which case the class is created for the pytorch default dtype (as returned by [`torch.get_default_dtype()`](https://pytorch.org/docs/stable/torch.html#torch.get_default_dtype)).



## torch.iinfo

- *CLASS*`torch.``iinfo`


A [`torch.iinfo`](https://pytorch.org/docs/stable/type_info.html#torch.torch.iinfo) is an object that represents the numerical properties of a integer [`torch.dtype`](https://pytorch.org/docs/stable/tensor_attributes.html#torch.torch.dtype) (i.e. `torch.uint8`, `torch.int8`, `torch.int16`, `torch.int32`, and `torch.int64`). This is similar to [numpy.iinfo](https://docs.scipy.org/doc/numpy/reference/generated/numpy.iinfo.html).

A [`torch.iinfo`](https://pytorch.org/docs/stable/type_info.html#torch.torch.iinfo) provides the following attributes:

| Name | Type | Description                              |
| ---- | ---- | ---------------------------------------- |
| bits | int  | The number of bits occupied by the type. |
| max  | int  | The largest representable number.        |
| min  | int  | The smallest representable number.       |
