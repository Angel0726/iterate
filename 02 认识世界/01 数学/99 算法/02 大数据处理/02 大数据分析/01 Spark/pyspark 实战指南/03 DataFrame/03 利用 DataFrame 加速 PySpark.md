---
title: 03 利用 DataFrame 加速 PySpark
toc: true
date: 2019-07-02
---
.3 利用 DataFrame 加速 PySpark
DataFrame和 Catalyst 优化器（以及 Tungsten 项目）的意义是在和非优化的 RDD 查询比较时增加 PySpark 查询的性能。如下图所示，引入 DataFrame 之前，python查询速度普遍比使用 RDD 的 Scala 查询慢（后者快两倍）。通常情况下，这种查询性能的降低源于 python 和 JVM 之间的通信开销：
资料来源：Introducing DataFrames in Apache-spark for Large Scale Data Science（http://bit.ly/2blDBI1）
使用 DataFrame，过去不仅有明显的 python 性能改进，现在还有 python、Scale、SQL和 R 之间的性能校验。重要的是同时要注意，利用 DataFrame，PySpark往往明显加快，也有一些例外。最典型的是 python UDF的使用，导致在 python 和 Java 虚拟机之间的往返通信。请注意，这将是最坏的情况，如果计算基于 RDD 来做，情况将会是相似的。
即使 Catalyst 优化器的代码库是用 Scala 写的，python也可以利用 Spark 中性能优化的优势。通常，这是一个大约 2000 行代码的 python 包装，可以允许 PySpark DataFrame的查询变得快很多。
总之，python DataFrame和 SQL、Scala DataFrame以及 R DataFrame都能够利用 Catalyst 优化器（按照以下更新的图）：更多的信息，请参阅博客文章 Introducing DataFrames in Apache Spark for Large Scale Data Science（http://bit.ly/2blDBI1），还有 Reynold Xin的 Spark 峰会 2015 演讲 From DataFrames to Tungsten：A Peek into Spark’s Future（http://bit.ly/2bQN92T）。
