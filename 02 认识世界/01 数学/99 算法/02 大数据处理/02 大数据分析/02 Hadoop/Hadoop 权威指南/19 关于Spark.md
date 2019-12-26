---
title: 19 关于Spark
toc: true
date: 2018-06-27 07:51:30
---
Apache Spark （//即是用于大数据处理的集群计算框架。与本 书所讨论的其他大多数数据处理框架不同，Spark并没有以 MapReduce 作为执行 引擎，而是使用了它自己的分布式运行环境在集群上执行工作。不过，正如我们 将在本章中看到的，Spark与 MapReduce 在 API 和运行环境方面有许多相似之 处。Spark与 Hadoop 紧密集成，它可以在 YARN 上运行，并支持 Hadoop 文件格 式及其存储后端（如 HDFS）。

Spark最突出的表现在于它能将作业与作业之间产生的大规模的工作数据集存储在 内存中。这种能力使得 Spark 在性能上超过了等效的 MapReduce 工作流，通常可 高出 1 个数量级，在某些情况下则有可能高出更多' 原因是 MapReduce 的数据 集始终需要从磁盘上加载。从 Spark 处理模型中获益最大的两种应用类型分别为 迭代算法（即对一个数据集重复应用某个函数，直至满足退出条件）和交互式分析 （用户向数据集发出一系列专用的探索性査询）。

即使你不需要在内存中进行缓存，Spark还是会因其出色的 DAG 引擎和用户体验 而极具吸引力。与 MapReduce 不同，Spark的 DAG 引擎可以处理任意操作流水 线，并为用户将其转换为单个作业。

Spark的用户体验也是首屈一指的，它拥有丰富的 API 集，可用于执行多种常见的

