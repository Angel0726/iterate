---
title: 01 spark-submit 指令
toc: true
date: 2019-07-02
---
11.1 spark-submit命令
提交作业到 Spark 的入口点（在本地或者在集群上）是 spark-submit脚本。然而，该脚本不仅允许你提交作业（尽管这是其主要目的），而且还可以终止作业或检查其状态。在后台，spark-submit命令传递这个调用到 spark-class脚本，反过来，开始启动 Java 应用程序，感兴趣的话，你可以查看 Spark 的 GitHub 库：https://github.com/apache/spark/blob/master/bin/spark-submit。
spark-submit命令提供了一个统一的 API 把应用程序部署到各种 Spark 支持的集群管理器上（如 Mesos 或 Yarn），从而免除了单独配置每个应用程序。
在一般级别上，语法如下：
我们马上会把所有选项列表过一遍。app arguments是要传递给应用程序的参数。你可以使用 sys.argv从命令行解析参数（在 import sys之后），或者可以利用 argparse 来模块化 python。命令行参数
使用 spark-submit时，你可以给 Spark 引擎传递大量不同的参数。下面我们只讲述 python 的具体参数（因为 spark-submit还可以用来提交 Scala 或者 Java 所写的应用程序，并且打包成.jar文件）。
现在我们将逐个介绍这些参数，以便对命令行所做的工作有一个很好的概述：
·——master：用于设置主（头）结点 URL 的参数。支持的语法是：
·local：用于执行本地机器的代码。如果你传递 local 参数，Spark会运行一个单一的线程（不会利用任何并行线程）。在一个多核机器上，你可以通过确定 local[n]来为 Spark 指定一个具体使用的内核数，n指的是使用的内核数，还可以通过 local[*]来制定运行和 Spark 机器内核一样多的复杂线程。
·spark：//host：port：这是一个 URL 和一个 Spark 单机集群的端口（不运行任何作业调度，如 Mesos 或者 Yarn）。
·mesos：//host：port：这是一个 URL 和一个部署在 Mesos 的 Spark 集群的端口。
·yarn：作为负载均衡器，用于从运行 Yarn 的头结点提交作业。
·——deploy-mode：允许你决定是否在本地（使用 client）启动 Spark 驱动程序的参数，或者在集群内（使用 cluster 选项）的其中一台工作机器上启动。此参数的默认值是 client。这是一份解释更多特异性差异的 Spark 文档的摘录（来源：http://bit.ly/2hTtDVE）：
一个常见的部署策略是从[一个 screen 会话]物理上和你的工作机器（如在一个独立的 EC2 集群的主节点）在同一个位置的网关设备提交应用程序。这种设置比较适合客户端模式。客户端模式中，驱动程序直接作为集群的客户机在 spark-submit过程中启动。应用程序的输入和输出附加在控制台上。因此，这种模式特别适合参与 REPL 的应用程序（如 Spark shell）。
另外，如果你的应用程序从一台机器远程提交到工作机器（如你本地的笔记本电脑），常见的是，在驱动程序和执行程序之间使用集群模式最小化网络延迟。目前，独立模式不支持 python 应用程序的集群模式。
·——name：你的应用程序名称。注意，创建 SparkSession（下一节会了解）时，如果是以编程方式指定应用程序名称，那么来自命令行的参数会被重写。讨论——conf参数时，我们会简短地解释参数的优先级。
·——py-files：.py、.egg或者.zip文件的逗号分隔列表，包括 python 应用程序。这些文件将被交付给每一个执行器来使用。在本章后段，我们会为你展示如何将代码打包到一个模块中。
·——files：命令给出一个逗号分隔的文件列表，这些文件将被交付到每一个执行器来使用。
·——conf：参数通过命令行动态地更改应用程序的配置。语法是＝。例如，你可以传递——conf spark.local.dir＝/home/SparkTemp/或者——conf spark.app.name＝learningPySpark；后者相当于提交和之前解释过的——name一样的属性。Spark从 3 个地方使用配置参数：在应用程序中创建 SparkContext 时，你指定了来自 SparkConf 的参数获得最高优先权，然后第二优先级是任何你传递给来自命令行的 spark-submit脚本的参数，最后是任何在 conf/spark-defaults.conf文件中指定的参数。
·——properties-file：配置文件。它应该有和 conf/spark-defaults.conf文件相同的属性设置，也是可读的。
·——driver-memory：指定应用程序在驱动程序上分配多少内存的参数。允许的值有一个语法限制，类似于 1000M，2G。默认值是 1024M。
·——executor-memory：参数指定每个执行器上为应用程序分配多少内存。默认值是 1G。
·——help：展示帮助信息和退出。
·——verbose：在运行应用程序时打印附加调试信息。
·——version：打印 Spark 版本。
仅在 Spark 单机集群（cluster）部署模式下，或者在一个 Yarn 上的部署集群上，你可以使用——driver-cores来允许指定驱动程序的内核数量（默认值为 1）。仅在一个 Spark 单机或者 Mesos 的集群（cluster）部署模型中，你也有机会使用这些：
·——supervise：如果指定了这个参数，当驱动程序丢失或者失败时，就会重新启动该驱动程序。也可以通过在 Yarn



中设置集群（cluster）的——deploy-mode来完成。
·——kill：将完成的过程赋予 submission_id。
·——status：如果指定了该命令，它将请求指定的应用程序的状态。
在 Spark 单机和 Mesos（client部署模式）中，你可以指定——total-executor-cores，该参数会为所有执行器（不是每一个）请求指定的内核数量。另一方面，在 Spark 单机和 YARN 中，只有——executor-cores参数指定每个执行器的内核数量（在 YARN 模式中默认值为 1，或者对于单机模式下所有工作节点可用的内核）。
另外，向 YARN 集群提交时你可以指定：
·——queue：该参数指定了 YARN 上的队列，以便将该作业提交到队列（默认值是 default）
·——num-executors：指定需要多少个执行器来请求该作业的参数。如果启动了动态分配，则执行器的初始数量至少是指定的数量。
既然我们已经讨论了所有的参数，那么是时候把这些参数付诸实践了。
