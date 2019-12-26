---
title: 21 PyTorch 与 Torch
toc: true
date: 2019-12-06
---

# PyTorch 与 Torch

Torch 是一个与 Numpy 类似的张量（Tensor）操作库，与 Numpy 不同的是 Torch 对 GPU 支持的很好，Lua 是 Torch 的上层包装。<span style="color:red;">嗯，这个之前知道是基于 Torch 的，但是不知道 Torch 是一个与 Numpy 类似的张量操作库。。不知道 Torch 与 Numpy 是一个等级的。而且，之前我以为 Torch 的底层是 Lua 写的，实际上是 Lua 是 Torch 的上层包装。</span>

<span style="color:red;">嗯，突然想知道 PyTorch 是与 Lua 没有关系了是吗？是相当于用 python 代替了 Lua 吗？</span>


PyTorch 和 Torch 使用包含所有相同性能的 C 库：TH, THC, THNN, THCUNN，并且它们将继续共享这些库。<span style="color:red;">嗯 TH,THC,THNN,THCUNN 这些是什么？而且，非常想知道是怎么用 c 来实现的。</span>

这样的回答就很明确了，其实 PyTorch 和 Torch 都使用的是相同的底层，只是使用了不同的上层包装语言。<span style="color:red;">哇塞，这个回答了我一直存在的疑问，OK。</span>

注：LUA 虽然快，但是太小众了，所以才会有 PyTorch 的出现。<span style="color:red;">嗯。</span>
