---
title: 02 Catalyst 优化器刷新
toc: true
date: 2019-07-02
---
3.2 Catalyst优化器刷新
正如第 1 章所述，Spark SQL引擎如此之快的主要原因之一是 Catalyst 优化器。对于拥有数据库背景的读者，这张图看起来类似于关系数据库管理系统（RDBMS）的逻辑/物理计划和成本模型/基于成本的优化。
其意义在于，相对立即处理查询来说，Spark引擎的 Catalyst 优化器编译并优化了逻辑计划，而且还有一个能够确保生成最有效的物理计划的成本优化器。如下图：正如前几章所述，Spark SQL引擎既有基于规则的优化，也有基于成本的优化，包括（但不仅限于）谓词下推和列精简。针对 Apache Spark 2.2版本，jira项目[SPARK-16026]Cost-based Optimizer Framework（https://issues.apache.org/jira/browse/SPARK-16026）就像一张“通票”，除广播连接选择外，还实现了基于成本的优化器框架。更多的信息请参阅 Design Specification of Spark Cost-Based Optimization（http://bit.ly/2li1t4T）。
作为 Tungsten 项目的一部分，其通过生成字节码进一步改进性能（代码生成或 codegen）而不需要解释每一行数据。在 1.2.6节中可以找到更多有关 Tungsten 的细节。
如前所述，优化器是基于功能的编程结构，并且其设计有两个目的：为了便于对 Spark SQL添加新的优化技术和功能，以及允许外部开发人员扩展优化器（如添加数据源特定规则、支持新的数据类型等等）。更多的信息，请参阅 Michael Armbrust的精彩演讲 Structuring Spark：SQL DataFrames，Datasets，and Streaming（http://bit.ly/2cJ508xCatalyst）。
对于 Catalyst 优化器更深入的理解，请参阅 Deep Dive into Spark SQL’s Catalyst Optimizer（http://bit.ly/2bDVB1T）。
另外，有关 Tungsten 项目的更多信息，请参阅 Project Tungsten：Bringing Apache Spark Closer to Bare Metal（http://bit.ly/2bQIlKY）以及 Apache Spark as a Compiler：Joining a Billion Rows per Second on a Laptop（http://bit.ly/2bDWtnc）。
