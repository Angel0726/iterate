---
title: 14 关于Flume
toc: true
date: 2018-06-27 07:51:33
---
#### 第 14 章

![img](Hadoop43010757_2cdb48_2d8748-170.jpg)



Flume



Hadoop的构建宗旨是处理大型数据集。通常，我们假设这些数据已经存储在 HDFS中或者能够随时批量复制到 HDFS。然而，有许多系统并不符合此假设。这 些系统生产出我们想要通过 Hadoop 来汇总、存储和分析的数据流。与这些系统打 交道，Apache Flume （h即://flu/ne.apache.org/）再合适不过。

没计 Flume 的宗旨是向 Hadoop 批量导入基于事件的海量数据。一个典型的例子是 利用 Flume 从一组 Web 服务器中收集日志文件，然后把这些文件中的日志事件转 移到一个新的 HDFS 汇总文件中以做进一步处理，其终点（或者用 Flume 的术语称 之为 sink）通常为 HDFS。不过，Flume具有足够的灵活性，也可以将数据写到其 他系统中，例如 HBase 或 Solr。

要想使用 Flume，就需要运行 Flume 代理。Flume代理是由持续运行的 source（

Jit/



据来源）、sink（数据目标似及 channel（用于连接 source 和 sink）构成的 Java 进程。 Flume的 source 产生事件，并将其传送给 channel，channel存储这些事件直至转发 给 sink。可以把 source-channel-sink的组合视为基本 Flume 构件。

Flume由一组以分布式拓扑结构相互连接的代理构成。系统边缘的代理（例如，与 Web服务器共存于同一台机器上的代理）负责采集数据，并把数据转发给负责汇总 的代理，然后再将这些数据存储到其最终目的地。代理通过配置来运行一组特定 的 source 和 sink。因此，使用 Flume 所要做的主要工作就是通过配置使得各个组 件融接到一起。本章将展示如何构建用于收集数据的 Flume 拓扑结构，你也可以把它 当作是自己的 Hadoop 管道的一部分。

##### 14.1 安装 Flume

从下载页面即:/况"騰•叩沉下载一个稳定版本的 Flume 二进 制发行包，并在适当的位置解压缩该文件包：

% tar xzf apache-flume-x.y.z-bin.tar.gz

为了方便起见，可以把 Flume 的二进制文件路径添加到自己的路径中:

% export FLUME—HOME=~/sw/apache-flume-x.y.z-bin % export PATH=$PATH:$FLUME_HOME/bin

然后，使用 Flume-ng命令启动 Flume 代理，正如我们下面将要看到的。

##### 14.2示例

为了演示 Flume 是如何工作的，我们首先从以下设置出发。

(1)    监视新增文本文件所在的本地目录。

(2)    每当有新增文件时，文件中的每一行都将被发往控制台。

本例中的新增文件是由手工添加的，不过也不难想像由一个 Web 服务器之类的进 程不断创建我们希望利用 Flume 采集的新文件。此外，对于实际系统而言，我们 需要的不仅仅是记录文件的内容，而是要将文件内容写入 HDFS 以待后续处理， 本章稍后介绍。

在本例中，Flume代理仅运行了一个 source-channel-sink组合，并且它利用 Java 属 性文件进行配置。这些配置控制着 source、sink和 channel 的类型以及它们的连接 方式。这个例子所使用的配置如范例 14-1所示。

范例 14-1.使用 spooling directory source 和 logger sink 的 Flume 配置

agentl.sources = sourcel

agentl.sinks = sinkl

agentl.channels = channell agentl.sources.sourcel.channels = channell agent1.sinks.sinkl♦channel = channell

agentl.sources.sourcel.type = spooldir

agentl.sources.sourcel.spoolDir = /tmp/spooldir

agent1.sinks.sinkl.type = logger agent1.channels.channell.type = file

属性名称构成了一个分级结构，顶级为代理的名称。在本例中只有一个代理，其 名称为 agentl。下一级定义的是代理中的不同组件的名称，因此，以 agentl.sources为例，它列出在 agentl 中运行的 source 的名称（本例只有一个 source，即 sourcel）。同样，agentl 只有一个 sink（即 sinkl）和一个 channel（即 channell）0

再下一级是组件的属性。组件可用的属性取决于该组件的类型。在本例中，

agentl.sources.sourcel.type 设置为 spooldir，它是一个 spooling directory source，用于监视缓冲目录中的新增文件。Spooling directory source有一个称为 spoolDir的属性定义，因此，对于 sourcel 来说，完整的键应当是 agentl. sources.sourcel.spoolDir。source 所使用的 channel 贝樋过 agentl.sources, sourcel.channels 属性来设置。

在本例中，sink是一个 logger sink，用于将事件记录到控制台。该 sink 必须连接 channell（通过 agentl.sinks.sinkl.channel 属性来设置）。1 由于此处的 channel类型是 file channel，因此 channel 中的事件可以持久存储在磁盘上。整 个系统如图 14-1所示。

Local

filesystem



/tmp/

spooldir



Flume agent



spooldir

source



logger

sink



i,.



messages



source!



agentl

图 14-1.具有通过 file channel 连接的 spooling directory source 和 logger sink 的 Flume 代理

