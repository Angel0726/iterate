---
title: 03 Stark Streaming 应用程序数据流是什么
toc: true
date: 2019-07-02
---
10.3 Spark Streaming应用程序数据流是什么
下图提供了 Spark driver、workers、streaming源与目标间的数据流：
上图中 Spark Streaming Context的 ssc.start（）是入口点：
1.当 Spark Streaming上下文启动时，驱动进程将对 executor（即 Spark 工作节点）执行长时间运行的任务。
2.executor中的 Receiver（该图中的 Executor1）从 Streaming 源接收数据流。Receiver将输入的数据流分成多个数据块并将这些块保持在内存中。
3.这些块还被复制到另一个 executor 以避免数据丢失。
4.块 ID 信息被传送到 driver 上的块管理 Master（Block Management Master）。
5.对于在 Spark Streaming Context（通常是每 1 秒钟）内配置的每个批次间隔，驱动程序将启动 Spark 任务来处理这些块。然后，这些块被持久化到任意数量的目标数据存储中，包括云存储（例如 S3、WASB等），关系数据存储（例如 MySQL、PostgreSQL等）和 NoSQL 存储。
可以说，streaming应用程序有很多现存的部分需要不断优化和配置。Spark Streaming的大部分文档在 Scala 中更加完整，因此，在使用 python API时，可能需要参考 Scala 版本的文档。如果你遇到这种情况并且有建议的修改方法，请提交缺陷记录并且填写 PR（proposed fix），（https://issues.apache.org/jira/browse/spark/）。
本话题更深的探讨，请参考：
1.Spark 1.6 Streaming Programming Guide，https://spark.apache.org/docs/1.6.0/streaming-programming-guide.html。
2.Tathagata Das’Deep Dive with Spark Streaming（Spark Meetup 2013-06-17），http://www.slideshare.net/spark-project/deep-divewithsparkstreaming-tathagatadassparkmeetup20130617。1
