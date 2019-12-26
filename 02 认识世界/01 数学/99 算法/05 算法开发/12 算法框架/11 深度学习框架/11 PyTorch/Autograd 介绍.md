---
title: Autograd 介绍
toc: true
date: 2019-12-06
---
Autograd 实现是非常重要的一个功能。变量和功能是相互关联的，可以建立一个无环图，编码一个完整的历史的计算，并且每个变量都有一个 grad_fn。<span style="color:red;">什么意思？Autograd 到底是干什么的？每个变量都有一个 grad_fn 是什么意思？</span>