在运行本示例之前，我们首先需要在本地文件系统上创建一个缓冲目录：

% mkdir /tmp/spooldir

①注意，source的属性是 channels（复数），但 sink 的属性是 channel（单数）。这是因为一个 source可以向一个以上的 channel 输送数据（参见 14.5节），而一个 sink 只能吸纳来自一个 channel的数据。另外，一个 channel 也有可能向多个 sink 输送数据，详情可以参见 14.7节。

然后，使用 Flume-ng命令启动 Flume 代理:

% flume-ng agent \

--conf-file spool-to-logger.properties \ --name agentl \

--conf $FLUME一 HOME/conf \

-Dflume.root.logger=INFO,console

conf-file用于指定 Flume 的属性文件（即范例 14-1所示的属性文件），另外还

要通过--name来传递代理的名称（由于一个 Flume 属性文件可以定义多个代理， 因此我们必须指明运行的是哪一个代理）。通过--conf告诉 Flume 在哪里可以找

到它的通用配置，例如环境设置。

在一台新终端上，我们在缓冲目录中新建一个文件。Spooling directory source不允 许文件被编辑改动，因此为了防止写了一半的文件被 source 读取，应当先把全部 内容写到一个隐藏文件中，然后再执行文件重命名命令，从而使得 source 能够读 取到完整的文件：

% echo "Hello Flume" > /tmp/spooldir/.filel.txt % mv /tmp/spooldir/.filel.txt /tmp/spooldir/filel•txt

回到代理终端，我们看到 Flume 已经检测到该文件并对其进行了处理:

Preparing to move file /tmp/spooldir/filel.txt to /tmp/spooldir/filel.txt•COMPLETED

Event: { headers:{} body: 48 65 6C 6C 6F 20 46 6C 75 6D 65 Hello Flume }

Spooling directory source导入文件的方式是把文件按行拆分，并为每行创建一个 Flume事件。事件由一个可选的 header 和一个二进制的 body 组成，其中 body 是 UTF-8编码的文本行。Logger sink使用十六进制和字符串两种形式来记录 body。 由于该缓冲目录中的文件仅包含一行内容，因此被记录的事件也只有一个。我们 还看到该文件被 source 重命名为 filel.txt .COMPLETED，这表明 Flume 已经完 成文件的处理，并且对它不会再有任何动作。

##### 14.3事务和可靠性

Flume使用两个独立的事务分别负责从 source 到 channel 以及从 channel 到 sink 的

①对于连续追加记录的日志文件，可以使用定期滚动方式，并将旧文件转到缓冲目录下，供 Flume 读取。

事件传递。在上一节的例子中，spooling directory source为文件的每一行创建一个 事件。一旦事务中的所有事件全部传递到 channel 且提交成功，那么 source 就将 该文件标记为完成。

事务以类似的方式应用于从 channel 到 sink 的事件传递过程。如果由于某种原因 使得事件无法记录，那么事务将会回滚，而所有的事件仍然保留在 channel 中，等 待重新传递。

本例使用的 channel 是,file channel，这种类型的 channel 具有持久性：只要事件被 写人 channel，即使代理重新启动，事件也不会丢失。（Flume还提供有 memory channel，由于它的事件缓存在存储器中，因此它不具有持久存储能力。采用 memory channel时，如果代理重新启动，事件就会丢失。在某些时候这种情况是 可以接受的，具体取决于应用。与 file channel相比，memory channel的优势在于 具有较高的吞吐量。） 总的来说，source产生的每个事件都会到达 sink。这里需要说明的是，每个事件 到达 sink 至少一次（at least once），也就是说，同一事件有可能会重复到达。不论 source还是 sink 都有可能产生重复。例如，即使代理重启之前有部分或全部事件 已经被提交到 channel，但是在代理重启之后，spooling directory source还是会为 所有未完成的文件重新传递事件。代理重新启动后，logger sink也会重新记录那 些已经记录但未提交的事件（如果代理在这两个操作之间关闭）。

“At-least-once”看起来好像是一个缺陷，而实际上它是一个可以接受的性能上的 权衡。“exactly once”在含义上更加严格，但它需要使用一种两阶段的提交协议 （two-phase commit protocol），代价很高。这两种不同的选择所体现的正是作为高容 量并行事件采集系统的 Flume（采用 at-least-once方式）与较为传统的企业信息系统 （采用 exactly-once方式）之间的区别。采用 at-least-once方式时，重复的事件可留 待后续的处理流水线来移除。通常，这需要以特定于应用程序的重复数据删除作 业的形式写在 MapReduce 或 Hive 之中。

批量处理

为了提高效率，Flume在有可能的情况下尽量以事务为单位来批量处理事件，而 不是逐个事件地进行处理。批量处理方式尤其有利于提高 file channel的性能，因 为每个事务只需要写一次本地磁盘和调用一次 fsync。

批量的大小取决于组件的类型，并且在大多数情况下是可配置的。例如，spooling

directory source以 100 行文本作为一个批次来读取（可通过 BatchSize 属性没 置）。同样，在通过 Avro RPC发送之前，Avro sink（参见 14.6节）试图从 channel 中 批量地读取 100 个事件，当然，如果可用的事件不足 100 个也不会引起阻塞。

##### 14.4 HDFS Sink

