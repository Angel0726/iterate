---
title: 02 为什么需要 Spark Streaming
toc: true
date: 2019-07-02
---
10.2 为什么需要 Spark Streaming
Tathagata Das是 Apache Spark项目的贡献者及管理委员会（PMC）的成员，Spark Streaming的主要开发者，他曾在 Datanami 文章《Spark Streaming：What is It and Who’s Using it》中提到的对 streaming 有商业需求（https://www.datanami.com/2015/11/30/spark-streaming-what-is-it-and-whos-using-it/）。随着在线交易和社交媒体以及传感器和设备的普及，很多公司正在以更快的速度产生和处理更多的数据。
开发有规模的、实时的可实现的可预测的能力，为这些企业提供了竞争优势。无论你是在检测欺诈性的交易，提供传感器异常的实时检测，还是对下一个病毒性传播的推文做出反应，流分析在数据科学家和数据工程师的工具箱中变得日益重要。
Spark Streaming正在迅速被采用，原因是 Apache Spark在同一框架内统一了所有这些不同的数据处理范例（通过 ML 和 MLlib 的机器学习、Spark SQL和 Streaming）。因此，你可以从培训机器学习模型（ML或 MLlib）到使用这些模型（Streaming）评测数据，并使用你最喜爱的 BI 工具（SQL）执行分析，所有这些都在同一框架内。包括 Uber、Netflix和 Pinterest 在内的公司经常展示 Spark Streaming的应用范例：
·How Uber Uses Spark and Hadoop to Optimize Customer Experience，https://www.datanami.com/2015/10/05/how-uber-uses-spark-and-hadoop-to-optimize-customer-experience/；
·Spark and Spark Streaming at Netflix，https://spark-summit.org/2015/events/spark-and-spark-streaming-at-netflix/；
·Can Spark Streaming survive Chaos Monkey，http://techblog.netflix.com/2015/03/can-spark-streaming-survive-chaos-monkey.html；
·Real-time analytics at Pinterest，https://engineering.pinterest.com/blog/real-time-analytics-pinterest。
目前，围绕 Spark Streaming有四种广泛的场景：
·流 ETL：将数据推入下游系统之前对其进行持续的清洗和聚合。这么做通常可以减少最终数据存储中的数据量。
·触发器（Triggers）：实时检测行为或异常事件，及时触发下游动作。例如当一个设备接近了检测器或者基地，就会触发警报。
·数据浓缩：将实时数据与其他数据集连接，可以进行更丰富的分析。例如将实时天气信息与航班信息结合，以建立更好的旅行警报。
·复杂会话和持续学习：与实时流相关联的多组事件被持续分析，以更新机器学习模型。例如与在线游戏相关联的用户活动流，允许我们更好地做用户分类。
