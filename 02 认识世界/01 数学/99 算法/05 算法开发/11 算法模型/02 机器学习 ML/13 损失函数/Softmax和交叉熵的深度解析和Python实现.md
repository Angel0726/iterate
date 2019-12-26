---
title: Softmax和交叉熵的深度解析和Python实现
toc: true
date: 2019-11-17
---
# Softmax和交叉熵的深度解析和Python实现



对于多分类问题，老师一定会告诉你在全连接层后面应该加上 Softmax 函数，如果正常情况下（不正常情况指的是类别超级多的时候）用交叉熵函数作为损失函数，你就一定可以得到一个让你基本满意的结果。

现在很多开源的深度学习框架，直接就把各种损失函数写好了（甚至在 Pytorch中 CrossEntropyLoss 已经把 Softmax函数集合进去了），你根本不用操心怎么去实现他们，但是你真的理解为什么要这么做吗？

这篇小文就将告诉你：Softmax 是如何把 CNN 的输出转变成概率，以及交叉熵是如何为优化过程提供度量。为了让读者能够深入理解，我们将会用 Python 一一实现他们。



## Softmax函数



Softmax 函数接收一个 这N维向量作为输入，然后把每一维的值转换成（0，1）之间的一个实数，它的公式如下面所示：



![img](https://mmbiz.qpic.cn/mmbiz_png/ptp8P184xjyA5ZJWqMLjsRvCr604Dric1nf4CsjPENm8Elp2cwLbibrUePoX1DwjTweBPCQNMFPIQH9Y96bzTZicQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



正如它的名字一样，Softmax 函数是一个“软”的最大值函数，它不是直接取输出的最大值那一类作为分类结果，同时也会考虑到其它相对来说较小的一类的输出。



说白了，Softmax 可以将全连接层的输出映射成一个概率的分布，我们训练的目标就是让属于第k类的样本经过 Softmax 以后，第 k 类的概率越大越好。这就使得分类问题能更好的用统计学方法去解释了。



使用 Python，我们可以这么去实现 Softmax 函数：



![img](https://mmbiz.qpic.cn/mmbiz_png/ptp8P184xjyA5ZJWqMLjsRvCr604Dric1PKOQq36vxDOpRc7CBBvr3TYuOEsqMWbFBqdxWdHgQXuqibNj1uVySTw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



我们需要注意的是，在 numpy 中浮点类型是有数值上的限制的，对于`float64`，它的上限是 ![img](https://mmbiz.qpic.cn/mmbiz_png/ptp8P184xjyA5ZJWqMLjsRvCr604Dric1oJHsbsBPG9ibPjEOIOrVImoBx0TvpLfhXWE9AvSEv4nBKLAByPwib4sg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)。对于指数函数来说，这个限制很容易就会被打破，如果这种情况发生了 python 便会返回 `nan`。



为了让 Softmax 函数在数值计算层面更加稳定，避免它的输出出现 `nan`这种情况，一个很简单的方法就是对输入向量做一步归一化操作，仅仅需要在分子和分母上同乘一个常数 `C`，如下面的式子所示

![img](https://mmbiz.qpic.cn/mmbiz_jpg/ptp8P184xjyA5ZJWqMLjsRvCr604Dric1znP58ezb3xQVsYY9UEb501hhOTB4jYNKEfDudLJAawYyVFBIR7wXwg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



理论上来说，我们可以选择任意一个值作为![img](https://mmbiz.qpic.cn/mmbiz_png/ptp8P184xjyA5ZJWqMLjsRvCr604Dric1tw6CukgqZvzdP8EzkIFNEv9r5uuibOLLn0nUCdxB4Wmk8QGaqfibsrjA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)，但是一般我们会选择

![img](https://mmbiz.qpic.cn/mmbiz_png/ptp8P184xjyA5ZJWqMLjsRvCr604Dric1yAWeDNwLpyozLAkqxqM2bQWKR9gMtnTQYOfoW6u7d7TR6fO5j1S4RQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)，通过这种方法就使得原本非常大的指数结果变成0，避免出现 nan的情况。



同样使用 Python，改进以后的 Softmax 函数可以这样写：



![img](https://mmbiz.qpic.cn/mmbiz_png/ptp8P184xjyA5ZJWqMLjsRvCr604Dric1kBDSfc0R0IMVYv3OsqRBk0qE95yicntBAmia5eVHv2ECl69XwcHbzyJg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

##

### **▌****Softmax 函数的导数推倒过程**



通过上文我们了解到，Softmax 函数可以将样本的输出转变成概率密度函数，由于这一很好的特性，我们就可以把它加装在神经网络的最后一层，随着迭代过程的不断深入，它最理想的输出就是样本类别的 One-hot 表示形式。进一步我们来了解一下如何去计算 Softmax 函数的梯度（虽然有了深度学习框架这些都不需要你去一步步推导，但为了将来能设计出新的层，理解反向传播的原理还是很重要的），对 Softmax 的参数求导：



![img](https://mmbiz.qpic.cn/mmbiz_png/ptp8P184xjyA5ZJWqMLjsRvCr604Dric1Cib5J0ZnrTq9uriboicaP3gUaXgCiav6O0LxFGhul9bvZY7bEIywCq7Jeg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



根据商的求导法则，对于 ![img](https://mmbiz.qpic.cn/mmbiz_png/ptp8P184xjyA5ZJWqMLjsRvCr604Dric138ybeRWuLzrZqvrmRU9xtiby0U2oSZPbQ8bVTgJv68ocOoNt1sbibAVA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1) 其导数为 ![img](https://mmbiz.qpic.cn/mmbiz_png/ptp8P184xjyA5ZJWqMLjsRvCr604Dric1Ls5c5cb9mic6Ujwsco1kbLic6CNufHhNS385qyibK8n353kSYRlH0naLA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)  。对于我们来说

![img](https://mmbiz.qpic.cn/mmbiz_png/ptp8P184xjyA5ZJWqMLjsRvCr604Dric1CZvbfBAiazz8kcqoPGNTzOicia0Ml4dDRaUtrKK8uK0cEsIdyBfdlwr4w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)。在 ![img](https://mmbiz.qpic.cn/mmbiz_png/ptp8P184xjyA5ZJWqMLjsRvCr604Dric1QmarD4LZibRHSjv2en0a2CVukZQMlGiaaz0uFeUbXxf3bctJyaIs4VTA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)中，![img](https://mmbiz.qpic.cn/mmbiz_png/ptp8P184xjyA5ZJWqMLjsRvCr604Dric1gZs60dZAChH7WicXl2oUNZIVeRVBTt8c5nQqXRzfZuIu6bCibb2qz2RQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)  一直都是 ![img](https://mmbiz.qpic.cn/mmbiz_jpg/ptp8P184xjyA5ZJWqMLjsRvCr604Dric1GoYticj0ialsWUvbwAT2eibtETmyr5YBaK8HQfCl5icozlX5ibPUKkPBo2w/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)，但是在 ![img](https://mmbiz.qpic.cn/mmbiz_png/ptp8P184xjyA5ZJWqMLjsRvCr604Dric1bbHKMbEWVWibX0Co7f4RdKq3snvwxltDGR8Nj5nVRDvsmSh1Kibj9Kcg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1) 中，当且仅当 ![img](https://mmbiz.qpic.cn/mmbiz_jpg/ptp8P184xjyA5ZJWqMLjsRvCr604Dric1Adve65JTNAynGwMr1t2EXMGvMPotDoJzJRiaxl1xkq47tvYQnbPtCKg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1) 的时候，![img](https://mmbiz.qpic.cn/mmbiz_png/ptp8P184xjyA5ZJWqMLjsRvCr604Dric1H5wJtdaLrZjuEvhNqHLRFQgSuCrSOkURmkszeLkFkgS9dbQPC7tgug/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1) 才为![img](https://mmbiz.qpic.cn/mmbiz_jpg/ptp8P184xjyA5ZJWqMLjsRvCr604Dric1A8zBIeiaiaZ59wCTM4FN4P6rWCLoRUYYVXHvGM0rd0TVAra42ib7yblYQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)。具体的过程，我们看一下下面的步骤：



如果 ![img](https://mmbiz.qpic.cn/mmbiz_jpg/ptp8P184xjyA5ZJWqMLjsRvCr604Dric1Adve65JTNAynGwMr1t2EXMGvMPotDoJzJRiaxl1xkq47tvYQnbPtCKg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)，

![img](https://mmbiz.qpic.cn/mmbiz_png/ptp8P184xjyA5ZJWqMLjsRvCr604Dric12vR2OSNSS15XcmO4VOFibVoKib6h1icb10yZHZUr2Ix9hxF2nqd2mnT6w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)





如果![img](https://mmbiz.qpic.cn/mmbiz_jpg/ptp8P184xjyA5ZJWqMLjsRvCr604Dric1kQzP135XuNoECCxDiav8pq7qLDXy3tQGG63qicYWFXw44ZUDbfEO6z9w/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![img](https://mmbiz.qpic.cn/mmbiz_png/ptp8P184xjyA5ZJWqMLjsRvCr604Dric1f9Cq62yWGz7Aibmq9E89EYTDBmp9XpgiaJMvwta5R86GxpaMbFODXHXg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



所以 Softmax 函数的导数如下面所示：



![img](https://mmbiz.qpic.cn/mmbiz_jpg/ptp8P184xjyA5ZJWqMLjsRvCr604Dric1jsGWt7cbI29t9praY3gjnKIISl7lgv7xvEVUp9wFheQL94S5ianrLqQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



### **▌****交叉熵损失函数**



下面我们来看一下对模型优化真正起到作用的损失函数——交叉熵损失函数。交叉熵函数体现了模型输出的概率分布和真实样本的概率分布的相似程度。它的定义式就是这样：



![img](https://mmbiz.qpic.cn/mmbiz_png/ptp8P184xjyA5ZJWqMLjsRvCr604Dric186CMvYDKo67lNW515AjTgObXdW4SP5T7iaKZKOjOApticdFhNj2nu4VA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



在分类问题中，交叉熵函数已经大范围的代替了均方误差函数。也就是说，在输出为概率分布的情况下，就可以使用交叉熵函数作为理想与现实的度量。这也就是为什么它可以作为有 Softmax 函数激活的神经网络的损失函数。



我们来看一下，在 Python 中是如何实现交叉熵函数的：



![img](https://mmbiz.qpic.cn/mmbiz_png/ptp8P184xjyA5ZJWqMLjsRvCr604Dric1qfRice9wksdGtxOueNlcVyYsZSKUAwEeNpz2OPoJLYcXKbQc1QPNSng/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

##

### **▌****交叉熵损失函数的求导过程**



就像我们之前所说的，Softmax 函数和交叉熵损失函数是一对好兄弟，我们用上之前推导 Softmax 函数导数的结论，配合求导交叉熵函数的导数：



![img](https://mmbiz.qpic.cn/mmbiz_png/ptp8P184xjyA5ZJWqMLjsRvCr604Dric1NguOhdYWLFQtIgVaqkbH6NbkuDCqz7O3oCHHsAVpqcGb3iaAcJrjSWA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



加上 Softmax 函数的导数:



![img](https://mmbiz.qpic.cn/mmbiz_png/ptp8P184xjyA5ZJWqMLjsRvCr604Dric1yiaRaiaiavm2BNlnEJicqVA1BesMcibd0rXIxC0x9OhoAo5Uib8iaiaWAyQF2w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)





y 代表标签的 One-hot 编码，因此 ![img](https://mmbiz.qpic.cn/mmbiz_jpg/ptp8P184xjyA5ZJWqMLjsRvCr604Dric1g0UCjp0icHXxhVq3TO7QvW1ia0VfsJL5TSFeh7nLGicdNccG4sCCy8Wyg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)，并且 ![img](https://mmbiz.qpic.cn/mmbiz_jpg/ptp8P184xjyA5ZJWqMLjsRvCr604Dric1HWGoDCI8hZHSojoJKfeLZcBAVllzmAgo01n4diaJN9Cs8jKh1M0ow3g/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)。因此我们就可以得到：



![img](https://mmbiz.qpic.cn/mmbiz_png/ptp8P184xjyA5ZJWqMLjsRvCr604Dric1cY1Z0MsFPeIkAgYKNPSaRdNP4xOgVmURAdrEQIN0R9pg67qjayZCow/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



可以看到，这个结果真的太简单了，不得不佩服发明它的大神们！最后，我们把它转换成代码：



![img](https://mmbiz.qpic.cn/mmbiz_png/ptp8P184xjyA5ZJWqMLjsRvCr604Dric1VcywAZdHic5oxKFibRkZJVzKZ7ia8vn8WGOzEiaEbWtUtEpGu4ichSbV5Pw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



### **▌****小结**



需要注意的是，正如我之前提到过的，在许多开源的深度学习框架中，Softmax 函数被集成到所谓的 CrossEntropyLoss 函数中。比如 Pytorch 的说明文档，就明确地告诉读者 CrossEntropyLoss 这个损失函数是 Log-Softmax 函数和负对数似然函数（NLLoss）的组合，也就是说当你使用它的时候，没有必要再在全连接层后面加入 Softmax 函数。还有许多文章中会提到 SoftmaxLoss，其实它就是 Softmax 函数和交叉熵函数的组合，跟我们说的 CrossEntropyLoss 函数是一个意思，这点需要读者自行分辨即可。





> ### 原文链接：
>
> https://deepnotes.io/softmax-crossentropy
>
>
>
> GitHub 地址：
>
> https://github.com/parasdahal/deepnet
>
>
>
> ### 参考链接：
>
> The Softmax function and its derivative
>
> Bendersky, E., 2016.
>
> https://eli.thegreenplace.net/2016/the-softmax-function-and-its-derivative/
>
>
>
> CS231n Convolutional Neural Networks for Visual Recognition
>
> Andrej Karpathy, A.K., 2016.
>
> http://cs231n.github.io/convolutional-networks/


# 相关

- [Softmax和交叉熵的深度解析和Python实现](https://mp.weixin.qq.com/s?__biz=MzAwNDI4ODcxNA==&mid=2652249683&idx=1&sn=e12f2e478ca64c14f6c41cfa83ee5efb&chksm=80cc87f6b7bb0ee0b6baa02e4c39b21d7c25d2d7c80693feade4f6507fda9c8309cc0fb791c4&mpshare=1&scene=1&srcid=0729km0v5g0cqqJaJY4PqiYm#rd)