Flume关注的重点是向 Hadoop 数据存储器传递海量数据，因此，接下来让我们看 看应当如何配置 Flume 代理以向 HDFS sink传递事件。范例 M-2中的配置将上一 个例子中的 sink 更改为 HDFS sink，其中必不可缺的属性设置只有两个：sink的 类型（即 hdfs）和 hdfs.patho hdfs.path指定文件存放的位置（如果路径中没有 指定文件系统，那么像往常一样使用由 Hadoop 的^.clefaultFS属性所指定的文 件系统，正如本例所示）。另外，我们还指定了一个有意义的文件前缀和后缀，并 指示 Flume 将事件以文本格式写入文件。

范例 14-2.使用 spooling directory source 和 HDFS sink 的 Flume 配置

agentl.sources = sourcel

agentl.sinks = sinkl

agentl.channels = channell

agentl.sources.sourcel.channels = channell

agentl.sinks.sinkl.channel = channell

agentl.sources.sourcel.type = spooldir

agentl.sources.sourcel.spoolDir = /tmp/spooldir

agentl.sinks.sinkl.type = hdfs

agentl.sinks.sinkl.hdfs.path = /tmp/flume

agentl.sinks.sinkl.hdfs.filePrefix = events

agentl.sinks.sinkl.hdfs.fileSuffix = .log

agentl. sinks. sinkl. hdfs. inllsePref ix = _

agentl.sinks.sinkl.hdfs.fileType = DataStream

agentl.channels.channell.type = file

重新启动代理以应用 spool-to-hdfs.properties的配置，并在文件缓冲目录中创建一 个新文件：

% echo -e "Hello\nAgain" > /tmp/spooldir/.file2•txt % mv /tmp/spooldir/.file2.txt /tmp/spooldir/file2.txt

这一次，事件被传递给 hdfs sink并写到一个文件。对于正在进行写操作处理的 文件，其文件名会添加一个后缀“.tmp”，以表明文件处理尚未完成。在本例 中，hdfs.inUsePrefix属性被设置为下划线（默认值为空），此举将导致正在进行 写操作处理的文件的文件名还要添加一个_（下划线）作为前缀。这样做是因为 MapReduce会忽略以下划线为前缀的文件。因此，一个典型的临时文件的文件名 为 “ events. 1399295780136.log.tmp”，其中的数字是由 HDFS sink 生成的时 间戳。

在超过给定的打开时间（默认值为 30 秒，由 hdfs.rolllnterval属性设置）或者 达到给定的文件大小（默认值为 1024 个字节，由 hdfs.rollSize属性设置），再 或者写满了给定数量的事件（默认值为 10，由 hdfs.rollCount属性设置）之前， HDFS sink保持文件的打开状态。如果以上三个条件中有任意一条得到满足，则关 闭文件，并且体现其正在使用中的文件名的前缀和后缀都被移除。此后，新的事 件将被写入一个新的文件（新文件的文件名将具有体现其正在使用中的前缀和后 缀，直至该轮处理结束）。

在等待 30 秒后，我们可以肯定文件的处理已经结束，现在可以查看它的内容：

% hadoop fs -cat /tmp/flume/events.1399295780136.log

Hello

Again

HDFS sink以运行 Flume 代理的用户的身份来写文件，除非设置了 hdfs.proxyUser属性，那么 HDFS sink将以指定用户的身份来写文件。

14.4.1分区和拦截器

大型数据集常常被组织为分区（partition），这样做的好处是，如果查询仅涉及到数 据的某个子集，查询过程就可以被限制在特定的分区范围内。Flume事件的数据 通常按时间来分区，因此，我们可以定期运行某个进程来对已完成的分区进行各 种数据转换处理（例如，删除重复的事件）。

我们可以很容易地将示例中的数据存储方式更改为分区存储方式，只需要对 hdfs.path属性进行设置，使之具有使用时间格式转义序列的子目录：

agentl.sinks.sinkl.hdfs.path



/tmp/flume/year=%Y/month=%m/day=%d

此处，我们选择天作为分区粒度，不过也可以使用其他级别的分区粒度，结果将 导致不同的文件目录布局方案。（如果使用 Hive，可参考 17.6.2节有关 Hive 如何 在磁盘上布局分区的介绍。）在 Flume 用户指南（<http://flume>, apache, org/ FI訓 eUserGuide.html）中，与 HDFS sink相关的文件提供了格式转义序列的完整

列表。

一个 Flume 事件将被写入哪个分区是由事件的 header 中的 timestamp（时间戳）决 定的。在默认情况下，事件 header 中并没有 timestamp，但是它可以通过 Flume 拦截器（interceptor）来添加。拦截器是一种能够对事件流中的事件进行修改或删除 的组件，它们连接 source 并在事件被传递到 channel 之前对事件进行处理。°下面 的两行配置用于为 sourcel 增加一个时间戳拦截器，它将为 source 产生的每个事 件添力卩一个 timestamp header：

agentl.sources.sourcel.interceptors = interceptorl agentl.sources.sourcel.interceptors.interceptorl.type

timestamp



