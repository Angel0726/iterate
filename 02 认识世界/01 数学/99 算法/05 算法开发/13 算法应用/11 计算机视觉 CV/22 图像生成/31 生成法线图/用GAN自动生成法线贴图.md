---
title: 用GAN自动生成法线贴图
toc: true
date: 2019-11-17
---
>
> 如果设计工具能根据简单的素描自动生成法线贴图，那将能够为图形设计师提供很大的帮助。近日，香港城市大学、中国科学技术大学、大连理工大学和湖南大学四所高校的研究者提出了一种使用生成对抗网络的法线贴图生成方法。该研究的论文已被将于 5 月 15-18 日在加拿大蒙特利尔举办的 ACM SIGGRAPH 交互式 3D 图形和游戏研讨会（i3D）接收。



法线贴图（normal map）在学术研究和商业生产中都有至关重要作用。对于形状重建、表面编辑、纹理贴图和拟真表面渲染等很多图形应用而言，法线贴图非常重要。表面法线贴图是形状的高阶差分信息，因此在设计流程的早期阶段，人类不易准确推理得到。



在各种表示方法中，素描（sketch）能让设计师比较直观地传达自己的设计概念，因为这种方式有很好的多样性、灵活性、简洁性和效率。这也是一种用于展现形状和其它几何信息的常用媒介。使用素描将 3D 信息传递到 2D 域中是人们常常使用的自然方法。因为表面法线是编码 3D 信息的最直接的方法之一，所以「素描到法线」是将 2D 概念投射到 3D 空间的主要释义方法，这在卡通着色、数字表面建模、游戏场景增强等方面有广泛的应用。根据素描自动推导法线贴图有望成为图形设计师的有用工具。



近些年来，研究界已经见证了深度神经网络在各种不同领域的优良能力。深度神经网络已经成为了很多问题背后的常用解决方案，尤其是与图像相关的难题。具体而言，基于 GAN 的方法已经在一系列图像生成问题上取得了出色的表现。更具体来说，对于基于引导的（guidance-based）图像生成，GAN 在传统的深度学习方法的基础上表现出了显著的提升。因为法线信息和素描曲线在图像域中都有良好的表征，所以根据素描推导法线贴图可以使用深度神经网络来实现。



在本论文中，我们提出了一种交互式的生成系统，其使用了一个深度神经网络框架来根据输入素描生成法线贴图。在我们的系统中，素描到法线贴图生成问题被当作了一个图像转译问题——使用一个基于 GAN 的框架将素描图像「转译」成法线贴图图像。为了增强输入素描和所生成的法线贴图之间的对应关系，我们整合了一种条件 GAN 框架，其可以根据条件引导（conditional guidance）[20] 来生成和鉴别图像。我们在生成器中使用了 U-net [24] 架构来在生成过程中传递平滑的信息流，从而进一步提升像素层面的对应关系。我们在我们的实现中使用了 Wasserstein 距离，以为网络更新提供更有效的引导和降低训练过程的不稳定性。



因为素描是形状的高度简化的表示，所以对于单张输入素描，可能会有多种形状释义或可能的法线贴图。我们依靠用户来解决这种模糊性问题。为了做到这一点，我们提供了一个用户界面，让用户可以直接在输入素描中提供特定点的法线信息，从而引导法线贴图生成。这样的界面也能扩大法线贴图的设计选择范围。我们的系统非常高效，可以根据输入素描和点提示信息实时地生成法线贴图。



