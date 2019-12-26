---
title: 01 Python 到 RDD 之间的通信
toc: true
date: 2019-07-02
---
3.1 python到 RDD 之间的通信
每当使用 RDD 执行 PySpark 程序时，潜在地需要巨大的开销来执行作业。如下图所示，在 PySpark 驱动器中，Spark Context通过 Py4j 启动一个使用 JavaSparkContext 的 JVM。所有的 RDD 转换最初都映射到 Java 中的 pythonRDD 对象。
一旦这些任务被推送到 Spark 工作节点，pythonRDD对象就使用管道（pipe）启动 python 的子进程（subprocess），发送代码和数据到 python 中进行处理：
虽然该方法允许 PySpark 将数据处理分布到多个工作节点的多个 python 子进程中，但是如你所见，python和 JVM 之间还是有很多上下文切换和通信开销的。一个有关 PySpark 性能的优秀资源是 Holden Karau的 Improving PySpark Performance：Spark performance beyond the JVM（http://bit.ly/2bx89bn）。