时间戳拦截器可以确保这些时间戳能够基本如实地反映事件创建的时间。对某些 应用程序而言，在事件写入 HDFS 时使用一个时间戳已经足够了，但是必须要注 意，如果存在多层 Flume 代理，那么事件的创建时间和写入时间之间可能存在明 显差异，尤其是当代理出现停机的情况时（参见 14.6节）。针对这种情况，我们可 以对 HDFS sink的 hdfs.useLocalTimeStamp属性进行设置，以便使用由运行 HDFS sink的 Flume 代理所产生的时间戳。

14.4.2文件格式

一般而言，使用二进制格式来存储数据是一个好主意，因为它生成的文件比使用 文本格式的文件更小。HDFS sink使用的文件格式由 hdfs.fileType属性及其他 一些属性的组合控制。

默认的 Hdfs.fileType文件格式为 SequenceFile，也就是把事件写入一个顺序 文件。在这个顺序文件中，键的数据类型为 LongWritable，它包含的是事件的时 间戳（如果事件的 header 中没有时间戳，就使用当前时间）。值的数据类型为 BytesWritable，其中包含的是事件的 body。如果 hdfs.writeFormat被设置为 Text，那么顺序文件中的值就变成 Text 数据类型，而非 BytesWritable 类型。

针对 Avro 文件所使用的配置略有不同，它的 Hdfs.fileType属性被设置为 DataStream，就像纯文本一样。此外，serializer（注意，没有前缀 hdfs.）必须 设置为 avro_event。如果想要启用压缩，则需要设置 serializer.compressionCodec属 性。下面这个例子的 HDFS sink配置为将事件写到一个 Snappy 压缩的 Avro 文

①表 14-1介绍了 Flume提供的拦栽器。

件中：

agent1.sinks.sinkl.type = hdfs

agentl.sinks.sinkl.hdfs.path = /tmp/flume

agentl.sinks.sinkl.hdfs.filePrefix = events

agentl.sinks.sinkl.hdfs.fileSuffix = .avro

agentl.sinks.sinkl.hdfs.fileType = DataStream

agentl•sinks•sinkl*serializer = avro一 event

agentl•sinks•sinkl•serializer.compressionCodec = snappy

每个事件用一个两字段的 Avro 记录来表示，这两个字段分别为 header 和 body, 其中 header 对应于 Avro 映射（字符串值），body则对应于 Avro 字节字段。

若想使用定制的 Avro 模式，可以有多种选项。如果需要把一个 Avro 内存中的对 象发送到 Flume，那么以使用 Log4jAppender 为宜。它允许通用 log4j 的 Logger

来记录 Avro 的通用映射、特殊映射或自反映射对象，并将其发送给 Flume 代理运 行的 Avro source（参见 14.6节）。在这种情况下，HDFS sink的 serializer 属性应 当设置为 org.apache.flume.sink.hdfs.AvroEventSerializer$Builder，并且 其 Avro 模式在 header 中设置（参见相关类的文档）。

另一方面，如果事件最初不是从 Avro 对象导出的，那么可以写一个定制的 serializer，以便将 Flume 事件转换为具有定制模式的 Avro 对象。为此， org.apache.flume.serialization 包的 AbstractAvroEventSerializer 类可以 作为一个很好的起点。

##### 14.5扇出

术语扇出（fan out）指的是从一个 source 向多个 channel，亦即向多个 sink 传递事 件。例如，范例 M-3中的配置就是将事件同时传递到一个 HDFS sink（经由 channella 到达 sinkla）和一个 logger sink（经由 channellb 到达 sinklb）0

范例 14-3•用 spooling directory source 且扇出到 HDFS sink 和 logger sink 的 Flume 配置

agentl.sources = sourcel

agentl.sinks = sinkla sinklb

agentl，channels = channella channellb

agentl.sources.sourcel.channels = channella channellb agentl.sinks.sinkla.channel = channella agentl.sinks.sinklb.channel = channellb

agentl.sources.sourcel.type = spooldir

agentl.sources.sourcel.spoolDir = /tmp/spooldir

agentl.sinks.sinkla.type = hdfs

agentl.sinks•sinkla.hdfs.path = /tmp/flume

agentl.sinks.sinkla.hdfs.filePrefix = events

agentl.sinks.sinkla.hdfs.fileSuffix = .log

agentl.sinks.sinkla.hdfs.fileType = DataStream

agentl.sinks.sinklb.type = logger

agentl.channels.channella.type = file

agentl.channels.channellb.type = memory

本例的主要变化在于将 agentl.sources.sourcel.channels属性设置为一个由 channella和 channellb 组成的空格分隔列表，使得该 source 被配置为向多个 channel传递事件。在本例中，连接 logger sink的 channel(即 channellb)是一个 memory channel，因为我们记录事件的目的只是为了调试，并不介意代理重启时事 件的丢失。此外，与前面的例子相同，每个 channel 通过配置连接到一个 sink。图 14-2描绘了完整的流程。

Local

filesystem

O.j

channella

spooldir

source

file channel

memor

agentl



HDFS



![img](Hadoop43010757_2cdb48_2d8748-173.jpg)



14-2•有 spooling directory source 并扇出到一个 HDFS sink 和一个 logger sink 的 Flume agent

###### 14.5.1交付保证

