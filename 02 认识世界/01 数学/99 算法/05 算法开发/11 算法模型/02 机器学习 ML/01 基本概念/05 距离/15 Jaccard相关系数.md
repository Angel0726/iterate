---
title: 15 Jaccard相关系数
toc: true
date: 2019-08-28
---

# Jaccard 相关系数


雅卡尔指数

Jaccard index

Jaccard 相关系数是用于比较样本集的相似性与多样性的统计量。能够量度有限样本集合的相似度，其定义为两个集合交集大小与并集大小之间的比例：

$$
J(A, B)=\frac{|A \cap B|}{|A \cup B|}=\frac{|A \cap B|}{|A|+|B|-|A \cap B|}
$$

如果 A 与 B 完全重合，则定义 J(A,B) = 1。于是有

$$
0 \leq J(A, B) \leq 1
$$

雅卡尔距离（Jaccard distance）则用于量度样本集之间的不相似度，其定义为 1 减去雅卡尔系数，即

$$
d_{J}(A, B)=1-J(A, B)=\frac{|A \cup B|-|A \cap B|}{|A \cup B|}
$$


## 应用例子


**项目场景**：分析一个产品的竞品，譬如 app 的竞品、网站的竞品等等

**项目分析**：简单来说就是竞品分析，竞品分析有很多比较成熟的方法，竞品分析其实和推荐有着很大的相关性。譬如我要分析一个技术网站的竞品有哪些，通俗点说，就是看一个用户经常访问哪些网站、不同类的用户访问网站的偏好是什么、在同类技术网站里与之定位想进，用户人群相似的网站有哪些等等。抽象来看，即可得出两个关键词：用户和物品（或者说物品和竞品）。这个关键词是不是很熟悉？在推荐里我们经常会遇到 item 和 user 之间的相似度，那么竞品分析其实也可以同类化于相似度的计算问题。

**具体做法**：Jaccard相似度。

简单说下公式：

给定两个集合 A 和 B，A和 B 的 Jaccard 相似度 = |A与 B 的交集元素个数| / |A与 B 的并集元素个数|

那么这样一个公式是来应用到竞品分析中的呢？我们假设一个场景：

喜欢博客园的用户也喜欢浏览知乎、CSDN、Github等，喜欢知乎的用户也喜欢浏览 Github、InfoQ、V2EX、SegmentDefault、博客园等，假设我们根据浏览次数来进行排序，得到两个集合，那么我们可以简化为博客园和知乎的竞品分别为：

```
博客园=[知乎、CSDN、Github]
知乎=[Github、InfoQ、V2EX、SegmentDefault、博客园]
```

此时，第一版计算结果：博客园与知乎的 Jaccard 相似度为= 1 / 7=0.14

这是最简单的 Jaccard 相似度计算，然而我们发现，逛博客园的经常逛知乎，且知乎权重很高，但是他们俩的相似度却很低，只有 0.14，看起来好像并不符合常理，于是，我做了点修改，将需要计算的竞品本身也加入集合，即：

```
博客园=[博客园、知乎、CSDN、Github]
知乎=[知乎、Github、InfoQ、V2EX、SegmentDefault、博客园]
```

这样我们再来计算，得到第二版计算结果：博客园与知乎的 Jaccard 相似度 = 3 / 7 = 0.42

为什么我们要将竞品本身考虑进去呢？其实很简单，以博客园为例，我们的目的是找到博客园的竞品，分析出经常浏览博客园的用户还会经常浏览哪些同类技术网站，那么博客园的用户肯定是经常浏览博客园的，这点显而易见，一个物品本身也是自身的竞品。将要分析的竞品本身加入集合后就可避免我们第一次计算时出现的不符合常识的结果。

但是，还得思考一个问题，博客园对知乎的 Jaccard 相似度与知乎对博客园的 Jaccard 相似度应该是一样的吗？

按照前两次计算，我们认为是一样的，因为只是考虑的交集的个数，并没有考虑集合中元素所处的位置因素。然而实际上，集合中的元素位置其实是有先后之分的，按降序排列，即竞品相关度是越来越低的。此时未考虑元素的位置因素似乎也有悖尝试。举个例子：一个经常看博客园的用户，也会经常看知乎，那么一个经常看知乎的用户是否也代表也会经常看博客园呢？这个结论与我们给出的条件是相悖的：一个经常看知乎的用户，相比于博客园，更偏好于 Github。所以我们得到结论：两个竞品 A 和 B，A对 B 的重要性不一定等于 B 对 A 的重要性。

　　所以，我们对此进行进一步改进

```
博客园=[博客园、知乎、CSDN、Github]　　　　　　 ====》博客园 = [1.0,0.6,0.3,0.1]
知乎=[知乎、Github、InfoQ、V2EX、SegmentDefault、博客园]　  ====》知乎 = [1.0,0.55,0.15,0.14,0.11,0.05]
```

(注：竞品本身加入集合我设定权重为 1，其他竞品元素总分为 1)

此时，计算得到第三版计算结果：

博客园对知乎的 Jaccard 相似度 = （ 两者交集的权重得分和/ 两者权重总和 ) * 知乎在博客园集合中所占的权重 = ( 1+0.6+0.1+1+0.55+0.05 / (2+2) ）* 0.6 = （ 3.3 /4 ）* 0.6 = 0.495

知乎对博客园的 Jaccard 相似度 =  （ 两者交集的权重得分和/ 两者权重总和 ) * 博客园在知乎集合中所占的权重 =（ 1+0.6+0.1+1+0.55+0.05 / (2+2) ）* 0.05 = ( 3.3 /4 ）*0.05 = 0.04

由此可得，博客园与知乎的竞品相似度是不相同的，也符合常理





# 相关

- wiki
- [Jaccard相似度在竞品分析中的应用](https://www.cnblogs.com/charlotte77/p/7504251.html)
