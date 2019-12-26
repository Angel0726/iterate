---
title: 01 什么是  Spark Streaming
toc: true
date: 2019-07-02
---
10.1 什么是 Spark Streaming
Spark Streaming的核心是一种可扩展、容错的数据流系统，它采用 RDD 批量模式（即批量处理数据）并加快处理速度。同时它又有点过于简单，基本上 Spark Streaming可以以小批量或批次间隔（从 500 毫秒到更大的间隔窗口）运行。
如下图所示，Spark Streaming接收输入数据流，并在内部将数据流分为多个较小的 batch（batch大小取决于 batch 的间隔）。Spark引擎将这些输入数据的 batch 处理后，生成处理过数据的 batch 结果集。
资料来源：Apache Spark Streaming Programming Guide（http://spark.apache.org/docs/latest/streaming-programming-guide.html）
Spark Streaming的主要抽象是离散流（DStream），它代表了前面提到的构成数据流的那些小批量。DStream建立在 RDD 上，允许 Spark 开发人员在 RDD 和 batch 的相同上下文中工作，现在只将其应用于一系列流问题当中。另外一个重要的方面是，由于你使用的是 Apache Spark，Spark Streaming与 MLlib、SQL、DataFrame和 GraphX 都做了集成。
下图表示 Spark Streaming的基本组件：
资料来源：Apache Spark Streaming Programming Guide（http://spark.apache.org/docs/latest/streaming-programming-guide.html）
Spark Streaming是一种 high-level API，为状态操作提供了容错的 exactly-once语义。Spark Streaming内置了一系列 receiver，可以接收很多来源的数据，最常见的是 Apache Kafka、Flume、HDFS/S3、Kinesis和 Twitter。例如，最常用的 Kafka 和 Spark Streaming的集成在“Spark Streaming＋Kafka Integration Guide”中有详细记载：https://spark.apache.org/docs/latest/streaming-kafka-integration.html。
此外，你可以创建自己的定制接收器（custom receiver），如 Meetup Receiver（https://github.com/actions/meetup-stream/blob/master/src/main/scala/receiver/MeetupReceiver.scala），允许你使用 Spark Streaming来读取 Meetup Streaming API（https://www.meetup.com/meetup_api/docs/stream/2/rsvps/）。查看 Meetup Receiver如何工作
如果你对 Spark Streaming Meetup Receiver感兴趣，可以参考 Databricks notebooks，它使用了前面提到过的 Meetup Receiver：https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny％40databricks.com/content/Streaming％20Meetup％20 RSVPs。
下面的截图是运行中的笔记本左边窗口的截图，同时可以浏览右边的 Spark UI（Streaming Tab）。

你可以使用 Spark Streaming从全国（或世界）范围内接收 Meetup RSVP，并根据州（或国家）获得 Meetup RSVP的几乎实时的摘要。注意，这些笔记本目前是用 Scala 写的。
