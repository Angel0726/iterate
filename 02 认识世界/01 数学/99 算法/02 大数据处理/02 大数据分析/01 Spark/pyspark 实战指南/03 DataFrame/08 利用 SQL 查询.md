---
title: 08 利用 SQL 查询
toc: true
date: 2019-07-02
---
3.8 利用 SQL 查询
让我们运行相同的查询，但是这一次，我们将对相同的 DataFrame 使用 SQL 查询。记住，DataFrame是可访问的，因为我们针对 swimmers 执行了.createOrReplaceTempView方法。
3.8.1 行数
以下是在你的 DataFrame 中利用 SQL 获得行数的代码段：
输出如下：
3.8.2 利用 where 子句运行筛选语句
利用 SQL 运行筛选语句，你可以使用 where 子句，如以下代码段所示：
查询输出的是仅选择 age＝22的 id 列和 age 列：
和 DataFrame API查询一样，如果只是想要取回眼睛颜色以字母 b 开头的游泳运动员的名字，我们还可以使用 like 语法：
输出如下：更多的信息，请参阅 http://bit.ly/2cd1wyx中的 Spark SQL，DataFrames，and Datasets Guide。使用 Spark SQL和 DataFrame 的一个重要提示是，虽然 CSV、JSON以及各种各样的数据格式便于使用，但是 Spark SQL分析查询最常见的存储格式是 Parquet 文件格式。这是一个许多其他数据处理系统支持的列式/柱状格式，并且对于自动保存原始数据模式的 Parquet 文件，Spark SQL支持这些 Parquet 文件的读写。更多的信息，请参阅最新的 Spark SQL Programming Guide>Parquet Files（http://spark.apache.org/docs/latest/sql-programming-guide.html#parquet-files）。另外，许多有关 Parguet 的性能优化，包括（但不仅限于）Automatic Partition Discovery and Schema Migration for Parquet（https://databricks.com/blog/2015/03/24/spark-sql-graduates-from-alpha-in-spark-1-3.html）以及 How Apache Spark performs a fast count using the parquet metadata（https://github.com/dennyglee/databricks/blob/master/misc/parquet-count-metadata-explanation.md）。
