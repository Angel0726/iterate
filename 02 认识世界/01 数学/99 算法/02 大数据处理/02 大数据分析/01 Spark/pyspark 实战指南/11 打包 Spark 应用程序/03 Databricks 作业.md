---
title: 03 Databricks 作业
toc: true
date: 2019-07-02
---
11.3 Databricks作业
如果你正在使用 Databricks 产品，从你的 Databricks 笔记本开发到生产的一种简单的方法是使用 Databricks 作业特征。它会让你：
·安排你的 Databricks 笔记本运行现存的或者新的集群；
·安排你期望的频率（从分钟到月）；
·安排超时和重试你的作业；
·作业开始、完成或者出错时，进行提醒；
·查看历史作业运行以及回顾个人笔记本作业运行的历史。
此功能极大简化了作业提交的调度和生产流程。注意，你需要升级你的 Databricks 订阅（社区版）来使用此功能。
要使用此功能，可以到 Databricks 作业菜单，点击 Create 作业。从这里开始填写作业名称，并且选择你想要转换为作业的笔记本，如下截图所示：
一旦你选择了你的笔记本，你还可以选择是使用一个正在运行的现存的集群，还是使用作业调度来为这个作业特别启动一个 New Cluster，如以下截图所示：


一旦你选择好了笔记本和集群；便可以设置进度表、警报、超时和重试。
一旦你完成了所有作业的设置，它应该看上去类似人口与价格线性回归作业（Population vs.Price Linear Regression），如以下截图所示：
你可以通过点击 Active runs下方的 Run Now链接来测试作业。
正如在 Meetup Streaming RSVPs作业中所指，你可以查看已完成运行的历史；如截图所示，该笔记本有 50 个已完成的作业：
点击作业运行（job run）（本例中 Run 50），你可以看到作业运行的结果。你不仅仅能看到开始的时间、持续的时间和状态，还能查看特定作业的结果：

REST作业服务器（Job Server）
运行作业的一种流行方式是使用 REST API。如果你使用的是 Databricks，可以使用 Databricks REST API运行你的作业。如果你更喜欢管理自己的作业服务器，有一种开源的 REST 作业服务器是 spark-jobserver，用一个 REST 的接口来提交和管理 Apache Spark作业、jars以及作业环境。近期项目（写这本书的时期）进行了更新，所以它可以处理 PySpark 作业。更多的信息，请参阅 https://github.com/spark-jobserver/spark-jobserver。