Flume使用独立的事务来负责从 spooling directory source到每一个 channel 的每批 事件的传递。在本例中，有一、个事务负责从 source 到连接 HDFS sink的 channel 的 事件交付，另一个事务负责从 source 到连接 logger sink的 channel 的同一批事件 的交付。若其中有任何一个事务失败（例如 channel 满溢），这些事件都不会从 source中删除，而是等待稍后重试。

在本例中，由于我们并不介意是否有一些事件未能交付给 logger sink，因此可以 指定该 channel 为 optional（可选的）channel0在这种情况下，如果与该 channel 相关联的事务失败，source并不会保留事件以待再次尝试。（请注意，如果在两个

channel的事务都提交之前代理出现故障，那么在代理重新启动后所有受影响的事 件都会重新传递，即使未提交的 channel 被标记为 optional 也是这样)。为此，需 要把 source 的属性 selector.optional设置为一个空格分隔的 channel 的列表：

agent1.sources.sourcel.selector.optional = channellb

近实时索引

对事件进行索引以便于搜索是扇出技术在实践应用中的一个很好的例子。来自 source的事件既被发送到 HDFS sink(这是事件的主要信息库，因此使用 required channel)，同时也被发送到 Solr(或 Elasticsearch) sink，以建立搜索索引 (使用 optional channel)。

MorphlineSolrSink从 Flume 事件中提取字段，并把它们转换为 Solr 文件(通过 使用 Morphline 配置文件)，然后再将其装载到一个活跃的 Solr 搜索服务器中。

由于导入的数据在很短的时间内就可以呈现在搜索结果中，所以这个过程被称 为近实时(near real time)过程。

###### 14.5.2复制和复用选择器

正常的扇出流是向所有 channel 复制事件，但有些时候，人们希望有更多的行为方 式可以选择，从而使某些事件被发送到这一个 channel，而另一些事件被发送到另 一个 channel。这可以通过在 source 上设置一个复用选择器(multiplexing selector)来 实现，此时我们可以定义路由规则，使得事件 header 中的特定值与某个 channel 相匹配。详细的配置信息请参考 Flume 用户指南。

##### 14.6通过代理层分发

如何对一组 Flume 代理进行扩展？如果每个产生原始数据的节点上只运行一个代 理，那么在目前为止我们所描述的各种配置情况下，不管任何时间，每一个写入 HDFS的文件的内容都完全由来自同一节点的事件组成。要是能够把来自一组节 点的事件汇聚到一个文件中，效果可能会更好，因为这样可以减少文件的数量， 增加文件的大小(以减轻同时施加在 HDFS 身上的压力，并提高 MapReduce 的处理 效率，参见 8.2.1节)。另外，由于向文件输入数据的节点变多，因此文件可以更 快地推陈出新，从而使得这些文件可用于分析的时间更接近于事件的创建时间， 这在某些情况下是有必要的。

要想实现 Flume 事件的汇聚，就需要使用分层结构的 Flume 代理。第一层代理负 责采集来自原始 source（如 Web 服务器）的事件，并将它们发送到第二层。第二层 代理的数量比第一层少，这些代理先汇总来自第一层代理的事件，再把这些事件

写入 HDFS（参见 代理。



14-3）。如果 source 节点的数量庞大，那么最好使用多层

local

source

tier2

agent

local

source

local

source

HDFS

local

source

tier2

agent



图 14-3.通过第二层代理汇聚来自第一层的 Flume 事件

要构建分层的结构，需要使用某种特殊的 sink 来通过网络发送事件，再加上相应 的 source 来接收这些事件。Avro sink可通过 Avro RPC将事件发送给运行在另一 个 Flume 代理上的其他的 Avro。另外也可以使用 Thrift sink，它通过 Thrift RPC 做同样的事情，并搭配了一个 Thrift source。®

![img](Hadoop43010757_2cdb48_2d8748-175.jpg)



不要被命名所迷惑：Avro的 sink 和 source 不提供写入或读取 Avro 文件的能 力，它们仅用于代理层的事件分发，并且为了做到这一点，它们需要通过 Avro RPC进行通信（并因此而得名）。若想将事件写入 Avro 文件，应当使用 HDFS sink，如 14.4.2节所述。

范例 14-4描绘了一个两层 Flume 代理的配置。这个配置文件定义了两个代理， agentl和 agent2。运行在第一层的代理称为 agentl，它具有 spooldir source和

①Avro的 sink-source对比 Thrift 的 sink-source对出现得更早，在写本书的时候，Thrift的 sink-source对还无法提供某些功能，例如加密。

Avro sink，并通过 file channel连接。运行在第二层的代理是 agent2，它具有 Avro source，用于监听从 agentl 的 Avro sink发送过来的事件所抵达的端口。 Agent2的 sink 所使用的配置与范例 14-2中的 HDFS sink相同。

请注意，由于在同一台机器上运行了两个 file channel，因此必须通过配置使得它 们分别指向不同的数据以及检查点目录（默认情况下都在用户的主目录中）。只有这 样做，两个 channel 的文件才不会彼此重叠。

范例 14-3.使用 spooling directory source 和 HDFS sink 的两层 Flume 代理的配置

林 First-tier agent

agentl.sources = sourcel

agentl.sinks = sinkl

agentl.channels = channell

agentl.sources.sourcel.channels = channell

