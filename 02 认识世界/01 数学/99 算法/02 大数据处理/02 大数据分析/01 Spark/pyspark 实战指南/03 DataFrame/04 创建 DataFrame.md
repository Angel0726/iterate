---
title: 04 创建 DataFrame
toc: true
date: 2019-07-02
---
3.4 创建 DataFrame
通常情况下，通过使用 SparkSession 导入数据（或者调用 PySpark 的 shell 脚本 spark）来创建 DataFrame。在 Spark 1.x版本中，通常必须使用 sqlContext。
后几章中，我们将讨论如何将数据导入你的本地文件系统、Hadoop分布式文件系统（HDFS）或者其他的云存储系统（例如 S3 或者 WASB）。在本章中，我们的重点是在 Spark 中直接生成你自己的 DataFrame 数据或者利用 Databricks 社区版中的现成数据源。关于如何注册 Databricks 社区版的说明，请参阅额外的章节。
首先，不必访问文件系统，我们将通过生成的数据创建一个 DataFrame。在这种情况下，我们会先创建 stringJSONRDD RDD，然后将它转换成一个 DataFrame。这段代码用 JSON 格式创建了一个由几个游泳选手（他们的 ID、名字、年龄、眼睛颜色）组成的 RDD。
3.4.1 生成自己的 JSON 数据
接下来，我们将开始生成 stringJSONRDD RDD：
现在，我们已经创建了 RDD，利用 SparkSession read.json方法，RDD将会被转换成一个 DataFrame（即 spark.read.json（……））。我们还可以利用.createOrReplaceTempView方法创建一个临时表。在 Spark 1.x中，该方法是.registerTempTable，但在 Spark 2.x中该方法却是被废弃的方法之一。
3.4.2 创建一个 DataFrame
以下是创建 DataFrame 的代码：
3.4.3 创建一个临时表
以下是创建临时表的代码：
在前面的章节中提到过，许多的 RDD 操作都是有相关转换的，直到行动操作执行，这些 RDD 操作都不会被执行。例如在前面的代码段中，sc.parallelize是执行利用 spark.read.json从 RDD 转化成 DataFrame 时的一个转换。请注意，在这段代码的电脑屏幕截图中（靠近左下），直到第二个含有 spark.read.json操作的子代码出现，Spark工作都不会被执行。虽然这些是源自 Databricks 社区版的截屏，但是所有的示例代码和 Spark UI截屏可以在任何 Apache Spark 2.x的版本中执行/查看。
为了进一步强调这一点，在下图的右窗格中，我们展示了执行的 DAG 图。一个更加便于理解 Spark UI DAG可视化的优质资源是一篇博客文章 Understanding Your Apache Spark Application Through Visualization（http://bit.ly/2cSemkv）。
在以下的截屏中，你可以看到 Spark 工作的 parallelize 操作源于生成 RDD stringJSONRDD的第一个代码块，创建 DataFrame 需要 map 操作和 mapPartitions 操作：
spark.read.json（stringJSONRDD）工作的 DAG 可视化 Spark UI
在接下来的截屏中，你能看到 parallelize 操作的各个阶段正来自于生成 RDD stringJSONRDD的第一个代码块，创建 DataFrame 需要 map 操作和 mapPartitions 操作：
spark.read.json（stringJSONRDD）工作中各个阶段的 DAG 可视化 Spark UI
重要的是注意 parallelize、map和 mapPartitions 都是 RDD 转换而来。spark.read.json（在这个例子中）包裹在 DataFrame 中，这不仅是 RDD 转换，还是 RDD 转换成 DataFrame 的行动。这是一个重要的调用，因为即使你执行的是 DataFrame 操作，调试操作还是需要你记住并理解 Spark UI中的 RDD 操作。
请注意，创建临时表是一次 DataFrame 转换，并且只有直到执行 DataFrame 动作时，创建临时表才会被执行（例如，在 SQL 中的查询将在以下章节执行）。DataFrame的转换和动作与 RDD 的转换和动作类似，还有一套缓慢的（转换）操作。但是，对比 RDD，DataFrames操作并不是缓慢的，主要是源于 Catalyst 优化器。更多的信息，请参阅 Holden Karau和 Rachel Warren的 High Performance Spark（http://highperformancespark.com/）。
