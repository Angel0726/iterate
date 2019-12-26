---
title: 01 什么是 Apache Spark
toc: true
date: 2019-07-02
---
# 什么是 Apache Spark

Apache Spark是一个开源的、强大的分布式查询和处理引擎。它提供 MapReduce 的灵活性和可扩展性，但速度明显更高：当数据存储在内存中时，它比 Apache Hadoop快 100 倍，访问磁盘时高达 10 倍。

Apache Spark允许用户读取、转换、聚合数据，还可以轻松地训练和部署复杂的统计模型。Java、Scala、python、R和 SQL 都可以访问 Spark API。Apache Spark可用于构建应用程序，或将其打包成为要部署在集群上的库，或通过笔记本（notebook）（例如 Jupyter、Spark-Notebook、Databricks notebooks和 Apache Zeppelin）交互式执行快速的分析。

Apache Spark提供的很多库会让那些使用过 python 的 pandas 或 R 语言的 data.frame或者 data.tables的数据分析师、数据科学家或研究人员觉得熟悉。非常重要的一点是，虽然 Spark DataFrame会让 pandas 或 data.frame、data.tables用户感到熟悉，但是仍有一些差异，所以不要期望过高。具有更多 SQL 使用背景的用户也可以用该语言来塑造其数据。此外，Apache Spark还提供了几个已经实现并调优过的算法、统计模型和框架：为机器学习提供的 MLlib 和 ML，为图形处理提供的 GraphX 和 GraphFrames，以及 Spark Streaming（DStream和 Structured）。Spark允许用户在同一个应用程序中随意地组合使用这些库。

Apache Spark可以方便地在本地笔记本电脑上运行，而且还可以轻松地在独立模式下通过 YARN 或 Apache Mesos于本地集群或云中进行部署。它可以从不同的数据源读取和写入，包括（但不限于）HDFS、Apache Cassandra、Apache HBase和 S3：

资料来源：Apache Spark is the smartphone of Big Data（http://bit.ly/1QsgaNj）
