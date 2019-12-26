---
title: MIT 自动驾驶系统也能拨开迷雾
toc: true
date: 2019-11-17
---
# MIT 自动驾驶系统也能拨开迷雾

论文地址：

http://web.media.mit.edu/~guysatat/fog/materials/TowardsPhotographyThroughRealisticFog.pdf



利用水箱和不同浓度的雾来测试系统：

<center>

![mark](http://images.iterate.site/blog/image/20191101/tdHKSObuLnGc.png?imageslim)

</center>


雾天驾驶一直是自动驾驶汽车导航系统面对的重要问题。麻省理工学院 (MIT) 的研究团队开发了一套基于LIDAR的深度感知系统，就算物体隐藏在人类肉眼难以望穿的浓雾背后，系统也能测定物体的距离和形状。

许多自动驾驶系统使用的是可见光，与基于雷达的系统相比而言分辨率更高，识别路标和车道标记的能力也更强。而基于可见光的系统在能见度偏低的驾驶条件中，会受到严重的局限——

晴朗的天气里，光线从射出到返回的时间可以准确反映物体的距离；但在雾中，传感器收到的光线很可能是经水滴反射而来，不一定是从汽车需要避让的障碍物身上返回。

伽马分布，OT=optical thickness：

<center>

![mark](http://images.iterate.site/blog/image/20191101/N6M4BCVtqnft.png?imageslim)

</center>



MIT团队利用统计学来解决这一问题。雾中水滴反射光线的形态取决于雾有多浓，平均来说，光线进入浓雾的程度要低于其进入薄雾的程度。不过，研究人员证明了，不论是多么重的雾，反射光线到达传感器所需的时间都与伽马分布相贴合。

与钟形的高斯分布相比，伽马分布要复杂一些，呈现的形状更为多样，不对称的情况很多。但与高斯分布相似的是，伽马分布同样可以用两个变量就完全表示出来。MIT团队通过估算这些变量得出分布，用以将被雾反射的光线过滤出来。这样一来，物体距离测定的准确度，便不会受到大雾天气的过度影响。

不同浓度雾中的人形成像：

<center>

![mark](http://images.iterate.site/blog/image/20191101/hofwNXkegkQn.png?imageslim)

</center>



关键的一点是，MIT系统会对传感器的1,024枚像素做出1,024个伽马分布。系统可以在不同浓度的雾中稳定发挥的原因，便是每一枚像素看到的并不完全是同一片雾。





# 相关

- [MIT：自动驾驶系统也能拨开迷雾，看清物体](https://mp.weixin.qq.com/s?__biz=MzIzNjc1NzUzMw==&mid=2247495843&idx=4&sn=db0fcef1bc290b84ac0c185ade54cdf9&chksm=e8d047d1dfa7cec7e7b55ac10d6ea66b8a95075ec277d5dbf3e55e55aeb537522cdbc5139b73&mpshare=1&scene=1&srcid=0425FWZS3a3Sz84DruAv0Mqt#rd)
