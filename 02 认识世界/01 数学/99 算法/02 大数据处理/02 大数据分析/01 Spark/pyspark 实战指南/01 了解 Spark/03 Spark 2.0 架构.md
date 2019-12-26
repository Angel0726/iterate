---
title: 03 Spark 2.0 架构
toc: true
date: 2019-07-02
---
1.3 Spark 2.0的架构
Apache Spark 2.0的引入是 Apache Spark项目基于过去两年平台开发经验近期所发布的主要版本更新：
资料来源：Apache Spark 2.0：Faster，Easier and Smarter（http://bit.ly/2ap7qd5）
Apache Spark 2.0发布的三个重要主题包括性能增强（通过 Tungsten Phase 2）、引入结构化流以及统一 Dataset 和 DataFrame。虽然 Dataset 目前仅在 Scala 和 Java 中可用，但我们仍然将其描述为 Spark 2.0的一部分。有关 Apache Spark 2.0的更多信息，请参阅由 Spark 的核心提交者提供的以下介绍：Reynold Xin的 Apache Spark 2.0：Faster，Easier，and Smarter（http://bit.ly/2ap7qd5）；Michael Armbrust的 Structuring Spark：DataFrames，Datasets，and Streaming（http://bit.ly/2ap7qd5）；Tathagata Das的 A Deep Dive into Spark Streaming（http://bit.ly/2aHt1w0）；Joseph Bradley的 Apache Spark MLlib2.0 Preview：Data Science and Production（http://bit.ly/2aHrOVN）。
1.3.1 统一 Dataset 和 DataFrame
在上一节中，我们指出 Dataset 仅在 Scala 或 Java 中可用。但是，我们提供了以下背景文字来让你更好地了解 Spark 2.0的针对性。
Dataset于 2015 年作为 Apache Spark 1.6版本的一部分推出。Dataset的目标是提供一个类型安全的编程接口。这允许开发人员使用编译时类型安全（生产应用程序可以在运行之前检查错误）处理半结构化数据（如 JSON 或键值对）。python不实现 Dataset API的部分原因是 python 不是一种类型安全的语言。
同样重要的是，Dataset API包含高级别域的特定语言操作，如 sum（）、avg（）、join（）和 group（）。这种最新的特性意味着不仅具有传统 Spark RDD的灵活性，而且代码也更容易表达、读取和写入。与 DataFrame 类似，Dataset可以通过将表达式和数据字段暴露给查询计划器并借助 Tungsten 的快速内存编码来运用 Spark 的 Catalyst 优化器。
Spark API的历史演变如下图所示，注意从 RDD 到 DataFrame 到 Dataset 的过程：



资料来源：Webinar Apache Spark1.5：What is the difference between a DataFrame and a RDD？（http://bit.ly/29JPJSA）
DataFrame和 Dataset API的统一使创建向后兼容的重大改变成为可能。这是 Apache Spark 2.0成为主要版本（相对 1.x这种重大改变很少的次要版本而言）的主要原因之一。从下图中可以看出，DataFrame和 Dataset 都属于新的 Dataset API，作为 Apache Spark 2.0的一部分被引入进来：
资料来源：A Tale of Three Apache Spark APIs：RDDs，DataFrames，and Datasets（http://bit.ly/2accSNA）
如前所述，Dataset API提供了一种类型安全的面向对象的编程接口。通过将表达式和数据字段暴露给查询计划器和 Project Tungsten的快速内存编码，Dataset可以利用 Catalyst 优化器。但是现在 DataFrame 和 Dataset 已统一为 Apache Spark 2.0的一部分，DataFrame现在是未类型化的 Dataset API的一个别名。进一步来说：
1.3.2 SparkSession介绍
在过去，你可能会使用 SparkConf、SparkContext、SQLContext和 HiveContext 来分别执行配置、Spark环境、SQL环境和 Hive 环境的各种 Spark 查询。SparkSession本质上是这些环境的组合，包括 StreamingContext。