agentl.sinks.sinkl.channel = channell

agentl.sources.sourcel.type = spooldir

agentl.sources.sourcel.spoolDir = /tmp/spooldir

agentl•sinks•sinkl•type = avro

agentl.sinks.sinkl.hostname = localhost

agentl.sinks.sinkl.port s 10000

agentl.channels.channell.type = file

agentl.channels.channell.checkpointDir=/tmp/agentl/file-channel/checkpoint agentl.channels.channell.dataDirs=/tmp/agentl/file-channel/data

林 Second-tier agent

agent2.sources = source2 agent2.sinks = sink2 agent2.channels = channel2 agent2.sources.source2.channels = channel2 agent2>sinks.sink2.channel = channel2

agent2.sources.source2.type = avro

agent2.sources•source2•bind = localhost agent2.sources.source2.port = 10000

agent2.sinks.sink2.type = hdfs

agent2.sinks.sink2.hdfs.path = /tmp/flume agent2.sinks.sink2.hdfs.filePrefix = events agent2.sinks.sink2.hdfs.fileSuffix = .log agent2.sinks.sink2.hdfs.fileType = DataStream

agent2.channels.channel2.type = file

agent2.channels.channel2.checkpointDir=/tmp/agent2/file-channel/checkpoint

该系统如图 14-4所示。

Local

filesystem



agentl



agent2



HDFS



图 14-4.由 Avro sink-source对连接的一个两层 Flume 代理

这两个代理需要分别运行，它们用的--conf-file 同，分别为

% flume-ng agent --conf-file spool-to-hdfs-tiered.properties -



参数相同，



-name agentl



但--name参数不



以及

% flume-ng agent --conf-file spool-to-hdfs-tiered.properties --name agent2 . . •

交付保证

Flume利用事务来确保每一批事件都能可靠地从 source 传递至 channel，以及从 channel传递至 sink。在 Avro sink-source连接的背景下，事务可用于确保将事件可 靠地从一个代理传递到下一个代理。

在 agentl 中，Avro sink从 file channel读取一批事件的操作被封装在一个事务 中。只有当 Avro sink收到确认，表示它到 agent2 的 Avro source RPC端点的写操 作成功时该事务才会提交，而只有当 agent2 将事件批量写入其 file channel操作 的事务被成功提交后才会发送该确认。因此，Avro的 sink-source对可以保证事件

从一个 Flume 代理的 channel 成功传递到另一个 Flume 代理的 channel（至少一

次）0 假如其中任何一个代理停止运行，显然这些事件将无法顺利传递到 HDFS。例 如，若是 agentl 停止运行，那么文件将会积压在缓冲目录中，以等待 agentl 重 新启动并处理。此外，在代理停止运行的那一刻，它自己的 file channel中的所有 事件在代理重新启动后仍然可用，因为 file channel可以提供持久存储保证。

如果 agent2 停止运行，事件将被保留在 agentl 的 file channel中，直至 agent2 重新启动。但是请注意，channel的容量必然是有限的，如果 agentl 的 channel 已 填满而 agent2 还没有恢复运行，那么任何新事件都将丢失。在默认情况下，file channel能够恢复的事件数量不超过一百万条（可以通过 capacity 属性进行设置），

另外，当检査点目录中的可用磁盘空间低于 500 MB时，也将停止接受事件（由 minimumRequiredSpace 属性控制）。

上述两种情况都假没代理最终能够恢复运行，但事实并非总是如此（例如正在运行

♦

的硬件出现了故障）。如果 agentl 无法恢复运行，损失仅限于其 file channel中保 存的那些在 agentl 停机前尚未来得及交付给 agent2 的事件。就此处所描述的架 构而言，由于第一层有多个类似于 agentl 的代理，因此，可以利用这一层中的其 他节点来接管故障节点。例如，如果该节点运行了负载均衡的 Web 服务器，那么 其他节点将会消化掉故障 Web 服务器上的通信量，并且它们将生成新的 Flume 事 件传递到 agent2。因此，没有新的事件会丢失。

但是，如果 agent2 无法恢复运行，带来的后果要严重得多。对上游的第一层代理 （以 agentl 为例）而言，channel中的所有事件都将丢失，并且产生的新事件也无法 传递。这个问题的解决方案是让 agentl 具有多个冗余的 Avro sink，并把这些 sink 安排在同一个 sink 组中。因此，如果目标 agent2 的 Avro 端点不可用，agentl还 可以尝试使用同一个 sink 组中的其他 sink。我们将在下一小节讨论 sink 组。

##### 14.7 Sink 组

sink组允许将多个 sink 当作一个 sink 来处理，以实现故障转移或负载均衡（参见图 14-5）。若某个第二层代理不可用，事件将被传递给另一个第二层代理，从而使这 些事件不中断地到达 HDFS。

![img](Hadoop43010757_2cdb48_2d8748-178.jpg)



load balance or failover

source

local

source

local

source

source

tied

tier2

agent

tier2

agent

HDFS

tier!

agent

agent

agent

agent



14-5.为了负载均衡或故障转移而使用多个 sink

要想配置 sink 组，首先要把代理的 sinkgroups 属性设置为该 sink 组的名称，然 后为 sink 组列出组中的所有 sink 以及 sink 处理器的类型。Sink处理器的类型用于 设置 sink 的选择策略。范例 14-5给出的是在两个 Avro 端点之间实现负载均衡的 配置。

