---
title: 09 DataFrame 场景 - 实时飞行性能
toc: true
date: 2019-07-02
---
3.9 DataFrame场景——实时飞行性能
为了展示能够对 DataFrame 使用的查询类型，让我们看一看实时飞行性能的用例。我们会分析航空公司的实时性能以及航班延误的原因——实时数据（http://bit.ly/2ccJPPM），并且加入机场数据集，由开放的航班机场、航线和航线数据（http://bit.ly/2ccK5hw）获得，可以更好地理解与航班延误相关的变量。本节将使用 Databricks 社区版（Databricks Community Edition）（Databricks产品的免费版），可以从 https://databricks.com/try-databricks中得到。我们将使用 Databricks 中的可视化和预加载的数据集，帮助你更方便地专注于编写代码和分析结果。
如果想在自己的环境中运行，你可以在我们的 GitHub 库中找到对于本书可用的数据集，https://github.com/drabastomek/learningPySpark。
3.9.1 准备源数据集
首先我们将通过指定数据集的文件路径位置以及使用 SparkSession 导入数据集，来处理机场和飞行性能源数据集：
注意，我们使用 CSV 阅读器（com.databricks.spark.csv）导入数据，这个方法适用于任何指定的分隔符（请注意机场数据是制表符（tab）分隔的，而飞行性能数据是逗号（comma）分隔的）。最后，我们对飞行数据集进行缓存，以便加快后续的查询。
3.9.2 连接飞行性能和机场
其中一个有关 DataFrame/SQL的较为常见的任务是将两种不同的数据集关联在一起，这往往是一个难度较高的操作（从性能的角度来看）。使用 DataFrame，针对这些关联了大量的性能优化默认包括：


在我们的场景中，通过城市和起飞代码查询华盛顿州的航班延误总数。这就要求将飞机性能数据和机场数据，通过国际航空运输协会（International Air Transport Association，简称 IATA）将代码关联在一起。该查询输出如下：
使用笔记本（notebook）（如 Databricks、ipython、Jupyter和 Apache Zeppelin），你可以更轻松地执行和可视化查询。在下面的例子中，我们将使用 Databricks 笔记本。我们可以用 python 笔记本在笔记本元素中使用％sql函数执行 SQL 语句：
和之前的查询一样，不过由于格式不同更容易阅读。在 Databricks 笔记本的例子中，我们可以将该数据快速地可视化为一个条形图：
3.9.3 可视化飞行性能数据
让我们继续可视化数据，分解美国大陆上的所有联邦州。

输出的条形图如下：
不过，如果可以把这些数据视为一张地图，则效果更佳；点击图表左下方的条形图图标，选择不同的本地导航，包括地图：
DataFrame的一个重要优势是，信息像表格一样被结构化。因此，无论是使用笔记本还是你偏爱的 BI（商业智能 Business Intelligent）工具，都可以快速可视化数据。你可以在 http://bit.ly/2bkUGnT中找到完整的 pyspark.sql.DataFrame方法列表。在 http://bit.ly/2bTAzLT中找到完整的 pyspark.sql.functions列表。