例如，过去我们这么写：
现在可以这样写：
或者这样写：
SparkSession现在是读取数据、处理元数据、配置会话和管理集群资源的入口。
1.3.3 Tungsten Phase 2
当项目开始时，对计算机硬件环境的基本观察是，尽管内存、磁盘和网络接口（在一定程度上）的性价比有所改善，但 CPU 的性价比并非如此。虽然硬件制造商可以在每个插槽中放置更多的核心（即通过并行化提高性能），但是实际核心速度没有显著的改进。
Project Tungsten于 2015 年推出，旨在为 Spark 引擎的性能提高做出显著改进。这些改进的第一阶段侧重于以下几个方面：内存管理和二进制处理：利用应用程序语义来显式管理内存，并消除 JVM 对象模型和垃圾回收的开销。高速缓存感知计算：利用存储器层次结构的算法和数据结构。代码生成：使用代码生成来利用现代编译器和 CPU。
下图是更新的 Catalyst 引擎，用于表示 Dataset 所包含的内容。如图右侧所示（成本模型右侧），代码生成的使用基于选定的物理计划生成基础 RDD：
资料来源：Structuring Spark：DataFrames，Datasets and Streaming（http://bit.ly/2cJ508x）
推进全阶段代码生成是 Tungsten Phase 2的一个部分。也就是说，Spark引擎现在将在编译时为整个 Spark 阶段生成字节码，而不是仅为特定的作业或任务生成字节码。围绕这些改进的主要方面包括：没有虚拟函数调度：减少了多次 CPU 调用，这在调度数十亿次运算时可能对性能产生深远的影响。存储器中的中间数据对比 CPU 寄存器中的中间数据：Tungsten Phase 2将中间数据放入 CPU 寄存器。从 CPU 寄存器而不是从存储器获得数据使读取数据的周期数得到了数量级的提升。循环展开和 SIMD：优化 Apache Spark的执行引擎，以利用现代编译器和 CPU 有效地编译和执行简单 for 循环（而不是复杂的函数调用图）。
更多有关 Project Tungsten的深入评论请参阅：
·Apache Spark Key Terms，Explained（https://databricks.com/blog/2016/06/22/apache-spark-key-terms-explained.html）。
·Apache Spark as a Compiler：Joining a Billion Rows per Second on a Laptop（https://databricks.com/blog/2016/05/23/apache-spark-as-a-compiler-joining-a-billion-rows-per-second-on-a-laptop.html）。
·Project Tungsten：Bringing Apache Spark Closer to Bare Metal（https://databricks.com/blog/2015/04/28/project-tungsten-bringing-spark-closer-to-bare-metal.html）。
1.3.4 结构化流
引用 Reynold Xin在 Spark Summit East2016中说过的：“执行流分析的最简单的方法是不必考虑流”。
这是构建结构化流的基础。虽然流媒体功能强大，但关键问题之一是流可能难以构建和维护。Uber、Netflix和 Pinterest 等公司都有在生产中运行的 Spark Streaming应用程序，他们也有专门的团队来确保系统高度可用。如果需要更高级的 Spark Streaming的内容，请参考 Spark Streaming：What Is It and Who’s Using It？（http://bit.ly/1Qb10f6）。
如前文所述，当操作 Spark Streaming（以及用于此的任何流系统）时，有许多方面可能出错，包括（但不限于）后期事件，到最终数据源的部分输出，失败时的状态恢复和分布式读/写：
资料来源：A Deep Dive into Structured Streaming（http://bit.ly/2aHt1w0）
因此，为了简化 Spark Streaming，现在有一个单一 API 可以解决 Apache Spark 2.0版本中的批处理和流式处理。更简洁地说，高级流 API 现在构建在 Apache Spark SQL引擎之上。它运行的查询与使用 Dataset、DataFrame时完全相同，为你提供所有性能和优化以及事件时间、窗口、会话（session）、源（source）和槽（sink）等方面的优势。
1.3.5 连续应用
总而言之，Apache Spark 2.0不仅统一了 DataFrame 和 Dataset，而且统一了流、交互式和批处理查询。产生了一整套新的用例，包括将数据聚合到流中并使用传统的 JDBC/ODBC提供它，在运行时更改查询，或在多种场景中构建和应用 ML 模型的延迟用例：



资料来源：Apache Spark Key Terms，Explained（https://databricks.com/blog/2016/06/22/apache-spark-key-terms-explained.html）
同时，你现在可以构建端到端的连续应用程序，在其中你可以对实时数据进行批处理，执行 ETL、生成报告、更新或跟踪流中的特定数据。有关连续应用程序的更多信息，请参阅 Matei Zaharia的博客文章 Continuous Applications：Evolving Streaming in Apache Spark2.0-A foundation for end-to-end real-time applications（http://bit.ly/2aJaSOr）。
