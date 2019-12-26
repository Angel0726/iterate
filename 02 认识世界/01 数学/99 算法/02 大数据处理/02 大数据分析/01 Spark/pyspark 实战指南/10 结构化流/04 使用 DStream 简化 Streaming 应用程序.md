---
title: 04 使用 DStream 简化 Streaming 应用程序
toc: true
date: 2019-07-02
---
10.4 使用 DStream 简化 Streaming 应用程序
下面我们使用 python 的 Spark Streaming来创建一个简单的单词计数例子。这个例子中，我们会使用 DStream——由众多小批次数据组成的离散数据流。本书这一部分使用的例子可以在以下内容中找到：https://github.com/drabastomek/learningPySpark/blob/master/Chapter10/streaming_word_count.py。
这个字数计数示例将使用 Linux/Unix nc命令——它是一种读写跨网络连接数据的简单实用程序。我们将使用两个不同的 bash 终端，一个使用 nc 命令将多个单词发送到我们计算机的本地端口（9999），另一个终端将运行 Spark Streaming来接收这些字，并对它们进行计数。脚本的初始命令集在这里被注明：
对于以上的多个命令，有些重要的地方要解释一下：
1.第 9 行的 StreamingContext 是 Spark Streaming的入口点。
2.第 9 行中……（sc，1）的 1 是批间隔；在这种情况下，我们每秒运行微批次。
3.第 12 行上的 lines 代表通过 ssc.socketTextStream提取而来的 DStream 数据流。
4.如同在描述中提到的，ssc.socketTextStream是 Spark Streaming中从特定套接字查看文本流的方法，在这里，是本地计算机的 9999。
接下来的几行代码（如评论中所述），将 DStream 的行分为单词，然后使用 RDD 对每批数据中的单词进行计数，并将该信息打印到控制台（行号 9）：
代码行的最后一行启动 Spark Streaming（ssc.start（）），然后等待终止命令来停止运行（例如<Ctrl><C>）。如果没有等到终止命令，Spark Streaming程序将继续运行。
如前所述，现在有了脚本，打开两个终端窗口：一个用于您的 nc 命令，另一个用于 Spark Streaming程序。要从其中一个终端启动 nc 命令，请键入：
从这个点开始，你在这个终端所输入的一切都将被传送到 9999 端口，如下面的屏幕截图所示：


本例中（如前所述），我敲入 green 这个词三次，blue五次。从另一个终端屏幕，我们来运行刚创建的 python 流脚本。本例中该脚本被命名为 streaming_word_count.py../bin/spark-submit streaming_word_count.py localhost 9999。
该命令将运行 streaming_word_count.py脚本，读取本地计算机（即 localhost）端口 9999 以接收发送到该套接字的任何内容。由于你已经在第一个屏幕上将信息发往端口，因此在启动脚本后不久，Spark Streaming程序会读取发送到端口 9999 的任何单词，并按照以下屏幕截图中所示的样子执行单词计数：
streaming_word_count.py脚本将持续读取并打印任何新的信息到控制台。回到第一个终端（使用 nc 命令的终端），我们现在可以输入下一组单词，如下面的屏幕截图所示：
查看第二个终端中的流脚本，你会注意到此脚本每秒钟继续运行（即配置的 batch interval为 1 秒），几秒钟后你会看到 gohawks 的计数：


使用这个比较简单的脚本，现在你可以看到使用 python 的 Spark Streaming。但是，如果你继续在 nc 终端中输入单词，你会注意到此信息未汇总。如果我们继续在 nc 终端输入 green（如下所示）：
Spark Streaming终端将报告当前数据快照；即两个 green 的计数值（如下所示）：
但是这里没有体现出全局聚合的概念，全局聚合会保留信息的状态。这意味着，不是报告有两个新的 green，而是让 Spark Streaming给出 green 总体的计数，比如，7个 green，5个 blue，1个 gohawks。我们将在下一节中以 UpdateStateByKey/mapWithState的形式讨论全局聚合。其他好的 PySpark Streaming示例，请查看：
“Network Wordcount”（在 Apache Spark GitHub repo中）：https://github.com/apache/spark/blob/master/examples/src/main/python/streaming/network_wordcount.py；
“python Streaming Examples”：https://github.com/apache/spark/tree/master/examples/src/main/python/streaming；
“S3 FileStream Wordcount”（Databricks notebook）：https://docs.cloud.databricks.com/docs/latest/databricks_guide/index.html#07％20Spark％20Streaming/06％20FileStream％20 Word％20Count％20-％20python.html。
