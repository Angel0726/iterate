---
title: 02 创建 RDD
toc: true
date: 2019-07-02
---
2.2 创建 RDD
PySpark中，有两种方法可以创建 RDD：
要么用.parallelize（……）集合（元素 list 或 array）：
要么引用位于本地或者外部的某个文件（或者多个文件）：从 ftp：//ftp.cdc.gov/pub/Health_Statistics/NCHS/Datasets/DVS/mortality/mort2014us.zip下载 Mortality 数据集 VS14MORT.txt文件（2016年 7 月 31 日访问）；记录的模式在该文档中解释：http://www.cdc.gov/nchs/data/dvs/Record_Layout_2014.pdf。选择这些数据集的目的是：这些记录的编码会帮助我们在本章中理解如何使用 UDF 变换你的数据。为了方便，我们也在此链接中上传了该文件：http://tomdrabas.com/data/VS14MORT.txt.gz。
sc.textFile（……，n）方法中的最后一个参数代表该数据集被划分的分区个数。经验法则是把每一个集群中的数据集分成 2 到 4 个分区。
Spark可以从多个文件系统中读取：如 NTFS、FAT这类的本地文件系统，或者 Mac OS Extended（HFS＋），或者如 HDFS、S3、Cassandra这类的分布式文件系统，还有其他各类的文件系统。避免数据集从这样的路径读取或者被保存到这样的路径：路径不能包含特殊字符[]。注意，这也适用于 Amazon S3或者 Microsoft Azure数据存储的存储路径。
支持多种数据格式：文本、parquet、JSON、Hive tables（Hive表）以及使用 JDBC 驱动程序可读取的关系数据库中的数据。注意，Spark可以自动处理压缩数据集（如之前的 Gzipped 例子）。
根据数据读取方式的不同，持有的对象将以略有不同的方式表示。从文件中读取的数据表示为 MapPartitionsRDD，而不是使用.paralellize（……）方法对一个集合进行操作时的 ParallelCollectionRDD。
2.2.1 Schema
RDD是无 schema 的数据结构（和下一章要讨论的 DataFrame 不同）。因此在以下的代码片段中的并行数据集，通过 Spark 使用 RDD 非常适用：
所以，我们几乎可以混合使用任何类型的数据结构：tuple、dict或者 list 和 Spark 都能支持。
如果对数据集使用方法.collect（）（执行把该数据集送回驱动的操作），可以访问对象中的数据，和在 python 中常做的一样。
执行的结果是：
.collect（）方法把 RDD 的所有元素返回给驱动程序，驱动程序将其序列化成了一个列表。


我们会在本章之后的部分讨论更多有关.collection（）的使用注意事项。
2.2.2 从文件读取
从文本文件读取数据时，文件中的每一行形成了 RDD 的一个元素。
data_from_file.take（1）命令输出了以下的结果（有的不可读）：
为了增强它的可读性，我们可以创建一个元素列表，其中每行代表一个值的列表。
2.2.3 Lambda表达式
在这个例子中，我们将从 data_from_file的隐藏查看记录提取有用的信息。请参阅本书 GitHub 库里有关这个方法的细节。由于空间的限制，只呈现了该完整方法的缩写版本，特别是创建 Regex 表达式（正则表达式）的时候。可以在此找到代码：https://github.com/drabastomek/learningPySpark/tree/master/Chapter03/LearningPySpark_Chapter03.ipynb。
首先，我们在下列代码的帮助下定义方法，该方法会把不可读的行解析成我们能够使用的信息：谨记！定义纯 python 方法会降低应用程序的速度，因为 Spark 需要在 python 解释器和 JVM 之间连续切换。你要尽可能使用内置的 Spark 功能。
接下来，我们要引入必要的模块：re模块，我们会使用正则表达式来解析记录，使用 NumPy 可以缓解一次选择多个元素。
最后，创建 Regex 对象提取指定信息并通过 Regex 对象解析行。我们不会深入到细节来描述正则表达式。可以在此找到关于正则表达式的提纲 https://www.packtpub.com/application-development/mastering-python-regular-expressions。
一旦记录被解析，我们就尝试着将列表转换成 NumPy 数组并返回该数组；如果转化返回失败，则返回一个列表的默认值-99，这样我们就知道该记录没有被正确解析。我们可以通过使用.flatMap（……）隐式筛选出不合规的数据，并且返回一个空 list[]，而不是-99。查看细节：http://stackoverflow.com/questions/34090624/remove-elements-from-spark-rdd。
现在，我们要使用 extractInformation（……）方法分割和转换数据集。请注意，我们只把签名方法传到.map（……）：在每一个分区中，该方法每次都会传递 RDD 的一个元素给 extractInformation（……）方法：
运行 data_from_file_conv.take（1）命令产生以下的结果（缩减版）：2
