---
title: 01 GraphFrames 介绍
toc: true
date: 2019-07-02
---
# GraphFrames介绍

GraphFrames利用 Apache Spark DataFrame的强大功能来支持一般图形处理。具体来说，点和边由 DataFrame 表示，允许我们存储每个节点和边的任意数据。虽然 GraphFrames 与 Spark 的 GraphX 库类似，但他们之间有一些关键的区别，包括：

·GraphFrames利用了 DataFrame API的性能优化和简单性。

·通过使用 DataFrame API，GraphFrames现在具有 python、Java和 Scala 的 API。GraphX只能通过 Scala 访问；现在所有的算法都可以在 python 和 Java 中使用。

·请注意，在撰写本文时，GraphFrames使用 python3.x的话会有 bug，因此我们将使用 python2.x。

在撰写本书时，GraphFrames的版本是 0.3。它可以作为 Spark 软件包（http://spark-packages.org）从 https://spark-packages.org/packages/graphframes/graphframes上下载。关于 GraphFrames 的更多信息，请参考 Introducing GraphFrames（https://databricks.com/blog/2016/03/03/introducing-graphframes.html）。