范例 14-5.利用 sink 组在两个 Avro 端点之间实现负载均衡的 Flume 配置

毒

agentl.sources = sourcel

agentl.sinks = sinkla sinklb

agentl.sinkgroups = sinkgroupl

agentl.channels = channell

agentl.sources.sourcel.channels = channell

agentl•sinks•sinkla.channel = channell

agentl.sinks.sinklb.channel = channell

agentl.sinkgroups.sinkgroupl.sinks = sinkla sinklb

agentl.sinkgroups.sinkgroupl.processor.type = load_balance

agentl.sinkgroups.sinkgroupl.processor.backoff = true agentl.sources.sourcel.type = spooldir

agentl.sources.sourcel.spoolDir = /tmp/spooldir agentl.sinks.sinkla.type = avro

agentl•sinks•sinkla.hostname = localhost

agentl.sinks.sinkla.port = 10000

agentl.sinks.sinklb.type = avro agentl.sinks.sinklb.hostname = localhost

agentl<sinks.sinklb.port = 10001 agent1.channels.channell.type = file

这个例子定义了两个 Avro sink： sinkla和 sinklb，它们之间的区别仅在干所连 接的 Avro 端点不同（由于我们是在本地主机上运行所有实例，所以这个区别体现 在端口上，对分布式结构来说，是两个主机不同而端口相同）。我们还定义了 sinkgroupl，并设置 sinkla 和 sinklb 作为它的 sink 组成员。

处理器类型设置为 load_balance，它试图使用循环选择机制在组中的两个 sink 成 员之间分发事件流（你可以通过 processor.selector属性来改变此设置）。如果某 个 sink 不可用，那么就尝试下一个 sink，如果两个 sink 都不可用，事件也不会从 channel中移除，就像在单个 sink 的情况下一样。默认情况下，sink处理器不会记 住 sink 的不可用性，所以每一批被传递的事件都会重试故障的 sink。这样做有可 能使效率变低，所以我们可以通过没置 processor.backoff属性来改变这种行 为，让故障 sink 在指数增加超时周期内被列入黑名单（最长周期可达 30 秒，由 processor.selector.maxTimeOut 属性控制）。

【    还有另一种类型的处理器：failover。这种处理器不是在 sink 之间进行事件负载

■k 均衡处理，而是在优选 sink 可用的情况下一直使用优选 sink，而当优选 sink 故 jjlk障时则切换到另一个 sink。Failover类型的处理器需要为 sink 组维护其 sink 成

员的优先顺序，并按照优先顺序来尝试传递。如果具有最高优先级的 sink 不可 用，那么就尝试下一个最高优先级的 sink，以此类推。故障的 sink 在不断增加 的超时周期内被列入黑名单（最长周期可达 30 秒，由 processor.maxpenalty 属性控制）。

范例 M-6给出了作为第二层代理的 agent2a 的配置。

范例 14-6.在负载均衡的背景下的一个第二层代理的 Flume 配置

agent2a.sources = source2a

agent2a>sinks = sink2a

agent2a>channels = channel2a

agent2a.sources.source2a.channels = channel2a agent2a.sinks.sink2a.channel = channel2a

agent2a.sources.source2a.type = avro

agent2a.sources.source2a.bind = localhost agent2a.sources.source2a.port = 10000

agent2a•sinks.sink2a.type = hdfs

agent2a.sinks.sink2a.hdfs.path = /tmp/flume agent2a.sinks.sink2a.hdfs.filePrefix = events-a

agent2a.sinks.sink2a.hdfs.fileSuffix = .log

agent2a.sinks.sink2a.hdfs.fileType = DataStream

agent2a.channels.channel2a.type = file

Agents的配置基本相同，除了 Avro source端口（因为我们是在本地主机上运行这 个例子）以及 HDFS sink创建的文件名的前缀不同之外。文件名前缀用于确保多个 第二层代理同时创建的 HDFS 文件不会出现重名。

通常，Flume代理运行在不同的机器上，在这种情况下，可以利用主机名来让文 件名唯一，只需要配置一个主机拦截器（参见表 14-1），并在文件路径中包含转义 字符序列％^051；}:

agent2.sinks.sink2.hdfs.filePrefix = events-%{host}

图 14-6给出了整个系统的示意图。

Avro sink

sinkla

spooldir

source

Avro sink

channel!    1」抓 113

掛 a

Flume agent

agentl



Local

filesystem



HDFS

Avro

source

HDFS

sink

Flume轉細

Avro

source

HDFS

sink

丄

卿■■卿■卿 w

Flume agent

source2a Z2°    sink2a

agent2a

曬

/Imp/ flume

agent2b





14-6.在两个代理之间实现负载均衡

##### 14.8 Flume与应用程序的集成

既然 Avro source用来接收 Flume 事件的一个 RPC 端点，那么就有可能编写 RPC

客户端程序将事件发送至该端点，并且这个客户端可以嵌入到任何一个希望向 Flume导入事件的应用程序中。

