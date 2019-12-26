---
title: 10 Spark 数据集 Dataset API
toc: true
date: 2019-07-02
---
3.10 Spark数据集（Dataset）API
在进行有关 Spark DataFrame的讨论之后，我们来快速回顾 Spark 数据集 API。Apache Spark 1.6介绍了 Spark Dataset的目标，该目标是为域对象提供一个允许用户轻松表示转换的 API，同时还提供了 Spark SQL强大的执行引擎的性能和优势。作为 Spark 2.0版本（如下图说明）中的一部分，DataFrame API被合并到了 Dataset API之中，从而统一了所有的库的数据处理能力。由于这种统一，开发人员现在需要学习和记忆的概念变少了，并且使用了一种单一高层和类安全的 API——称为数据集：
从概念上说，Spark DataFrame是通用对象 Dataset[Row]集合的一个别名（alias），Row是一个通用的非类型化（untyped）JVM对象。相反，Dataset是一个强类型的 JVM 对象集合，通过 Scala 或者 Java 定义的案例类决定。最后一点尤为重要，因为这意味着由于缺乏类型增强的优势，PySpark不支持 Dataset API。注意，对于 PySpark 中不支持的 Dataset API，可以通过转化为 RDD 或者使用 UDF 来访问。更多信息，请参阅 jira[SPARK-13233]：python Dataset，http://bit.ly/2dbfoFT。