①详情参见 Matei Zaharia（Databricks平台首席科学家，大数据处理框架 Apache Spark创始人）等 人的文章，标题为 “Resilient Distributed Datasets: A Fault-Tolerant Abstraction for In-Memory Cluster Computing”，原文发布于 2012 年 NSDI’ 12 Proceedings of the 9th USENIX Conference on Networked Systems Design and Implementation，网址为 [http://bit.ly/resilient_dist_datasets](http://bit.ly/resilient_dist_datasets%e3%80%82)[。](http://bit.ly/resilient_dist_datasets%e3%80%82)

数据处理任务，譬如连接。在写本章时，Spark提供了三种语言的 API： Scala、 Java和 python。本章大多数示例使用的都是 Scala API，不过，要想把它们转换为 其他语言也很容易。Spark还为 Scala 和 python 提供了 REPL(read一 eval—print loop)交互模式，可以快速且轻松地浏览数据集。

实践证明 Spark 还是用于构建分析工具的出色平台。为此，Apache Spark项目包括 用于处理机器学习(MLlib)、图算法(GraphX)、流式计算(Spark Streaming)和 SQL

查询(Spark SQL)等模块。这些模块超出了本章的讨论范围，感兴趣的读者可以参 考 Apache Spark 网站，网址为 <http://spark.apache.org/0>

##### 19.1 安装 Spark

从下载页面(h/w/A下载一个稳定版本的 Spark 进制发行包(应当选择与正在使用的 Hadoop 发行版匹配的版本)，然后在合适的位 置解压缩该文件包：

% tar xzf spark-x.y.z-bin-distro.tgz

为了方便起见，可以把 Spark 的二进制文件路径添加到你的路径中，如下所示：

% export SPARK—HOME=~/sw/spark-x.y.z-bin-distro % export PATH=$PATH:$SPARK_HOME/bin

接下来就可以运行一个 Spark 示例了。

##### 19.2示例

我们通过 spark-shell程序运行一个交互式会话来演示 Spark。Spark-shell是添加了 一些 Spark 功能的 Scala REPL交互式解释器。通过以下命令启动该 shell 环境：

% spark-shell

Spark context available as sc.

scala〉

我们可以从控制台的输出看到已经创建了一个名为 SC 的 Scala 变量，它被用于保 存 SparkContext 实例。这是 Spark 的主要入口点。接下来就可以加载一个文本 文件：

scala> val lines = sc.textFile("input/ncdc/micro-tab/sample.txt")

lines: org.apache.spark.rdd.RDD[String] = MappedRDD[l] at textFile at <console>:12

lines变量引用的是一个弹性分布式数据集(Resilient Distributed Dataset，简称 RDD)。RDD是 Spark 最核心的概念，它是在集群中跨多个机器分区存储的一个只 读的对象集合。在典型的 Spark 程序中，首先要加载一个或多个 RDD，它们作为 输入通过一系列转换得到一组目标 RDD，然后对这些目标 RDD 执行一个动作， 例如计算出结果或者写入持久存储器。“弹性分布式数据集”中的术语“弹性” 指的是 Spark 可以通过重新安排计算来自动重建丢失的分区。

I 加载 RDD 或执行转换并不会立即触发任何数据处理的操作，只不过是创建了 一个计算的计划。只有当对 RDD 执行某个动作(比如 foreachO)时，才会触发 真正的计算。

继续讨论示例程序。我们希望执行的第一个转换是把文本行拆分为字段：

scala> val records = lines.map(_.split('At'1))

records: org.apache.spark.rdd.RDD[Array[String]] = MappedRDD[2] at map at <console>:14

通过 RDD 的 map()方法可以对 RDD 中的每个元素应用某个函数。此处，我们将 每一行文本(即一个 String)拆分成一个 String 类型的 Scala 数组。

然后，应用过滤器来滤除所有不良记录:

scala> val filtered = records.filter(rec => (rec(l) != H9999H && rec(2).matches("[01459]”)))

filtered: org.apache.spark.rdd.RDD[Array[String]] = FilteredRDD[3] at filter at <console>:16

RDD的方法的输入是一个过滤谓词，也就是一个返回布尔值的函数。 在本例中，此函数用于检查记录是否缺失了温度(以 9999 来表示)或者读。数质量不 达标。

为了找出每年的最高温度，我们需要按年份字段分组，这样才能对每年的所有温 度值进行处理。Spark的 reduceByKey()方法提供了分组功能，但是它需要一个 用 Scala Tuple2来表示的键-值对 RDD。因此，我们首先需要利用另一个 map 把 RDD转换为适当的表示形式：

scala〉 val tuples = filtered.map(rec => (rec(0).tolnt, rec(l).tolnt)) tuples: org.apache•spark.rdd.RDD[(IntInt)] = MappedRDD[4] at map at <console>:18

然后，我们就可以执行聚合操作。reduceByKeyO方法的参数是一个函数，它以

一对值作为输入，并将它们组合形成一个值。在本例中，我们使用了 Java的 Math.max 函数:

scala> val maxTemps = tuples•reduceByKey((aJ b) => Math.max(a， b)) maxTemps: org.apache.spark.rdd.RDD[(Int^ Int)] = MapPartitionsRDD[7] at reduceByKey at <console>:21

通过调用 foreach()方法，并传递 println()参 制台，我们就可以看到 maxTemps 的内容，：

把其中的每个元素打印到控



scala> maxTemps.foreach(println(」)

(1950,22)

(1949,111)

foreach()方法与标准 Scala 集合(例如 List)的等效方法一样，它对 RDD 的每个 元素应用一个函数(这个函数会产生一些附带效果)。正是这个操作触发了 Spark运 行一个作业来计算 RDD 中的值，从而使这些值能够传递给 println()方法。

或者，我们也可以将 RDD 保存到文件系统，如下所示:

scala> maxTemps.saveAsTextFile("output")

它创建了一个包含分区文件的名为 output 的目录：

% cat output/part-*

(1950,22)

(1949,111)

saveAsTextFile()方法也会触发 Spark 作业的运行。这两种方法的主要区别在于 saveAsTextFile()方法没有返回任何值，只是计算得到一个 RDD，并将其分区 写入 output 目录下的文件中。

###### 19.2.1 Spark应用、作业、阶段和任务

正如我们在上面的例子中看到的，Spark像 MapReduce 一样也有作业(job)的概 念，只不过 Spark 的作业比 MapReduce 的作业更通用，因为 Spark 作业是由任意 的多阶段(stages)有向无环图(DAG)构成，其中每个阶段大致相当于 MapReduce 中 的 map 阶段或 reduce 阶段。

这些阶段又被 Spark 运行环境分解为多个任务(task)，任务并行运行在分布于集群 中的 RDD 分区上，就像 MapReduce 中的任务一样。

Spark作业始终运行在应用（application）上下文（用 SparkContext 实例来表示）中，它 提供了 RDD分组以及共享变量。一个应用可以串行或并行地运行多个作业，并为 这些作业提供访问由同一应用的先前作业所缓存的 RDD 的机制（参见 19.3.3节）。 像 spark-shell会话这样的交互式 Spark 会话只是应用的一个实际的例子。

###### 19.2.2 Scala独立应用

在通过 Spark shell对程序进行完善之后，你可能希望将其打包形成一个自包含的 应用，以便将来能够再次运行。通过范例 19-1的 Scala 程序，可以了解它的具体 做法。

范例 19-1.使用 Spark 找出最高温度的 Scala 应用程序

import org.apache.spark.SparkContext

import org.apache.spark.{SparkConf^ SparkContext}

object MaxTemperature { def main(args: Array[String]) {

val conf = new SparkConf () .setAppName("Max Temperature..) val sc = new SparkContext(conf)

sc.textFile(args(0))

•map(—.split("\t"))

.filter(rec => (rec(l) != H9999u && rec(2).matches(H[01459]"))) .map(rec => (rec(0〉.tolnt, rec(l).tolnt))

.reduceByKey((aJ b) => Math.max(a, b))

.saveAsTextFile(args ⑴)

}

}

在运行一个独立程序时，由于提供 SparkContext 的 shell 环境已经不存在了，因此 我们需要自己创建 SparkContext。通过 SparkConf 来创建一个新的实例，有了 这个实例就可以把各种 Spark 属性传递给应用。在前面的程序中，我们只是设置 了应用的名称。

另外还要做一些其他方面的小改动。首先，我们要使用命令行参数来指定输入和

输出路径。其次，还要利用方法链来避免为每个 RDD 创建中间变量。这样做可以 使程序变得更加紧凑，同时在必要时仍然可以通过 Scala IDE来查看每次转换的类

型信息。

【 不是 Spark 定义的所有转换都可用于 RDD 类自身。在本例中，reducebyKey()

Vk (这个方法只能作用于键•值对 RDD)实际上是在 PairRDDFunctions 类中定义的，

.    不过，我们可以使用以下的导人操作来让 Scala 将 RDD[(Int, Int)]隐式转换为

PairRDDFunctions：

import org.apache.spark.SparkContext.

这个导入操作让 Spark 可以使用更多样化的隐式转换，因此它当然值得被包含

在程序中。

这一次，我们用 sparhsubmit 命令来运行程序，在作为参数传递的应用 JAR 中包 含了经过编译的 Scala 程序，后面紧随着程序的命令行参数(输入和输出路径)：

X spark-submit ••class MaxTemperature ••master local \ spark-examples.jar input/ncdc/micro-tab/sample.txt output

% cat output/part-*

(1950,22)

(1949,111)

我们还指定了两个选项：--class告诉 Spark 应用的类名，--master指定作业应 该在哪里运行。值 local 告诉 Spark 所有作业都在本地机器上的一个 JVM 中运 行。19.6节将会介绍在集群上运行的选项。下面让我们来看看 Spark 如何使用其 他语言，先从 Java 开始。

###### 19.2.3 Java 示例

Spark是用 Scala 实现的，而 Scala 作为基于 JVM 的语言，与 Java 有着良好集成关 系。用 Java 语言来写前面的示例非常简单，只不过会有点冗长，参见范例 19-2。0 范例 19-2.用 Spark 找出最离气温

public class MaxTemperatureSpark {

public static void main(String[] args) throws Exception { if (args.length !» 2) {

System.err.println("Usage: MaxTemperatureSpark <input path> 〈output path〉”)； System.exit(-1);

}

SparkConf conf » new SparkConf();

DavaSparkContext sc « new JavaSparkContext(MlocalM"MaxTemperatureSpark'conf); OavaRDD<String> lines » sc.textFile(args[0]);

①如果用 Java 8 lambda表达式来写，这段 Java 版的程序可以会更紧凑。

3avaRDD<String[]> records = lines.map(new FunctiorKString^ String[]>() { ^Override public Stringf] call(String s) {

return s•split

}

});

]avaRDD<String[]> filtered = records.filter(new Function<String[], Boolean>() { ^Override public Boolean call(String[] rec) {

return rec[l] != "9999" && rec[2].matches(H[01459]H);

}

});

JavaPairRDD<Integer> Integer〉 tuples = filtered.mapToPair( new PairFunction<String[], Integer，Integer>() {

^Override public Tuple2<Integer^ Integer〉 call(String[] rec) { return new Tuple2<Integer> Integer>(

Integer.parselnt(rec[0])， Integer.parselnt(rec[1]));

}

}

)；

3avaPairRDD<Integer^ Integer〉 maxTemps = tuples.reduceByKey( new Function2<Integer^ Integer, Integer>() {

^Override public Integer call(Integer il, Integer i2) { return Math.max(il, i2);

}

}

);

maxTemps.saveAsTextFile(args[l]);

}

}

在 Spark 的 Java API中，RDD以]avaRDD实例来表示，或者在遇到键-值对 RDD 这种特殊情况时，使用 HavaPairRDD 来表示。这两个类都实现了 ]avaRDDLike 接口，大多数与 RDD 相关的方法都可以在这个接口中找到，例如，通过查看类 文档。

前面这段程序的运行与 Scala 版程序的运行类似，只不过它的类名变成了

MaxTemperatureSpark0

###### 19.2.4 python 示例

Spark通过一个名为々的 API 来为 python 提供语言支持。利用 python 的 lambda表达式，我们可以重写前面的示例程序。范例 19-3所示的这段程序看上去 非常接近于等效的 Scala 程序。

范例 19-3.使用 PySpark 找出最高温度的 python 应用程序

from pyspark import SparkContext import re， sys

sc = SparkContext("local11, "Max Temperature")

sc.textFile(sys.argv[l]) \

•map(lambda s: s.split("\t")) \

.filter(lambda rec: (rec[l] != "9999" and re.match(n[01459]n, rec[2]))) \ •map(lambda pec: (int(rec[0]), int(rec[l]))) \

.reduceByKey(max) \

.saveAsTextFile(sys.argv[2])

请注意，对于 reduceByKey()转换，我们使用的是 python 内置的 max 函 前面这段程序是用常规的 Cpython 语言来写的，这一点很重要。Spark通过复刻 (fork)python子进程来运行用户的 python 代码(在启动程序以及集群上运行用户任 务的 execw/or中)，并且使用套接字连接两个进程，从而使父进程能够传递将要由 python代码处理的 RDD 分区数据。

为了运行这段代码，我们需要指定的是 python 文件，而不是应用 JAR:

% spark-submit --master local \ chl9-spark/src/main/python/MaxTemperature.py \ input/ncdc/micro-tab/sample.txt output

Spark还可以使用 pyspark 命令，以交互模式用 python 来运行。

##### 19.3弹性分布式数据

弹性分布式数据集(RDD)是所有 Spark 程序的核心，因此本节将详细讨论它们的使 用情况。

###### 19.3.1创建

RDD的创建有三种方法：来自一个内存中的对象集合(也称为并行化一个集合)； 使用外部存储器(例如 HDFS)中的数据集；对现有的 RDD 进行转换。第一种方法 适用于对少量的输入数据进行并行的 CPU 密集型计算。例如，下面这段代码对数 字 1 到 10 运行独立计算

val params = sc.parallelize(l to 10)

val result = params.map(performExpensiveComputation)

performExpensiveComputation



函数对输入的值并行运行



其并行度由



①这就像使用 MapReduce 中的 NLinelnputFormat 来执行参数扫描一样，参见 8.2.2节对 NLinelnputFormat 的描述。

spark.default.parallelism属性确定，默认值取决于 Spark 作业的地点。在

本地运行时，默认值就是该机器的内核(core)数，而在集群上运行时，它是集群中 所有 executor 节点的内核的总数。

如果把并行度作为第二个参数传给 parallelize()，可以覆盖特定计算的并行度：

sc.parallelize(l to 10, 10)

创建 RDD 的第二种方法是创建一个对外部数据集的引用。我们已经知道如何为文 本文件创建一个 String 对象的 RDD：

val text: RDD[String] = sc.textFile(inputPath)

其中的路径可以是任何 Hadoop 文件系统路径，例如本地文件系统或者 HDFS 上的 文件。Spark内部使用了旧的 MapReduce API的 TextlnputFormat 来读取文件 (参见 8.2.2节对 TextlnputFormat 的描述)，这意味着它的文件分割行为与 Hadoop中的一致，因此在使用 HDFS 的情况下，每个 HDFS 块对应于一个 Spark 分区。这个默认值可以通过传递第二个参数以请求特定的分割数量来更改：

sc>textFile(inputPath^ 10)

另一种变体是通过返回一个字符串对 RDD 来把文本文件作为一个完整的文件对待 (类似于 8.2.1节的“将整个文件作为记录处理”)，其中第一个字符串是文件路 径，第二个字符串是文件内容。由于每个文件都要被加载到内存中，因此这种方 式只适合小文件：

val files: RDD[(Stringy String)] = sc.wholeTextFiles(inputPath)

Spark也可以处理文本文件以外的其他格式。例如，以下代码可用于读取顺序 文件：

sc.sequenceFile[IntWritable, Text](inputPath)    :

其中值得注意的是顺序文件键和值的 Writable 类型是如何指定的。对于普通的 Writable类型，Spark可以将它们映射为等效的 Java 类型，因此我们也可以使用 以下形式，效果都是一样的：

sc.sequenceFile[Int， String](inputPath)

从任意 Hadoop InputFormat格式创建 RDD 的方法有两种：对于需要路径输入的 那些基于文件的格式可以使用 hadoopFile()，而对于不需要路径输入的格式(例 如 HBase 的 TablelnputFormat)则可以使用 hadoopRDD()0这些方法适用于旧

的 MapReduce API，而对于新的 MapReduce API来说，需要相应地使用

newAPIHadoopFile()和 newAPIHadoopRDD()o 下面这段代码是读取 Avro 文件的一个例子，它利用了包含 WeatherRecord 类的 Specific API：

:据



val job = new 3ob()

Avro3ob.setInputKeySchema(job^ WeatherRecord.getClassSchema) val data = sc.newAPIHadoopFile(inputPath,

classOf[AvroKeyInputFormat[WeatherRecord]],

classOf[AvroKey[WeatherRecord]classOf[NullWritable], job.getConfiguration)

除了路径之外，newAPIHadoopFile()方法还需要输入 InputFormat 类型、键类 型、值类型以及 Hadoop 的配置。在配置中包含了 Avro模式，我们在代码的第二 行使用 AvroJob 帮助类对它进行设置。

第三种方式是通过对现有 RDD 的转换来创建 RDD。下面我们将详细讨论转换。

###### 19.3.2转换和动作

Spark为 RDD 提供了两大类操作：转换(transformation)和动作(action)。转换是从 现有 RDD 生成新的 RDD，而动作则触发对 RDD 的计算并对计算结果执行某种操 作，要么返回给用户，要么保存到外部存储器中。

动作的效果立竿见影，但转换不是，转换是惰性的，因为在对 RDD 执行一个动作 之前都不会为该 RDD 的任何转换操作采取实际行动。例如，下面这段代码用于小 写字母化文本文件中的文本行：

val text = sc.textFile(inputPath)

val lower: RDD[String] = text.map(_<toLowerCase()) lower.foreach(printIn(_))

map()方法是一种转换操作，它在 Spark 内部被表示为可以在稍后某个时刻对输入 RDD(文本)的每个元素调用一个函数(即 toLowerCase())。在调用 foreach()方 法(这是一个动作)之前，该函数实际上并没有被调用。事实上，Spark在结果即将

被写入控制台之前，才会运行一个作业来读取输入文件并对其中的每一行文本调

用 toLowerCase()o

要想判断一个操作是转换还是动作，我们可以观察其返回类型：如果返回的类型 是 RDD，那么它是一个转换，否则就是一个动作。在翻阅 RDD 文档(在 org.apache.spark.rdd包中)时了解这一点会很有用。通过这些文档可以找到对 RDD执行的大多数操作。另外，在 PairRDDFunctions 类中可以找到用于键-值

对 KDD 的各种转换和动作。

在 Spark 库中包含了一组丰富的操作，包括映射、分组、聚合、重新分区、采 样、连接 RDD 以及把 RDD 作为集合来处理的各种转换，同时还包括将 RDD 物化 为集合；对 RDD 进行统计数据的计算；从一个 RDD 中采样固定数量的元素；以 及将 RDD 保存到外部存储器等各种动作，具体情况请参阅相应的类文档。

Spark 中的 MapReduce

Spark的 map()和 reduce()操作与 Hadoop MapReduce中的同名函数并没有直 接对应关系，尽管从命名上看好像是有。Hadoop MapReduce中的 map 和 reduce的一般形式为(参见第 8 章)：

map: (Kl, VI) -* list(K2, V2) reduce: (K2， list(V2)) list(K3， V3)

注意，这两个函数都可以返回多个输出对，它们以 list 来表示，而在 Spark(以及常规的 Scala)中，这种情况需要通过 flatMap()操作来实现。 flatMap()与 map()类似，只是少了一层嵌套：

scala> val 1 = List(l, 2， 3)

1: List[Int] = List(l, 2, 3〉

scala> l.map(a => List(a))

res0: List[List[Int]] = List(List(l)， List(2), List(3))

scala> l.flatMap(a => List(a)) resl: List[Int] = List(l, 2, 3)

想要在 Spark 中模拟 Hadoop MapReduce的一种简单的方法是使用两个 flatMap()操作，并且两者之间使用 groupByKey()和 sortByKey()分隔， 它们分别执行的是 MapReduce 的混洗(shuffle)和排序(sort)操作：

val input: RDD[(K1, VI)]=...

val mapOutput: RDD[(K2， V2)] = input.flatMap(mapFn)

val shuffled: RDD[(K2, Iterable[V2])] = mapOutput.groupByKey().sortByKey() val output: RDD[(K3, V3)] = shuffled.flatMap(reduceFn)

在这里，键的类型 K2 需要继承 Scala 的 Ordering 类型才能满足 sortByKey()的要求。这个例子可能有助于了解 MapReduce 和 Spark 之间的

关系，但不应盲目应用。其中一个原因是 sortByKey()执行的是全排序， 所以它的语义与 Hadoop MapReduce中的略有不同。通过使用 repartitionAndSortWithinPartitions()来执行部分排序可以避免此问

题，但即使这样做，效率还是不高, groupByKey(), 一个用于排序)。



3为 Spark 使用了两个 shuffle(—个用于



与其试图模仿 MapReduce，还不如使用你实际需要的操作。例如，如果不需要 按键排序，那么可以省略 sort By Key ()的调用(这在常规的 Hadoop MapReduce 中是不可能的)。

类似地，groupByKey()在大多数情况下会显得过于笼统。通常，我们只是想 利用 shuffle 来聚合相应的值，因此使用 reduceByKey()、foldByKey()或者 aggregateByKey()(在下一节介绍)要比使用 groupByKey()效率更高，因为 它们也可以作为 combiner 在 map 任务中运行。最后，flatMap()也不是必须 的，如果总是只有一个返回值，那么首选使用 map()，而如果有零个或一个返 回值，则应当使用 filter()。

聚合转换

按键来为键-值对 RDD 进行聚合操作的三个主要转换函数分别是

reduceByKey()、foldByKeyO和 aggregateByKey()o 它们的工作方式稍有不 同，但都用干聚合给定键的值，并为每个键产生一个值。(对应的等效动作分别是

reduce()、fold()和 aggregate()，它们以类似的操作方式为整个 RDD 产生一

个值。)

其中最简单的是 reduceByKey()，它为键-值对中的值重复应用一个二进制函数， 直至产生一个结果值。例如：

val pairs: RDD[(Strings Int)]=

sc.parallelize(Array(("a", 3), ("a", 1), ("b", 7), ("a", 5)))

val sums: RDD[(String, Int)] = pairs.reduceByKey(_+_) assert(sums.collect().toSet === Set(("a", 9), ("b", 7)))

键 a 的值使用加法函数(_七)来聚合，即(3 + /)+5 = 9，而键 b 只有一个值，因此 不需要聚合。由于上述操作一般来说是分布式的，它们根据 RDD 的不同分区在不 同的任务中执行，因此这些函数应当是可交换和可结合的。换句话说就是操作的 先后次序及两两之间的结合关系并不重要。在这种情况下，上述聚合操作可以是 5 +(5 + 7)，也可以是 3+(/+ 5)，两者返回的结果相同。

assert语句中使用的三元等于运算符(===)来自于 ScalaTest，比起使用常规 的==运算符，它可以提供更多有意义的失败消息。

下面这段代码给出了如何利用 foldByKeyO 来执行相同的操作：

val sums: RDD[(String， Int)] = pairs.foldByKey(0)(一+一) assert(sums.collect()•toSet === Set(("a", 9), ("b", 7)))

请注意，这一次我们必须要提供一个零值(zero value)，在做整数加法时它就是 0， 但是对于其他的类型及操作来说，它有可能是不同的。此处，a的值聚合为((0 + J) + 7) + 5) = 9(也可以是其他顺序，但是，第一个操作必定是加 0)。对于 b 则是 0 + 7=70

使用 foldByKey()与使用 reduceByKey()—样，没有谁比谁更强大，尤其是两者 都不能改变聚合结果值的类型。要想达到这个目的，我们需要用 aggregateByKey()。 例如，我们可以将一些整数值聚合形成一个集合：

val sets: RDD[(String, HashSet[Int])]=

pairs.aggregateByKey(new HashSet[Int])(一+=」一++=」

assert(sets.collect().toSet === Set((Ma", Set(l, 3, 5)), ("b", Set(7))))

对于集合加法运算来说，零值就是空集，因此我们使用 new HashSet[Int]来创 建一个新的可变集合。必须为 aggregateByKey()提供两个输入的函数。第一个 函数负责把 Int 合并到 HashSet[Int]中。此处，我们使用_+=_函数来把整数添 加到集合中(如果使用_+_，则返回一个新集合并保留第一个集合不变)。

第二个函数负责合并两个 HashSet[Int]中的值(此操作发生在 map 任务的 combiner运行之后，并且在 reduce 任务的两个分区进行聚合时)。此处我们用 ++=函数把第二个集合中的所有元素添加到第一个集合中。

对于键 a，操作顺序可能如下所示:

((0 + 3) + 1) + 5) = (1, 3, 5)

或者，如果 Spark 使用了一个 combiner，则如下所示：

(0 + 3) + 1) ++ (0 + 5) = (1, 3) ++ (5) = (1, 3, 5)

转换后的 RDD 可以持久化存储在内存中，以提高其后的操作效率，关于这个问题 我们将在下面讨论。

###### 19.3.3持久化

回顾 19.2节介绍的示例，我们可以用下述命令把年份/温度对构成的中间数据集缓 存到内存中：

scala> tuples.cache()

resl: tuples•type = MappedRDD[4] at map at <console>:18

调用 cache()并不会立即缓存 RDD，相反，它用一个标志来对该 RDD 进行标 记，以指示该 RDD 应当在 Spark 作业运行时被缓存。因此，让我们先强制运行一 个作业：

scala> tuples.reduceByKey((a, b) => Math.max(a, b)).foreach(println(一))

INFO BlockManagerlnfo: Added rdd_4_0 in memory on 192.168.1.90:64640 INFO BlockManagerlnfo: Added rdd_4_l in memory on 192.168.1.90:64640 (1950,22)    一

(1949,111)

来自 BlockManagerlnfo 的日志行表明 RDD 分区已作为作业运行的一部分保存 在内存中。日志显示 RDD 的编号为 4(这个编号在调用 cache()方法后显示在控 制台上)，它有两个分区，分别被标记为 0 和 1。如果我们对缓存的数据集运行另 一个作业，将会看到从内存中加载该 RDD。这次我们要计算的是最低温度：

scala> tuples.reduceByKey((a, b〉 => Math.min(a, b)).foreach(printIn(_))

INFO BlockManager: Found block rdd_4_0 locally INFO BlockManager: Found block rdd_4_l locally (1949,78)

(1950,-11)

这只是一个简单的小型数据集示例，对于大规模作业来说，这样做能够节省可观 的时间。相比较而言，MapReduce在执行另一个计算时必须从磁盘中重新加载输 入数据集，即使它可以使用中间数据集作为输入(例如，经过了对无效行和不必要 字段清理的数据集)，也始终无法摆脱必须从磁盘加载的事实，这必然会影响其执 行速度。Spark可以在跨集群的内存中缓存数据集，这也就意味着对数据集所做的 任何计算都会非常快。

事实证明，这对于数据的交互式探索査询(interactive exploration)非常有用。它天 生适用于某些特定风格的算法，例如迭代算法。在迭代算法中，上一次迭代计算 的结果可以被缓存在内存中，以用作下一次迭代的输入。这些算法也可以用 MapReduce来表示，但是每次迭代都要作为单个 MapReduce 作业来运行，因此每 次迭代的结果必须写入磁盘，然后在下一次迭代时从磁盘中读回。

![img](Hadoop43010757_2cdb48_2d8748-231.jpg)



被缓存的 RDD 只能由同一应用的作业来读取。如果要在应用之间共享数据 集，则必须在第一个应用中使用 saveAs*()方法(譬如 saveAsTextFile()、 saveAsHadoopFile()等等)将其写入外部存储器，然后在第二个应用中使用 SparkContext的相应方法(如 textFile()、hadoopFile()等等)进行加载。同

理，当应用终止时，它缓存的所有 RDD 都将被销毁，除非这些 RDD 已被显式 保存，否则无法再次访问。

持久化级别

调用 cache()将会在 executor 的内存中持久化保存 RDD 的每个分区。如果 executor没有足够的内存来存储 RDD 分区，计算并不会失败，只不过是根据需要 重新计算分区。对于包含大量转换操作的复杂程序来说，重新计算的代价可能太 高，因此 Spark 提供了不同级别的持久化行为，我们可以通过调用 persist()并 指定 StorageLevel 参数来做出选择。

默认的持久化级别是 MEMORY_ONLY，它使用对象在内存中的常规表示方法。另一

种更紧凑的表示方法是通过把分区中的元素序列化为字节数组来实现的。这一级 别称为 MEMORY_ONLY_SER。与 MEMORY一 ONLY 相比，MEMORY_ONLY_SER 多了一份 CPU开销，但是，如果它生成的序列化 RDD 分区的大小适合被保存到内存中， 而常规的表示方法却无法做到这一点时，这份额外开销就是值得的。 MEMORY_ONLY_SER还能减少垃圾回收的压力，因为每个 RDD 被存储为一个字节 数组，而不是大量的对象。

通过检査 driver 日志文件中的 BlockManager 消息，可以了解 RDD 分区的大小 ■k 是否适合被保存到内存中。此外，每个 driver 的 SparkContext 都运行了一个

HTTP服务器(端口 4040)，可提供运行环境以及正在运行的作业的相关信息， 包讎雜 RDD 舰的信息。

默认情况下，RDD分区的序列化使用的是常规的 Java 序列化方法，但是，无论从 大小还是速度来看，使用 Kryo 序列化方法(在下一小节介绍)通常都是更好的选 择。通过压缩序列化分区可以进一步节省空间(再次以牺牲 CPU 为代价)，其做法 是把 spark.rdd.compress属性设置为 true，并且可选地设置 spark. io. compression.codec 属性 0 如果重新计算数据集的代价太过高昂，那么可以使用 MEMORY_AND_DISK(如果数 据集的大小不适合保存到内存中，就将其溢出到磁盘)，或 MEMORY_ANDJ)ISK_SER (如果序列化数据集的大小不适合保存到内存中，就将其溢出到磁盘)。

另外还有一些更高级的以及实验性的持久化级别，它们可用于在集群中的多个节 点上复制分区，或者也可以使用堆外内存，详情请参阅 Spark 文档。

###### 19.3.4序列化

在使用 Spark 时，要从两个方面来考虑序列化：？

〔据序列化和函数序列化（或称为



闭包函数）。

1.数据

让我们先来了解一下数据序列化。默认情况下，Spark在通过网络将数据从一个 executor发送到另一个 executor 时，或者以序列化的形式缓存（持久化）数据时（参见 19.3.3节），所使用的都是 Java 序列化机制。Java序列化机制为程序员所熟知（你必 须确保所使用的类实现了 java.io.Serializable 或 java .io. Externalizable 接 口），但从性能或大小来看，这种做法效率并不高。

使用 Kryo 序列化机制（Zz即对于大多数 Spark 程 序都是一个更好的选择。Kryo是一个高效的通用 Java 序列化库。要想使用 Kryo 序列化机制，需要在你的驱动器程序的 SparkConf 中设置 spark.serializer属 性，如下所示：

conf.set（"spark.serializer", Horg.apache.spark.serializer.KryoSerializer"）

Kryo不要求被序列化的类实现某个特定的接口（例如 java.io.Serializable）。 因此，旧的纯 Java 对象也可以在 RDD 中使用，除了需要启用 Kryo 序列化之外， 没有什么更多的工作需要做。话虽如此，但是如果使用之前先在 Kryo 中对这些类 进行注册，那么可以提高其性能。这是因为 Kryo 需要写被序列化对象的类的引用 （每个被写的对象都需要写一个引用），如果已经注册了类，那么该引用就只是一个 整数标识符，否则就是完整的类名。上述原则仅适用于你自己的类，Spark已经代 你注册了 Scala类和其他一些框架类（比如 Avro Generic类或 Thrift 类）。

在 Kryo 中注册类很简单，先创建一个 KryoRegistrator 子类，然后重写 registerClasses（）方法：

class CustomKryoRegistrator extends KryoRegistrator { override def registerClasses(kryo: Kryo) {

kryo.register(classOf[WeatherRecord])

最后，在 driver 程序中将 spark .kryo. registrator 属性设置为你的 KryoRegistrator

实现的完全限定类名：

conf.set("spark.kryo.registrator.、 "CustomKryoRegistrator")

2.函数

通常函数的序列化会“谨守本份”：Scala中的函数都可以通过标准的 Java 序列化 机制来序列化，这也是 Spark 用于向远程 executor 节点发送函数的手段。但是对 Spark来说，即使在本地模式下运行，也需要序列化函数，因此假若你无意中引入 了一个不可序列化的函数(例如，从不可序列化的类的方法转换得到的函数)，那么 你应该在开发初期就会发现它。

##### 19.4共享变量

Spark程序经常需要访问一些不属于 RDD 的数据。例如，下面这段程序在 map() 操作中用到了一张查找表(lookup table)：

val lookup = Map(l -> "a", 2 -> "e", 3 -> "i", 4 -> "o", 5 -> "u") val result = sc. parallelize (Array (2^ 1, 3)) .map(lookup(」) assert(result.collect().toSet === Set("aM, "e", "i"))

虽然这段程序可以正常工作(变量 lookup 作为闭包函数的一部分被序列化后传递 给 mapO)，但是使用广播变量可以更高效地完成相同的工作。

564 第 19 章

###### 19.4.1广播变量

广播变量(broadcast variable)在经过序列化后被发送给各个 executor，然后缓存在 那里，以便后期任务可以在需要时访问它。它与常规变量不同，常规变量是作为 闭包函数的一部分被序列化的，因此它们在每个任务中都要通过网络被传输一 次。广播变量的作用类似于 MapReduce 中的分布式缓存(参见 9.4.2节)，两者的不 同之处在于 Spark 将数据保存在内存中，只有在内存耗尽时才会溢出到磁盘上。

我们通过向 SparkContext 的 broadcast^)方法传递即将被广播的变量来创建一 个广播变量。它返回 Broadcasts]，即对类型为 T 的变量的一个封装：

val lookup: Broadcast[Map[Int^ String]]=

sc.broadcast(Map(l -> "a", 2 -> "e", 3 •>    4 -> no", 5 •> "un))

val result = sc.parallelize(Array(2^ 1, 3)).map(lookup.value(_)) assert(result.collect().toSet === Set("a", "e", "i"))

请注意，要想在 RDD 的 map()操作中访问这些变量，需要对它们调用 value。

顾名思义，广播变量是单向传播的，即从 driver 到任务，因此一个广播变量是没 有办法更新的，也不可能将更新传回 driver。要想做到这一点，我们需要累加器。

###### 19.4.2累加器

累加器(accumulator)是在任务中只能对它做加法的共享变量，类似于 MapReduce 中的计数器(参见 9.1节)。当作业完成后，driver程序可以检索累加器的最终值。

下面这个例子使用累加器来对一个整数 RDD 中的元素个数进行计数，同时使用 reduce()动作对 RDD 中的值求和：

val count: Accumulator[Int] = sc.accumulator(O) val result = sc.parallelize(Array(1^ 2, 3))

.map(i => { count += 1; i })

.reduceCCx, y) => x + y)

assert(count.value === 3)

assert(result === 6)

代码的第一行使用了 SparkContext的 accumulator()方法来创建一个累加器变 量 count。map()操作是一个恒等函数，其附带效果是使 count 递增。当 Spark 4乍业的结果被计算出来后，可以通过对累加器调用 value 来访问它的值。

在这个例子中，累加器的类型为 Int，事实上它可以是任意的数值类型。Spark还 可以让累加器的结果类型与加数类型不同(参见 SparkContext 的 accumulable() 方法)，也能够累积可变集合中的值(通过 accumulableCollection()方法)。

##### 19.5剖析 Spark 作业运行机制

下面来看看当我们运行 Spark 作业时会发生些什么。在最高层，它有两个独立的 实体：driver    executor。driver负责托管应用(SparkContext)并为作业调度任

务。executor专属于应用，它在应用运行期间运行，并执行该应用的任务。通常， driver作为一个不由集群管理器(cluster manager)管理的客户端来运行，而 executor 则运行在集群的计算机上。不过，也并不总是这样(参见 19.6节)。在下面的讨论 中，我们假设应用的 executor 已经运行。

###### 19.5.1作业提交

图 19-1描绘了 Spark运行作业的过程。当对 RDD 执行一个动作(比如 count()) 时，会自动提交一个 Spark 作业。从内部看，它导致对 SparkContext 调用 run]ob()(图 19-1中的步骤 1)，然后将调用传递给作为 driver 的一部分运行的调 度程序(步骤 2)。调度程序由两部分组成：DAG调度程序和任务调度程序。DAG 调度程序把作业分解为若干阶段，并由这些阶段构成一个 DAG。任务调度程序则 负责把每个阶段中的任务提交到集群。

19-1. Spark如何运行作业



SparkContext

DAGScheduler

TaskScheduler

4: launch task

SchedulerBackend

a

5: launch task



ExecutorBackend

6: launch

Executor

executor



接下来，让我们看看 DAG 调度程序如何构建一个 DAG。

###### 19.5.2 DAG的构建

要想了解一个作业如何被划分为阶段，首先需要了解在阶段中运行的任务的类

型。有两种类型的任务：shuffle map任务和 result 任务。从任务类型的名称可以 看出 Spark 会怎样处理任务的输出。

shuffle map 任务顾名思义，shuffle map 任务就像是 MapReduce 中 shuffle 的 map 端部分。每个 shuffle map任务在一个 RDD 分区上运行计算，并根据分区函数把 输出写入一组新的分区中，以允许在后面的阶段中取用(后面的阶段可能由 shuffle map任务组成，也可能由 result 任务组成)。，shuffle map任务运行在除最终阶段 之外的其他所有阶段中。

result任务 result 任务运行在最终阶段，并将结果返回给用户程序(例如 count() 的计算结果)。每个 result 任务在它自己的 RDD 分区上运行计算，然后把结果发送 f'l driver，再由 driver 将每个分区的计算结果汇集成最终结果(比如，在 saveAsTextFile()操作的情况下，结果有可能是一个 Unit)。

最简单的 Spark 作业不需要使用 shuffle，因此它只有一个由 result 任务构成阶段， 这就像是 MapReduce 中的仅有 map 的作业一样。

比较复杂的作业要涉及到分组操作，并且要求一个或多个 shuffle 阶段。例如，下 而这个作业用于为存储在 inputPath 目录下的文本文件计算词频统计分布图(每行 文本只有一个单词)：

val hist: Map[Int， Long] = sc.textFile(inputPath)

.map(word => (word.toLowerCase(), 1))

.reduceByKey((a， b) => a + b)

.map(_,swap)

.countByKey()

前两个转换是 map()和 reduceByKey()，它们用于计算每个单词出现的频率。第 三个转换是 map()，它交换每个键-值对中的键和值，从而得到(cm/价，mwy/)对。 最后是 countByKey()动作，它返回的是每个计数对应的单词量(即词频分布)。

由于 reduceByKey()必须要有 shuffle 阶段，因此 Spark 的 DAG 调度程序将此作 业分为两个阶段。®结果得到的 DAG 如图 19-2所示。

通常，每个阶段的 RDD 都要在 DAG 中显示，并且在 DAG 图中给出了这些 RDD 的类型以及创建它的操作。例如，RDD[String]是由 textFile()创建的。为了简 化，图中省略了 Spark内部产生的一些中间 RDD。例如，由 textFile()返回的

①注意，countByKey()在本地 driver 上执行最后的聚合操作，而不是使用第二个 shuffle 阶段。 这与示例 18-3中等效的 Crunch 程序不同，后者使用了第二个 MapReduce 作业用于计数。

RDD实际上是一个 MappedRDD[String]，而其父对象是 HadoopRDD [LongWritable， Text]0

Stage 1 (shuffle map tasks)



‘詞

埔.



![img](Hadoop43010757_2cdb48_2d8748-235.jpg)



.J".'    r>'

•    •、    .7，.：^u，V    '    ；. • ；    rz*

••激 r

textRleO

翁

RDD[(String, Int)]



，鱗賜

map（）



![img](Hadoop43010757_2cdb48_2d8748-237.jpg)



![img](Hadoop43010757_2cdb48_2d8748-238.jpg)



![img](Hadoop43010757_2cdb48_2d8748-239.jpg)



...•，.

':議参’辨游觀

：7AV

RDD((String, Int)]

reduceByKeyO

g. ‘ I '.'：；.：；

\+ RDD deoendencv



19-2.用于计算词频统计分布图的 Spark 作业中的各个阶段以及 RDD

生意，reduceByKey（）转换跨越了两个阶段，这是因为它是使用 shuffle 实现的， 浮且就像 MapReduce 一样，reduce函数一边在 map 端作为 combiner 运行（阶段

1），一边又在 reduce 端又作为 reducer 运行（阶段 2）。它与 MapReduce 相似的另一 个地方是，Spark的 shuffle 实现将其输出写入本地磁盘上的分区文件中（即使对内 存中的 RDD 也一样），并且这些文件将由下一个阶段的 RDD 读取。® 如果 RDD 已经被同一应用（SparkContext）中先前的作业持久化保存，那么 DAG 调度程序将会省掉一些工作，不会再创建一些阶段来重新计算它（或者它的父

①可以通过配置（//即。来调整 shuffle 的性能。同时还要注意，Spark使用 f自己定义的 shuffle 实现，并没有与 MapReduce 的 shuffle 实现共享任何代码。

RDD)0

dag调度程序负责将一个阶段分解为若干任务以提交给任务调度程序。在本例的 第一个阶段中，输入文件的每个分区运行一个 shuffle map任务。reduceByKey（） 操作的并行度可以通过传递第二个参数来显式设置。如果没有设置并行度，则根 据父 RDD 来确定，在这种情况下就是输人数据的分区数。

DAG调度程序会为每个任务赋予一个位置偏好（placement preference），以允许任务 调度程序充分利用数据本地化（data locality）。例如，对于存储在 HDFS 上的输入 RDD分区来说，它的任务的位置偏好就是托管了这些分区的数据块的 datanode（称 为 node local），而对于在内存中缓存的 RDD 分区，其任务的位置偏好则是那些保 存 RDD 分区的 executor（称为 process local）0

回到图 19-1，一旦 DAG 调度程序已构建一个完整的多阶段 DAG，它就将每个阶 段的任务集合提交给任务调度程序（步骤 3）。子阶段只有在其父阶段成功完成后才 能提交。

###### 19.5.3任务调度

当任务集合被发送到任务调度程序后，任务调度程序用为该应用运行的 executor 的列表，在斟酌位置偏好的同时构建任务到 executor 的映射。接着，任务调度程 序将任务分配给具有可用内核的 executor（如果同一应用中的另一个作业正在运 行，则有可能分配不完整），并且在 executor 完成运行任务时继续分配更多的任

务，直到任务集合全部完成。默认情况下，每个任务到分派一个内核，不过也可 以通过设置 spark.task.cpus来更改。

清注意，任务调度程序在为某个 executor 分配任务时，首先分配的是进程本地 （process-local）任务，再分配节点本地（node-local）任务，然后分配机架本地（rack-local）任务，最后分配任意（非本地）任务或者推测任务（speculative task），如果没有 其他任务候选者的话。®

这些被分配的任务通过调度程序后端启动（图 19-1中的步骤 4）。调度程序后端向 executor后端发送远程启动任务的消息（步骤 5），以告知 executor 开始运行任务（步 骤 6）0

①推测任务是现有任务的复本，如果任务运行得比预期的缓慢，则调度程序可以将其作为备份 来运行，详情可以参见 7.4.2节。

![img](Hadoop43010757_2cdb48_2d8748-241.jpg)



Spark利用 Akka （一个基于 Actor 的平台，来构建髙度可扩展的 事件驱动分布式应用，而不是使用 HadoopRPC 进行远程调用。

当任务成功完成或者失败时，executor都会向 driver 发送状态更新消息。如果失败 了，任务调度程序将在另一个 executor 上重新提交任务。若是启用了椎测任务（默 认情况下不启用），它还会为运行缓慢的任务启动推测任务。

###### 19.5.4任务执行

Executor以如下方式运行任务（步骤 7）。首先，它确保任务的 JAR 包和文件依赖关 系都是最新的。executor在本地高速缓存中保留了先前任务已使用的所有依赖，因 此只有在它们更新的情况下才会重新下载。第二步，由于任务代码是以启动任务 消息的一部分而发送的序列化字节，因此需要反序列化任务代码（包括用户自己的 函数）。第三步，执行任务代码。请注意，因为任务运行在与 executor 相同的 JVM 中，因此任务的启动没有进程开销。®

任务可以向 driver 返回执行结果。这些执行结果被序列化并发送到 executor 后 端，然后以状态更新消息的形式返回 driver。shuffle map任务返回的是一些可以让 下一个阶段检索其输出分区的信息，而 result 任务则返回其运行的分区的结果 值，driver将这些结果值收集起来，并把最终结果返回给用户的程序。

##### 19.6执行器和集群管理器

我们已经看到 Spark 如何依靠 executor（执行器）来运行构成 Spark 作业的任务，但 是对 executor 实际上是如何开始工作的却只粗略带过。负责管理 executor 生命周 期的是集群管理器（cluster manager）, Spark提供了好多种具有不同特性的集群管理器。

本地模式在使用本地模式时，有一个 executor 与 driver 运行在同一个 JVM 中。 此模式对于测试或运行小规模作业非常有用。这种模式的主 URL 为 local（使用 一个线程）、local[n]（n个线程）或 local（*）（机器的每个内核一个线程）。

独立模式独立模式的集群管理器是一个简单的分布式实现，它运行了一个

①在 Mesos 细粒度模式下，情况并非如此，它的每个任务作为单独的进程运行。有关详情，请 参阅下一节。

master以及一个或多个 worker。当 Spark 应用启动时，master要求 worker 代表应 用生成多个 executor 进程。这种模式的主 URL 为 spark:///zo5/:porZo

Mesos模式 Apache Mesos是一个通用的集群资源管理器，它允许根据组织策略 在不同的应用之间细化资源共享。默认情况下（细粒度模式），每个 Spark 任务被当 作是一个 Mesos 任务运行。这样做可以更有效地使用集群资源，但是以额外的进 程启动开销为代价。在粗粒度模式下，executor在进程中运行任务，因此在 Spark 应用运行期间的集群资源由 executor 进程来掌管。这种模式的主 URL 为 mesos..//host'po"。

YARN模式 YARN 是 Hadoop 中使用的资源管理器（参见第 4 章）。每个运行的 Spark应用对应于一个 YARN 应用实例，毎个 executor 在自己的 YARN 容器中运 行。这种模式的主 URL 为 yarn-client 或 yarn-cluster。

Mesos和 YARN 集群管理器优于独立模式的集群管理器，因为它们考虑了在集群 上运行的其他应用（例如 MapReduce 作业）的资源需求，并统筹实施调度策略。独 立模式的集群管理器对集群的资源采用静态分配方法，因此不能随时适应其他应 用的变化的需求。此外，YARN是唯一一个能够与 Hadoop 的 Kerberos 安全机制

、集成的集群管理器（参见 10.4节）。

###### 运行在 YARN 上的 Spark

在 YARN 上运行 Spark 提供了与其他 Hadoop 组件最紧密的集成，也是在已有 Hadoop集群的情况下使用 Spark 的最简便的方法。为了在 YARN 上运行，Spark 提供了两种部署模式：yarn客户端模式和 YARN 集群模式。YARN客户端模式 的 driver 在客户端运行，而 YARN 集群模式的 driver•在 YARN 的 application master集群上运行。

对尸具有任何交互式组件的程序（例如 spark-shell或冲）都必须使用 YARN 客 户端模式。客户端模式在构建 Spark 程序时也很有用，因为任何调试输出都是立 即可见的。

另一方面，YARN集群模式适用于生成作业（production job），因为整个应用在集群 上运行，这样做更易于保留日志文件（包括来自 driver 的日志文件）以供稍后检查。 如果 application master出现故障，YARN还可以尝试重新运行该应用，详情参见 7.2.2 节）0

\1. YARN客户端模式

在 YARN 客户端模式下，当 driver 构建新的 SparkContext 实例时就启动了与 YARN之间的交互（图 19-3中的步骤 1）。该 Context 向 YARN 资源管理器提交一个 YARN应用（步骤 2），YARN资源管理器则启动集群节点管理器上的 YARN 容器， 并在其中运行一个名为 SparkExecutorLauncher 的 application master（步骤 3）。 ExecutorLauncher的工作是启动 YARN 容器中的 executor，为了做到这一点， ExecutorLauncher要向资源管理器请求资源（步骤 4），然后启动 ExecutorBackend 进程作为分配给它的容器（步骤 5）。

19-3.在 YARN 客户端模式下 Spark executor的启动流程



![img](Hadoop43010757_2cdb48_2d8748-243.jpg)



每个 executor 在启动时都会连接回 SparkContext，并注册自身。这就向 SparkContext提供了关于可用于运行任务的 executor 的数量及其位置的信息， 这些信息被用在任务的位置偏好策略中（参见 19.5.3节）。启动的 executor 的数量在 spark-shell、吵 tzrhw/wnY或;?戸戸 rA:中设置（如果未设置，则默认为两个），同时还 要设置每个 executor 使用的内核数（默认值为 1）以及内存量（默认值为 1,024 MB）。 下面这个例子显示了如何在 YARN 上运行具有 4 个 executor 且每个 executor 使用 1个内核和 2 GB内存的 spark-shell:

% spark-shell --master yarn-client \

--num-executors 4 \

垂师 executor*-cor*es 1 \

--executor-memory 2g

YARN资源管理器的地址并没有在主 URL 中指定（这与使用独立模式或 Mesos 模 式的集群管理器不同），而是从 HADOOP_CONF_DIR环境变量指定的目录中的 Hadoop配置中选取。

\2. YARN集群模式

在 YARN 集群模式下，用户的 driver 程序在 YARN 的 application master进程中运 行。使用 spark-submit命令时需要输入 yarn-cluster的主 URL：

% spark-submit --master yarn-cluster …

所有其他参数，比如--num-executors和应用 JAR（或 python 文件），都与 YARN 客户端模式相同（具体用法可通过 spark-submit --help获得）。

客户端将会启动 YARN 应用（图 19-4中的步骤 1），但是它不会运行任 何用户代码。剩余过程与客户端模式相同，除了 application master在为 executor 分配资源（步骤 4）之前先启动 driver 程序（步骤 3b）外。

• ••



spark-

submit



1: create



Spark

client



client JVM (driver)

I . ，為    . L 4 '' • ' I • ’ 1 ** V J *» .'.V . • • •



麵麵■鷗錐

•麵■ ■ J



2: submit YARN

application i resource manager node



client node



3a: start container

r —    — — • — —-

4: allocate resources (heartbeat)

NodeManager

_

3b: launch

5a: start container

node manager node



node manager node

l -



19-4•在 YARN 集群模式下 Spark executor的启动流程

在这两种 YARN 模式下，executor都是在还没有任何本地数据位置信息之前先启 动的，因此最终有可能会导致 executor 与存有作业所希望访问文件的 datanode 并 不在一起。对于交互式会话，这是可以接受的，特别是因为会话开始之前可能并 不知道需要访问哪些数据集。但是对于生成作业来说，情况并非如此，所以 Spark 提供了一种方法，可以在 YARN 群集模式下运行时提供一些有关位置的提示，以 提高数据本地性。

SparkContext构造函数可以使用第二个参数来传递一个优选位置。这个优选位 置是利用 InputFormatlnfo 辅助类根据输入格式和路径计算得到的。例如，对于 文本文件，我们使用的是 TextlnputFormat:

val preferredLocations = InputFormatlnfo.computePreferredLocations(

Seq(new InputFormatInfo(new Configuration(), classOf[TextlnputFormat], inputPath)))

val sc = new SparkContext(confpreferredLocations)

当向资源管理器请求分配时，application master需要用到这个优选位置(步骤 4)。1

##### 19.7延伸阅读

本章只讨论了 Spark的一些基础知识。更多细节请参阅 O’Reilly在 2014 出版的 Learning Spark，网址为 <http://shop.oreilly.com/product/> 0636920028512.do，作者 Holden Karau、Andy Konwinski、Patrick Wendell 和 Matei Zaharia。Apache Spark 网站(//即上还有关于最新 Spark 发行版的更新文档。

①在写这一章内容时，最新版本 Spark 1.2.0中优选位置 API 还不太稳定，町能在以后的版本中 会有所改善。