Flume SDK模块提供的 Java RpcClient类被用来发送 Event 对象到一个 Avro 端 点(即运行在 Flume 代理上的一个 Avro source，通常这个 Flume 代理位于另一

层)。通过配置，客户端程序可以在端点之间实现故障转移或负载均衡，并且也能 支持 Thrift 端点(Thrift sources)。

Flume嵌入式代理(embedded agent)也提供了类似功能，它是一个运行在 Java 应用 程序中的精简版的 Flume 代理。嵌入式代理有一个特殊的 source，应用程序通过 调用 EmbeddedAgent 对象中的方法将 Flume 事件对象发送到该 source。它唯一支 持的 sink 是 Avro sink，不过，我们可以通过配置使用多个 sink 来实现故障转移或 负载均衡。

有关 Flume SDK和嵌人式代理的更详细的描述，可以参考 Flume 开发者指南，网

址为 <http://flume.apache.org/FlumeDeveloperGuide.html0>

##### 14.9组件编目

我们在本章只使用了少量的 Flume 组件。Flume组件很多，表 14-1对它们进行了

简单介绍。关于这些组件的配置及使用的更多信息，请参考 Flume 用户指南，网 址为 <http://flume.apache.org/FlumeUserGuide.htmlc>

表 14-1. Flume 组件

类别    组件    描述

Source    Avro    监听由 Avro sink 或 Flume SDK 通过 Avro RPC 发送的事

_件所抵达的端口

Exec    运行一个 Unix 命令(例如 tail -F/path/to/file)，并把从标准

输出上读取的行转换为事件。请注意，此 source 不能保证 事件被传递到 channel，更好的选择可以参考 spooling directory source 或 Flurne SDK

HTTP    监听一个端口，并使用可插拔句柄(例如，JSON处理程序

或二进制数据处理程序)把 HTTP 请求转为事件

| 类别 Source  | 组件 DMS           | 描述读取来自 JMS Queue或 Topic 的消息并将其转为事件             |
| ----------- | ----------------- | ------------------------------------------------------------ |
|             | Netcat            | 监听一个端口，并把每行文本转换为一个事件                     |
|             | Sequencegenerator | 依据增量计数器来生成事件。对测试有用                         |
|             | Spoolingdirectory | 按行来读取保存在文件缓冲目录中的文件，并将其转换为 事件      |
|             | Syslog            | 从日志中读取行，并将其转换为事件                             |
|             | Thrift            | 监听由 Thrift sink或 Flume SDK通过 Thrift RPC发送的事 件所抵达的端口 |
|             | Twitter           | 连接 Twitter 的 streaming API(firehose 的 1%)，并将 tweet 转为事件 |
| Sink        | Avro              | 通过 Avro RPC发送事件到一个 Avro source                        |
|             | Elasticsearch     | 使用 Logstash 格式将事件写到 Elasticsearch 集群                  |
|             | File roll         | 将事件写到本地文件系统                                       |
|             | HBase             | 使用某种序列化工具将事件写到 HBase                            |
|             | HDFS              | 以文本、序列文件、Avro或定制格式将事件写到 HDFS               |
|             | IRC               | 将事件发送给 IRC 通道                                          |
|             | Logger            | 使用 SLF4J 记录 INFO 级别的事件。对测试有用                      |
|             | Morphline (Solr)  | 通过进程内的 Morphline 命令链来运行事件。通常用于 将数据加载到 Solr |
|             | Null              | 丢弃所有事件                                                 |
|             | Thrift            | 通过 Thrift RPC发送事件到 Thrift source                        |
| Channel     | File              | 将事件存储在一个本地文件系统上的事务日志中                   |
|             | JDBC              | 将事件存储在数据库中（嵌人式 Derby）                          |
|             | Memory            | 将事件存储在一个内存队列中                                   |
| Interceptor | Host              | 在所有事件上设置一个包含代理主机名或 IP 地址的主机 header      |
|             | Morphline         | 通过一个 Morphline 配置文件来过滤事件。用于有条件地 丢弃事件或者基于模式匹配地添加 header 或内容提取 |
|             | Regex extractor   | 使用指定的正则表达式来设置从事件 body 中以文本形式 提取的 header |
|             | Regex filtering   | 通过将事件 body 以文本形式与指定的正则表达式进行匹 配来决定包括或是不包括事件 |
|             | Static            | 在所有事件上设置一个固定的 header 及其值                       |

| 类别 Interceptor | 组件 Timestamp | 描述设置一个时间戳 header，其中包含的是代理处理事件的时 间，以毫秒为单位 |
| --------------- | ------------- | ------------------------------------------------------------ |
|                 | UUID          | 在所有事件上设置一个 ID header，它所包含的是一个全             |

局唯一标识符。对将来删除重复数据有用

关于 Flume 397



14.10延伸阅读
本章简单概述了 Flume。更多细节可参考 O’Reilly 2014年出版的 Using Flume (http://shop.oreilly.com/product/0636920030348.do)，作者 Hari Shreedharan。另外， 0,Reilly 2014年出版的 Hadoop Application Architectures也有很多关于设计导入管 道(以及构建普通 Hadoop 应用程序)的实用信息，作者为 Mark Grover、Ted Malaska、Jonathan Seidman 和 Gwen Shapira，网址为 http:"shop.oreilly.com/product/ 0636920033196.do。


