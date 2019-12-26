---
title: 02 Spark 作业 和 API
toc: true
date: 2019-07-02
---
1.2 Spark作业和 API
在本节中，我们将简要介绍 Apache Spark作业（job）和 API。这为 Spark 2.0架构的后续部分提供了必要的基础。
1.2.1 执行过程
任何 Spark 应用程序都会分离主节点上的单个驱动进程（可以包含多个作业），然后将执行进程（包含多个任务）分配给多个工作节点，如下图所示：
驱动进程会确定任务进程的数量和组成，这些任务进程是根据为指定作业生成的图形分配给执行节点的。注意，任何工作节点都可以执行来自多个不同作业的多个任务。
Spark作业与一系列对象依赖相关联，这些依赖关系是以有向无环图（DAG）的方式组织的，例如从 Spark UI生成的以下示例。基于这些，Spark可以优化调度（例如确定所需的任务和工作节点的数量）并执行这些任务。


有关 DAG 调度器的更多信息，请参考 http://bit.ly/29WTiK8。
1.2.2 弹性分布式数据集
弹性分布式数据集（简称 RDD）是不可变 Java 虚拟机（JVM）对象的分布式集合，Apache Spark就是围绕着 RDD 而构建的。我们使用 python 时，尤为重要的是要注意 python 数据是存储在这些 JVM 对象中的。更多的内容将在随后的第 2 章和第 3 章讨论。这些对象允许作业非常快速地执行计算。对 RDD 的计算依据缓存和存储在内存中的模式进行：与其他传统分布式框架（如 Apache Hadoop）相比，该模式使得计算速度快了一个数量级。
同时，RDD会给出一些粗粒度的数据转换（例如 map（……）、reduce（……）和 filter（……），详见第 2 章），保持 Hadoop 平台的灵活性和可扩展性，以执行各种各样的计算。RDD以并行方式应用和记录数据转换，从而提高了速度和容错能力。通过注册这些转换，RDD提供数据沿袭——以图形形式给出的每个中间步骤的祖先树。这实际上保护 RDD 免于数据丢失——如果一个 RDD 的分区丢失，它仍然具有足够的信息来重新创建该分区，而不是简单地依赖复制。更多数据沿袭信息参见：http://ibm.co/2ao9B1t。
RDD有两组并行操作：转换（返回指向新 RDD 的指针）和动作（在运行计算后向驱动程序返回值）。我们将在后面的章节中更详细地介绍这些内容。请参阅 Spark 编程指南，获取最新的转换和动作列表：http://spark.apache.org/docs/latest/programming-guide.html#rdd-operations。
某种意义上来说，RDD转换操作是惰性的，因为它们不立即计算其结果。只有动作执行了并且需要将结果返回给驱动程序时，才会计算转换。该延迟执行会产生更多精细查询：针对性能进行优化的查询。这种优化始于 Apache Spark的 DAGScheduler——面向阶段的调度器，使用如上面截图中所示的阶段进行转换。由于具有单独的 RDD 转换和动作，DAGScheduler可以在查询中执行优化，包括能够避免 shuffle 数据（最耗费资源的任务）。
有关 DAGScheduler 和优化（特别是窄或宽依赖关系）的更多信息，有一个很好的参考是《Effective Transformations》第 5 章（https://smile.amazon.com/High-Performance-Spark-Practices-Optimizing/dp/1491943203）。
1.2.3 DataFrame
DataFrame像 RDD 一样，是分布在集群的节点中的不可变的数据集合。然而，与 RDD 不同的是，在 DataFrame 中，数据是以命名列的方式组织的。如果你熟悉 python 的 pandas 或者 R 的 data.frames，这是一个类似的概念。
DataFrame旨在使大型数据集的处理更加容易。它们允许开发人员对数据结构进行形式化，允许更高级的抽象。在这个意义上来说，DataFrame与关系数据库中的表类似。DataFrame提供了一个特定领域的语言 API 来操作分布式数据，使 Spark 可以被更广泛的受众使用，而不只是专门的数据工程师。
DataFrame的一个主要优点是，Spark引擎一开始就构建了一个逻辑执行计划，而且执行生成的代码是基于成本优化程序确定的物理计划。与 Java 或者 Scala 相比，python中的 RDD 是非常慢的，而 DataFrame 的引入则使性能在各种语言中都保持稳定。
1.2.4 Dataset
Spark 1.6中引入的 Spark Dataset旨在提供一个 API，允许用户轻松地表达域对象的转换，同时还提供了具有强大性能和优点的 Spark SQL执行引擎。遗憾的是，在写这本书时，Dataset仅在 Scala 或 Java 中可用。当它们在 PySpark 中可用时，我们再在以后的版本中讨论。
1.2.5 Catalyst优化器
Spark SQL是 Apache Spark最具技术性的组件之一，因为它支持 SQL 查询和 DataFrame API。Spark SQL的核心是 Catalyst 优化器。优化器基于函数式编程结构，并且旨在实现两个目的：简化向 Spark SQL添加新的优化技术和特性的条件，并允许外部开发人员扩展优化器（例如，添加数据源特定规则，支持新的数据类型等等）：


详细信息，请查看 Deep Dive into Spark SQL’s Catalyst Optimizer（http://bit.ly/271I7Dk）和 Apache Spark DataFrames：Simple and Fast Analysis of Structured Data（http://bit.ly/29QbcOV）。
1.2.6 钨丝计划
Tungsten（钨丝）是 Apache Spark执行引擎项目的代号。该项目的重点是改进 Spark 算法，使它们更有效地使用内存和 CPU，使现代硬件的性能发挥到极致。
该项目的工作重点包括：
·显式管理内存，以消除 JVM 对象模型和垃圾回收的开销。
·设计利用内存层次结构的算法和数据结构。
·在运行时生成代码，以便应用程序可以利用现代编译器并优化 CPU。
·消除虚拟函数调度，以减少多个 CPU 调用。
·利用初级编程（例如，将即时数据加载到 CPU 寄存器），以加速内存访问并优化 Spark 的引擎，以有效地编译和执行简单循环。更多详细信息，请参考 Project Tungsten：Bringing Apache Spark Closer to Bare Metal（https://databricks.com/blog/2015/04/28/project-tungstenbringing-spark-closer-to-bare-metal.html）；Deep Dive into Project Tungsten：Bringing Spark Closer to Bare Metal[SSE 2015 Video and Slides]（https://spark-summit.org/2015/events/deep-dive-into-project-tungsten-bringing-spark-closerto-bare-metal/）；Apache Spark as a Compiler：Joining a Billion Rows per Second on a Laptop（https://databricks.com/blog/2016/05/23/apache-sparkas-a-compiler-joining-a-billion-rows-per-second-on-alaptop.html）。
