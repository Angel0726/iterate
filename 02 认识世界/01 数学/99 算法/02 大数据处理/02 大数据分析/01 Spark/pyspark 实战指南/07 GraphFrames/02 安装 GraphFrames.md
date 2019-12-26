---
title: 02 安装 GraphFrames
toc: true
date: 2019-07-02
---
# 安装 GraphFrames

如果你正在 Spark CLI（例如 spark-shell、pyspark、spark-sql、spark-submit）的环境中运行你的程序，你可以使用——packages命令来提取、编译和执行使用 GraphFrames 包所需的必要代码。

例如，要在 spark-shell中使用最新的带有 Spark2.0和 Scala2.11的 GraphFrames 软件包（版本 0.3），命令是：

如果你正在使用 notebook service（spark的一个 service），可能需要首先安装该软件包。例如，以下部分显示了在免费的 Databricks 社区版本（http://databricks.com/try-databricks）中安装 GraphFrames 库的步骤。

## 创建库

在 Databricks 中，你可以创建一个包括 Scala/Java JAR、python Egg或 Maven Coordinate（包括 Spark 包）的库。

要开始创建库，请在 Databricks 中点开 WorkSpace，右键单击要在其中创建库的文件夹（在本例中为 flights），选择 Create，然后单击 Library：

在“Create Library”的对话框中，下拉“Source”并在列表中选择“Maven Coordinate”，如下图所示。Maven是一个用于构建和管理类似 GraphFrames 这种基于 Java 的项目的工具。Maven坐标是这些项目（或依赖项或插件）的唯一标志，你可以使用它们在 Maven 仓库内快速找到目标项目；例如 https://mvnrepository.com/artifact/graphframes/graphframes。





从这里，你可以单击 Search Spark Packages and Maven Central这个按钮，并搜索 GraphFrames 包。确保 GraphFrames 中的 Spark（例如 Spark 2.0）和 Scala（例如 Scala 2.11）的版本与 Spark 群集相匹配。

如果你已经知道 GraphFrames Spark软件包的 Maven 坐标，你也可以自行输入。对于 Spark 2.0和 Scala 2.11，你可以在 coordinate 这一栏输入以下坐标：

输入后，点击 Create Library，如以下屏幕截图所示：

请注意，这是 GraphFrames Spark软件包（作为库的一部分）的一次性安装任务。一旦安装，你可以默认自动将软件包附加到你创建的任何 Databricks 集群上：