我们进行了广泛的定量和定性实验并与其它方法进行了比较，结果表明了我们的方法的有效性。我们在三种类别的数据上进行了评估，并与 pix2pix [14] 和 Lun et al. [19] 等其它方法进行了比较，结果表明我们的方法在生成低误差法线贴图方面能力出色。在输入素描的变化不断增多的实验中，通过逐步增多提示点并评估我们方法的稳健性，我们验证了该方法的用户交互能力。我们的方法也可以根据全新的人工绘制的素描而得到看起来合理的结果。用户研究进一步证明了我们的方法在用户感知方面的优势。



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8kvbFO2qbs7Apc1Ydbf0FyLqpFIrh4VibzAqvDFNibxc0GaduNKNEibxhWh274N5z48PfeK5amBDsvA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*图 1：我们的方法的整体工作流程。我们提出的生成器网络将单张输入素描转换成法线贴图，其中仅使用很少或不使用用户干预。这里我们使用了 RGB 通道来表示 3D 法线分量。所生成的法线贴图可用于多种应用，比如重设表面光照、纹理贴图等。比如这里我们将法线贴图用于冯氏着色（Phong shading）。*



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8kvbFO2qbs7Apc1Ydbf0FyCjZHCLSU2jYk3BPIAhgTW4YGMvsW5KqFOJvTYHHQxDNfkV3n11ovJQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*图 2：我们的方法的网络结构。在图左边是一个训练数据样本，其包含一张素描输入图像、一个点掩模（point mask）和基本真值的法线贴图。对于被选择的点，我们将其在掩模 (2) 中对应的值设为 1，并将来自法线贴图 (3) 的对应点法线复制到素描 (1) 中。我们将素描输入 (1) 和点掩模 (2) 连接起来作为生成器 G 的输入，以求取中间的法线贴图 (4)；然后再将该中间法线贴图与素描和掩模一起作为鉴别器 D 的输入，以验证中间法线贴图 (4) 与基本真值 (3) 相比在像素层面上的「真实性」。这个鉴别信息可在训练阶段引导生成器 G 更新自己的参数。在测试阶段，素描输入和点掩模只作为生成器 G 的输入，其输出会被导出为最终所生成的法线贴图。每个层模块之上或之下的数字表示层的数量，每个模块左侧的数字表示相应网络层的空间大小。*



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8kvbFO2qbs7Apc1Ydbf0FyINfBb899oicicsUNSibw3uf6SkJCKIgAoKsJE5G4ficNhvialSsz8fOicN7g/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*图 3：我们的用户界面。用户可以在画板（右侧）选择位置，然后使用法线空间（左侧）为它们分配所需的法线。*



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8kvbFO2qbs7Apc1Ydbf0FyibHOXAnxlW0Tn5ASS9y2IiaZ19nE2JkdmZQ0OXFY1NSJhgarC7MuuAiaw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*表 1：不同方法的误差情况。我们将 pix2pix [14]、Lun et al. [19] 和我们的方法的结果与基本真值法线贴图进行了比较。这里给出的值是生成图像（256×256 像素）中法线区域在像素层面上的平均差异。*



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8kvbFO2qbs7Apc1Ydbf0FyCJwUkn0oXUmIAorCFEP04KeafY63vs11Vk09nUKvC8UaKJzjvYk82g/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*图 4：使用我们的方法生成的法线贴图示例。每组的右上角是素描输入，每组的右下角是与基本真值比较所得到的对应误差图。我们可视化了所生成的法线贴图的角损失（angular loss），其中红色通道对应所生成的法线贴图的误差，白色是零误差。*



**论文：使用深度神经网络的交互式的基于素描的法线贴图生成（Interactive Sketch-Based Normal Map Generation with Deep Neural Networks）**





![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8kvbFO2qbs7Apc1Ydbf0FyMf0O2F78Nm3FBkywgeaGRxqibAbFjJMluQGS0yTkGKpS1Qd2EaQvN2w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



论文地址：http://sweb.cityu.edu.hk/hongbofu/doc/sketch2normal_i3D2018.pdf



高质量法线贴图（normal map）是用于表示复杂形状的重要媒介。在本论文中，我们提出了一种使用深度学习技术生成法线贴图的交互式系统。我们的方法使用了生成对抗网络（GAN），能为素描输入生成高质量的法线贴图。此外，我们还能通过整合用户在所选择的点上指定的法线来提升我们系统的交互能力。我们的方法可以实时生成高质量法线贴图。我们进行了全面的实验，结果表明了我们的方法的有效性和稳健性。我们还进行了全面的用户研究，结果表明：在用户感知方面，与其它方法相比，我们的方法所生成的法线贴图与基本真值（ground truth）之间的差异更小。![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8Zfpicd40EribGuaFicDBCRH6IOu1Rnc4T3W3J1wE0j6kQ6GorRSgicib0fmNrj3yzlokup2jia9Z0YVeA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)


# 相关

- [学界 | 用GAN自动生成法线贴图，让图形设计更轻松](https://mp.weixin.qq.com/s?__biz=MzA3MzI4MjgzMw==&mid=2650741123&idx=5&sn=4b17295bccea7babb1f42af92c1e7c27&chksm=871addfdb06d54eb97bf13bbca54113edd09399434199c6aa7c9420124d07f653274047cbf23&mpshare=1&scene=1&srcid=0421YWT6eulp3KnSe1cqwrQE#rd)
