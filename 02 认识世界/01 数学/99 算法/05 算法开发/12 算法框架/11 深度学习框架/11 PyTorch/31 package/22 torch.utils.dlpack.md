---
title: 22 torch.utils.dlpack
toc: true
date: 2019-06-29
---
# TORCH.UTILS.DLPACK

- `torch.utils.dlpack.``from_dlpack`(*dlpack*) → Tensor

  Decodes a DLPack to a tensor.Parameters**dlpack** – a PyCapsule object with the dltensorThe tensor will share the memory with the object represented in the dlpack. Note that each dlpack can only be consumed once.

- `torch.utils.dlpack.``to_dlpack`(*tensor*) → PyCapsule

  Returns a DLPack representing the tensor.Parameters**tensor** – a tensor to be exportedThe dlpack shares the tensors memory. Note that each dlpack can only be consumed once.
