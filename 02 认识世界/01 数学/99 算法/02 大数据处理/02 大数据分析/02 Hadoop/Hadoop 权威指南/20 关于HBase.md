---
title: 20 关于HBase
toc: true
date: 2018-06-27 07:51:30
---
（作者：Jonathan Gray 和 Michael Stack）



##### 20.1 HBase 基础

HBase是一个在 HDFS 上开发的面向列的分布式数据库。如果需要实时地随机访 问超大规模数据集，就可以使用 HBase 这一 Hadoop应用。

虽然数据库存储和检索的实现可以选择很多不同的策略，但是绝大多数解决办 法，特别是关系型数据库技术的变种，不是为大规模可伸缩的分布式处理设计 的。很多厂商提供了复制(replication)和分区(partitioning)解决方案，让数据库能够 从单个节点上扩展出去，但是这些附加的技术大都属于“事后”的解决办法，而 且非常难以安装和维护。并且这些解决办法常常要牺牲一些重要的 RDBMS 特 性。在一个“扩展的” RDBMS上，连接、复杂查询、触发器、视图以及外键约束 这些功能要么运行开销大，要么根本无法用。

HBase从另一个方向来解决可伸缩性的问题。它自底向上地进行构建，能够简单 地通过增加节点来达到线性扩展。HBase并不是关系型数据库，它不支持 SQL。® 但在特定的问题空间里，它能够做 RDBMS 不能做的事：在廉价硬件构成的集群 上管理超大规模的稀疏表。

HBase的一个典型应用是 web table, 一个以网页 URL 为主键的表，其中包含爬取

①不过，可以了解一下 17.4.3节中提到的 Apache Phoenix项目以及名为 Trafodion 的基于 HBase 的荠务 SQL 奴据库［https://wiki.trafodion.orgf)o

的页面和页面的属性(例如语言和 MIME 类型)。webtable非常大，行数可以达十亿 级(billion)之级。在 webtable 上连续运行用于批处理分析和解析的 MapReduce 作 业，能够获取相关的统计信息，增加验证的 MIME 类型列以及供搜索引擎进行索 引的解析后的文本内容。同时，表格还会被以不同运行速度的“爬取器” (crawler) 随机访问并随机更新其中的行。在用户点击访问网站的缓存页面时，需要实时地 将这些被随机访问的页面提供给他们。

###### 背景

HBase项目是由 Powerset 公司的 Chad Walters和 Jim Kelleman在 2006 年末发起 的。当时，它起源于在此之前 Google 刚刚发布的 Bigtable。® 2007年 2 月，Mike Cafarella提供代码，形成了 一个基本可以用的系统，然后 Jim Kellerman接手继续 推进该项目。

HBase的第一个发布版本是在 2007 年 10 月和 Hadoop 0.15.0捆绑在一起发布的。 2010年 5 月，HBase从 Hadoop 子项目升级成 Apache 顶层项目。今天，HBase已 然成为一种广泛应用于各种行业生产中的成熟技术。。

##### 20.2概念

在本节中，我们只对 HBase 的核心概念进行快速、简单的介绍。掌握这些概念至 少有助于消化后续内容。

###### 20.2.1数据模型的“旋风之旅”

应用把数据存放在带标签的表中。表由行和列组成。表格的“单元格(cell)由行 和列的坐标交叉决定，是有版本的。默认情况下，版本号是自动分配的，为 HBase插入单元格时的时间戳。单元格的内容是未解释的字节数组。例如， 图 20-1所示为用于存储照片的 HBase 表。

表中行的键也是字节数组。所以理论上，任何东西都可以通过表示成字符串或将 二进制形式转化为长整型或直接对数据结构进行序列化，来作为键值。表中的行

①引自 Fay Chang 等人的文章 “Bigtable: A Distributed Storage System for Structured Data”

（Bigtable： 一个结构化数据的分布式存储系统），发表于 2006 年 11 月，网址为 <http://research.google.com/archive/bigtable.html> Q

根据行的键值(也就是表的主键)进行排序。排序根据字节序进行。所有对表的访问 都要通过表的主键。®

Increasing row key



Column

mn family contents contents:image

000001

jpeg

娥灘送 :'5縷：’

tiff

000002

000003

51.8厂 3.1

I

__。国。    __    __    __ _一_

51.5,-0.1



Versions    Cells



![img](Hadoop43010757_2cdb48_2d8748-249.jpg)



20-1.用于描述存储照片的表的 HBase 数据模型

行中的列被分成“列族”(column family)。同一个列族的所有成员具有相同的前 缀。因此，像列/w/o./orwo/和 info:geo都是列族 k/o的成员，而则 属于 contents 族。列族的前缀必须由“可打印的”(printable)字符组成。而修饰性的 结尾字符，即列族修饰符，可以为任意字节。列族和修饰符之间始终以冒号(:) 分隔。

一个表的列族必须作为表模式定义的一部分预先给出，但是新的列族成员可以随 后按需要加入。例如，只要目标表中已经有了列族/A亦，那么客户端就可在更新时提 供新的列并存储它的值。

物理上，所有的列族成员都一起存放在文件系统中。所以，虽然我们前面把 HBase描述为一个面向列的存储器，但实际上更准确的说法是：它是一个面向列 族的存储器。由于调优和存储都是在列族这个层次上进行的，所以最好使所有列 族成员都有相同的访问模式(access pattern)和大小特征。对于存储照片的表，由

①HBase不支持表中的其他列建立索引(也称为辅助索引)。不过，有几种策略可用于支持辅助索

引提供的査询类型，每种策略在存储空间、处理负载和査询执行时间之间存在不同的利弊权 衡，关干这个问题，请参阅 HBase 参考指南，讽地为[http://hbase.apache.org/book.html](http://hbase.apache.org/book.html%e3%80%82)[。](http://hbase.apache.org/book.html%e3%80%82)

于图像数据比较大(兆字节)，因而跟较小的元数据(千字节)分别存储在不同的列

族中。

简而言之，HBase表和 RDBMS 中的表类似，只不过它的单元格有版本，行是排 序的，而只要列族预先存在，客户端随时可以把列添加到列族中去。

1.区域

HBase自动把表水平划分成区域(region)。每个区域由表中行的子集构成。每个区 域由它所属干的表、它所包含的第一行及其最后一行(不包括这行)来表示。一开 始，一个表只有一个区域。但是随着区域开始变大，等到它超出设定的大小阈 值，便会在某行的边界上把表分成两个大小基本相同的新分区。在第一次划分之 前，所有加载的数据都故在原始区域所在的那台服务器上。随着表变大，区域的 个数也会增加。区域是在 HBase 集群上分布数据的最小单位。用这种方式，一个 因为太大而无法放在单台服务器上的表会被放到服务器集群上，其中每个节点都 负责管理表所有区域的一个子集。表的加载也是使用这种方法把数据分布到各个 节点。在线的所有区域按次序排列就构成了表的所有内容。

2.加锁

无论对行进行访问的事务牵涉多少列，对行的更新都是“原子的”(atomic)。这使 得“加锁模型”(locking model)能够保持简单。

###### 20.2.2 实现

正如 HDFS 和 YARN 是由客户端、从属机(slave)和协调主控机(master)—(即 HDFS

饱 namenode 热 datanode，以及 YARN 的资源管理器和节点管理器)-组成，HBase也

采用相同的模型，它用一个 master 节点协调管理一个或多个 regionserver 从属机 (参见图 20-2)。HBase主控机(master)负责启动(bootstrap)—个全新的安装，把区域 分配给注册的 regionserver，恢复 regionserver 的故障。master的负载很轻。 regionserver负责零个或多个区域的管理以及响应客户端的读/写请求。regionserver 还负责区域的划分并通知 HBase master有了新的子区域(daughter region)，这样一来， 主控机就可以把父区域设为离线，并用子区域替换父区域。

![img](Hadoop43010757_2cdb48_2d8748-250.jpg)



Regionserver

Regionserver

Regionserver



••



••



••••



••



♦•减



''嚷顏.

图 20-2. HBase集群的成员

HBase依赖于 ZooKeeper(参见第 21 章)。默认情况下，它管理一个 ZooKeeper 实 例，作为集群的“权威机构” (authority)，尽管也可以通过配置来使用已有的 ZooKeeper集群。ZooKeeper集合体(ensemble)负责管理诸如 hbase:meta目录表

的位置以及当前集群主控机地址等重要信息。如果在区域的分配过程中有服务器 崩溃，就可以通过 ZooKeeper•来进行分配的协调。在 ZooKeeper 上管理分配事务 的状态有助于在恢复时能够从崩溃服务器遗留的状态开始继续分配。在启动一个 客户端到 HBase 集群的连接时，客户端必须至少拿到到集群所传递的 ZooKeeper 集合体的位置。这样，客户端才能访问 ZooKeeper 的层次结构，从而了解集群的 属性，例如服务器的位置。

类似于在 Hadoop 中可以通过 etc/hadoop/slaves文件查看 datanode 和节点管理器一 样，regionserver从属机节点列在 HBase 的 conf/regionservers文件中。启动和结束

服务的脚本和 Hadoop 类似，使用相同的基于 SSH 的机制来运行远程命令。集群 的站点配置(site-specific configuration)在 HBase 的 conf/hbase-site.xml 和 conf/hbase-

文件中。它们的格式和 Hadoop 父项目中对应的格式相同(参见第 10 章)。

![img](Hadoop43010757_2cdb48_2d8748-252.jpg)



对于 HBase 和 Hadoop 上相同的服务或类型，HBase实际上直接使用或继承 Hadoop的实现。在无法直接使用或继承时，HBase会尽量遵循 Hadoop 的模 型。例如，HBase使用 Hadoop Configuration系统，所以它们的配置文件格式 相同。对于作为用户的你来说，这意味着你使用 HBase 时感觉就像使用 Hado叩一样“亲切”。HBase只是在增加特殊功能时才不遵循这一规则。

HBase通过 Hadoop 文件系统 API 来持久化存储数据。多数人使用 HDFS 作为存储 来运行 HBase0 但是，在默认情况下，除非另行指明，HBase会将存储写入本地 文件系统。如果是体验一下新装 HBase，这是没有问题的，但如果稍后要使用 HBase集群，首要任务通常是把 HBase 的存储配置为指向要使用的 HDFS 集群。

运行中的 HBase

HBase内部保留名为 hbase:meta的特殊目录表(catalog table)。它们维护着当前集 群上所有区域的列表、状态和位置。hbase:/neta表中的项使用区域名作为键。区 域名由所属的表名、区域的起始行、区域的创建时间以及对其整体进行的 MD5 哈 希值(即对表名、起始行、创建的时间戳进行哈希后的结果)组成。

例如，表 TestTabLe 中起始行为 xyz 的区域的名称如下:

TestTable,xyz,1279729913622.Ib6el76fb8d8aa88fd4ab6bc80247ece.

在表名、起始行、时间戳中间用逗号分隔。MD5哈希值则使用前后两个句号包 如前所述，行的键是排序的。因此，要查找一个特定行所在的区域只要在目录表 中找到第一个键大干或等于给定行键的项即可。区域变化时，即分裂、禁用/启用 (disable/enable)、删除、为负载均衡重新部署区域或由于 regionserver 崩溃而重新 部署区域时，目录表会进行相应的更新。这样，集群上所有区域的状态信息就能保持是 最新的。

新连接到 ZooKeeper 集群上的客户端首先查找 hbase:meta的位置。然后客户端通 过查找合适的 hbase:meta区域来获取用户空间区域所在节点及其位置。接着， 客户端就可以直接和管理那个区域的 regionserver 进行交互。

每个行操作可能要访问三次远程节点。为了节省这些代价，客户端会缓存它们遍 历 hbase:meta时获取的信息。需要缓存的不仅有位置信息，还有用户空间区域 的开始行和结束行。这样，它们以后不需要访问 hbase:meta表也能得知区域存 放的位置。客户端在碰到错误之前会一直使用所缓存的项。当发生错误时，即区 域被移动了，客户端会再去查看 hbase:meta获取区域的新位置。如果

hbase:meta区域也被移动了，客户端会重新查找。

到达 Regionserver 的写操作首先追加到“提交日志” (commit log)中，然后加入内 存中的 memstore。如果 memstore 满，它的内容会被‘‘刷人”(flush)文件系统。

提交日志存放在 HDFS 中，因此即使一个 regionserver 崩溃，提交日志仍然可用。 如果发现一个 regionserver 不能访问(通常因为服务器的 znode 在 ZooKeeper 中过 期)，主控机会根据区域对死掉的 regionserver 的提交日志进行分割。重新分配 后，在打开并使用死掉的 regionserver 上的区域之前，这些区域会找到属于它们的 从被分割提交日志中得到的文件，其中包含还没有被持久化存储的更新。这些更 新会被“重做”(replay)以使区域恢复到服务器失败前的状态。

在读的时候首先查看区域的 memstore。如果在 memstore 中找到了需要的版本，查 向就结束了。否则，需要按照次序从新到旧检查“刷新文件” (flush file)，直到找 到满足查询的版本，或所有刷新文件都处理完为止。

有一个后台进程负责在刷新文件个数到达一个阈值时压缩它们。它把多个文件重 新写人一个文件。这是因为读操作检查的文件越少，它的执行效率越高。在压缩 (compaction)时，进程会清理掉超出模式所设最大值的版本以及删除单元格或标识 单元格为过期。在 regionserver 上，另外有一个独立的进程监控着刷新文件的大 小，一旦文件大小超出预先设定的最大值，便对区域进行分割。

##### 20.3安装

从一个 Apache Download Mirror ([http://www.apache.org/dyn/closer.cgi/hbase/)](http://www.apache.org/dyn/closer.cgi/hbase/)%e4%b8%ad%e6%a1%83%e9%80%89)[中桃选](http://www.apache.org/dyn/closer.cgi/hbase/)%e4%b8%ad%e6%a1%83%e9%80%89) 并下载一个 HBase 的稳定发布版本，然后在本地文件系统解压。示例如下：

% tar xzf hbase-x.y.z.tar.gz

和用 Hadoop 一样，首先需要告诉 HBase 系统中的 Java 在哪里。如果设置了 ]AVA_HOME环境变量，把它指向了正确的 Java 安装，HBase就会使用那个 Java 安 装。这样便不需要进行其他配置。否则，可以通过编辑 HBase 的 conf/hbase-env.sh，并指明]AVA_HOME变量的值(参见附录 A 的示例)，从而设置 HBase 所使 用的 Java 安装。

为了方便，把 HBase 的二进制文件目录加入命令行路径中。示例如下：

% export HBASE_HOME=^/sw/hbase-x.y.z % export PATH=$PATH:$HBASE_HOME/bin

要想获取 HBase 的选项列表，输入以下命令即可：

% hbase

Options:

--config DIR Configuration direction to use. Default: ./conf --hosts HOSTS Override the list in * regionservers1 file

Commands:

Some commands take arguments• Pass no args or -h for usage, shell Run the HBase shell hbck Run the hbase *fsck* tool hlog Write-ahead-log analyzer hfile Store file analyzer zkcli Run the ZooKeeper shell upgrade Upgrade hbase master Run an HBase HMaster node regionserver Run an HBase HRegionServer node zookeeper Run a Zookeeper server rest Run an HBase REST server thrift Run the HBase Thrift server thrift2 Run the HBase Thrift2 server clean Run the HBase clean up script classpath Dump hbase CLASSPATH

mapredcp Dump CLASSPATH entries required by mapreduce

pe Run PerformanceEvaluation

ltt Run LoadTestTool

version Print the version

CLASSNAME Run the class named CLASSNAME

###### 测试驱动

要启动一个使用本地文件系统々wp目录作为持久化存储的 HBase 临时实例，键人 以下命令：

% start-hbase.sh

在默认情况下，HBase 会被写入到 /${java.io.tnjpdir}/hbase-${username}中 0${java.io.tmpdir}通常被映射为々即，不过，还是应该通过设置 hbase-site.xml 中的 hbase.tmp.dir来对 HBase 进行配置，以便使用更长久的存储位置。在独 立模式下，HBase主控机、regionserver和 ZooKeeper 实例都是在同一个 JVM 中运 行的。

要管理 HBase 实例，键入以下命令启动 HBase 的 shell 环境即可：

% hbase shell

HBase Shell; enter •help<RETURN>• for list of supported commands.

Type "exit<RETURN>” to leave the HBase Shell

Version 0.98.7-hadoop2, r800c23e2207aa3f9bddb7e9514d8340bcfb89277J Wed Oct 8 15:58:11 PDT 2014

hbase(main):001:0>

这将启动一个加入了一些 HBase 特有命令的 JRuby 1RB解释器。输入 help 然后 按 RETURN 键可以查看已分组的 shell 环境的命令列表。输入 help COMMAND_GROUP可以查看某一类命令的帮助，而输入 help COMMAND则能获得某 个特定命令的帮助信息和用法示例。命令使用 Ruby 的格式来指定列表和目录。主 帮助屏幕的最后包含一个快速教程。

现在，让我们创建一个简单的表，添加一些数据，再把表清空 要新建一个表，首先必须为你的表起一个名字，并为其定义模式。一个表的模式 包含表的属性和列族的列表。列族本身也有属性，可以在定义模式时依次定义它 们。例如，列族的属性包括列族是否应该在文件系统中被压缩存储以及一个单元 格要保存多少个版本等。模式可以被修改，需要修改时把表设为“离线” (offline) 即可。在 shell 环境中使用 disable 命令可以把表设为离线，使用 alter 命令可 以进行必要的修改，而 enable 命令则可以把表重新设为“在线”(online)。

要想新建一个名为 test 的表，使其只包含一个名为 data 的列，表和列族属性都 为默认值，则键入以下命令：

hbase(main):001:0> create 'test1, 'data'

0 row(s) in 0.9810 seconds

如果前面有命令没有成功完成，那么 Shell 环境会提示错误并显示堆栈跟踪 ' (stack trace)信息。这时你的安装肯定没有成功。请检查 HBase 日志目录中的

胃主控机日志，查看哪里出了问题。默认的日志目录是

关于如何在定义模式时添加表和列族属性的示例，可参见 help 命令的输出。

为了验证新表是否创建成功，运行 list 命令。这会输出用户空间中的所有表:

hbase(main):002:0> list

TABLE

test

1 row(s) in 0.0260 seconds

要在列族 data 中三个不同的行和列上插入数据，获取第一行，然后列出表的内

容，输入如下:



| hbase(main):003:0> | put  | ■test、 | •rowl \  | 'data:l、 | 'valuel' |
| ------------------ | ---- | ------- | -------- | --------- | -------- |
| hbase(main):004:0> | put  | •test、 | •row2',  | ■data^1,  | 'value2’ |
| hbase(main):005:0> | put  | •test、 | ’ row3、 | ’data:3、 | vvalue3v |
| hbase(main):006:0> | get  | •test、 | 'rowl'   |           |          |
| COLUMN             | CELL |         |          |           |          |



data:1    timestamp=1414927084811, value=valuel

1 row(s) in 0.0240 seconds hbase(main):007:0> scan 'test'

ROW    COLUMN+CELL



rowl

row2

row3



3 row(s) in 0.0240 seconds



column=data:l, timestamp=1414927084811, value=valuel column=data:2Jtimestamp=1414927125174, value=value2 column=data:3j timestamp=1414927131931, value=value3



请注意我们是如何在添加三个新列的时候不修改模式的。



为了移除这个表，首先要把它设为禁用，然后删除:



hbase(main):009:0> 0 row(s) in 5.8420 hbase(main):010:0> 0 row(s) in 5.2560 hbase(main):011:0> TABLE



disable 'test seconds drop 'test' seconds list



0 row(s) in 0.0200



seconds



通过运行以下命令来关闭 HBase 实例:

% stop-hbase.sh



要想了解如何设置分布式的 HBase，并把它指向正运行的 HDFS，请参见 HBase 文档中有关 configuration section 的小节，网址为 <http://hbase.apache.org/book> /configuration.html 0



##### 20.4客户端

麝

和 HBase 集群进行交互，有很多种不同的客户端可供选择。

###### 20.4.1 Java

HBase和 Hadoop 一样，都是用 Java 开发的。范例 20-1展示了 20.3.1节中在 shell 环境下运行的 hva 实现版本。



范例 20-1.基本的表管理与访问 public class ExampleClient {

public static void main(String[] args) throws IOException {

Configuration config = HBaseConfiguration.create();

// Create tabLe

HBaseAdmin admin = new HBaseAdmin(config); try {

TableName tableName = TableName.valueOf("test");

HTableDescriptor htd = new HTableDescriptor(tableName);

HColumnDescriptor hcd = new HColumnDescriptor(Hdata"); htd•addFamily(hcd);

admin.createTable(htd);

HTableDescriptor^] tables = admin.listTables(); if (tables.length != 1 &&

Bytes.equals(tableName•getName(), tables[0].getTableName()•getName())) { throw new IOException("Failed create of table11);

I

}

// Run some operations -- three puts, a get, and a scan -- against the tabLe.

HTable table = new HTableCconfig^ tableName); try {

for (int i = 1; i <= 3; i++) {

byte[] row = Bytes.toBytes("row" + i);

Put put = new Put(row);

byte[] columnFamily = Bytes.toBytes("data"); byte[] qualifier = Bytes.toBytes(String.valueOf(i)); byte[] value = Bytes.toBytes("value.1 + i); put.add(columnFamilyqualifier, value);

table.put(put);

}

Get get = new Get(Bytes.toBytes(n rowl"));

Result result = table.get(get);

System.out.printIn(NGet: " + result);

Scan scan = new Scan();

ResultScanner scanner = table.getScanner(scan); try {

for (Result scannerResult : scanner) {

System.out.println(HScan: " + scannerResult);

}

} finally { scanner.close();

}

// Disable then drop the tabLe admin.disableTable(tableName); admin.deleteTable(tableName);

} finally {

table.close();

}

} finally {

admin.close();

}

} 、

}

关于 HBase 585

这个类只有一个主方法。为了简洁，我们没有给出导入包的信息。大多数 HBase 类者 P 位于 org.apache.hadoop.hbase 和 org.apache.hadoop. hbase.client

包中。

在这个类中，我们首先让 HBaseConfiguration 类来创建 Configuration 对 象。这个类会返回一个 Configuration，其中已经读入了程序 classpath 下 site.xml ft hbase-default.xml 文件中的 HBase 配置信息。这个 Configuration 接 下来会被用于创建 HBaseAdmin 和 HTable 实例。HBaseAdmin用于管理 HBase 集 群，添加和丢弃表。HTable则用于访问指定的表。Configuration实例将这些 类指向了执行这些代码的集群。

![img](Hadoop43010757_2cdb48_2d8748-253.jpg)



从 HBasel.O开始，新的客户端 API 更加干净且直观。HBaseAdmin和 HTable 的

构造函数已被弃用，不建议客户端显式引用这些旧类。取而代之的是，客户端 应该使用新的 ConnectionFactory 类创建一个 Connection 对象，然后根据需要 调用 getAdmin()或 getTable()来检索 Admin 或 Table 实例。连接管理以前是在 幕后为用户完成的，而现在则是客户端的职责。在本书的附带网站上可以找到 使用了新的 API 的本章示例的更新版本。

要创建一个表，我们需要首先创建一个 HBaseAdmin 的实例，然后让它来创建名 为 test 且只有一个列族 data 的表。在我们的示例中，表的模式是默认的。可以 使用 HTableDescripttor 和 HColumnDescriptor 中的方法来修改表的模式。接 下来的代码测试了表是否真的被创建了，如果没有，则抛出异常。

要对一个表进行操作，我们需要新建一个 HTable 的实例，并把我们的 Configuration实例和我们要操作的表的名称传递给它。然后，我们在循环中创 建 Put 对象，以便将数据插入到表中。Put把单个的单元格值 valuen 放入名为 rown的行的名为 data:n的列上，其中 n 为 1 到 3。列名通过两个部分指定：歹嗾名 和列族修饰词。上面这段代码使用了 HBase的 Bytes 实用类来把标识符和值转换为 HBase所需的字节数组。

接着，我们新建一个 Get 对象来获取和打印刚添加的第一行。然后，再使用 Scan 聰来搬酗表，拼丁印攏課。

在程序的最后，我们首先禁用表，接着删除它，把这张表清除掉。我们曾提到过 在丢弃表前必须先禁用它。

扫描器

H Base扫描器(scanner)和传统数据库中的游标(cursor)或 Java 中的迭代器 (iterator)类似。它和后者的不同之处在于使用后需要关闭。扫描器按次序返回 数据行。用户使用已设置的 Scan 对象实例作为 scan 参数，调用 getScanner()，以此来获取 HBase 中一个表上的担描器。通过 Scan 实例， 你可以传递扫描开始位置和结束位置的数据行、返回结果中要包含的列以及运 行在服务器端的过滤器。ResultScanner接口是调用 getScanner()时返回的 接口，它的定义如下：

public interface ResultScanner extends Closeable厂 Iterable<Result> { public Result next() throws IOException;

.public Result [] next(int nbRows) throws IOException; public void close();

}

可以查看接下来的一个或多个数据行。扫描器会在幕后每次获取 100 行数据， 把这些结果放在客户端，并只有在当前这批结果都被取光后才再去服务器端获 取下一批结果。以这种方式获取和缓存的行数是由 hbase.client. scanner. caching配置选项所决定的，或者也可以通过 setCaching()方法设置 Scan 实例自己缓存(cache)/預取(prefetch)的行数。

较大的缓存值可以加速扫描器的运行，但也会在客户端使用较多的内存。而 且，还要避免把缓存值设得太高，因为它会导致客户端用于处理一批数据的时 间超出扫描器的超时时间。如果在扫描器超时之前，客户端没有能再次访问服 务器，那么扫描器在服务器端所用的资源会被服务器端的垃圾收集器自动回 收。默认的扫描器超时时间为 60 秒，它在 hbase.client.scanner, timeout .period中设置。如果扫描器超时，客户端会收到一个 UnknownScannerException 异常。

编译这段程序的最简单的方法是使用本书示例代码附带的 Maven POM。然后，我 们可以用后面跟着类名的 hbase 命令来运行程序，如下所示：

% mvn package

% export HBASE_CLASSPATH=hbase-examples.jar

% hbase ExampleClient

Get: keyvalues={rowl/data:l/1414932826551/Put/vlen=6/mvcc=0} Scan: keyvalues={rowl/data:l/1414932826551/Put/vlen=6/mvcc=0} Scan: keyvalues={row2/data:2/1414932826564/Put/vlen=6/mvcc=0} Scan: keyvalues={row3/data:3/1414932826566/Put/vlen=6/mvcc=0}

通过 Result 的 toString()方法可以让每行输出显示一个 HBase 数据行。字段由

斜杠字符分隔且顺序如下：行名称，列名称，单元格时间戳，单元格类型，值的 字节数组(vlen)长度和一个内部 HBase 字段(mvcc)。稍后我们将看到如何使用 getValue()方法从 Result 对象中获取值。

###### 20.4.2 MapReduce

org.apache.hadoop.hbase.mapreduce包中的类和工具有利于将 HBase 作为 MapReduce作业的源/输出。Tablel叩 utFormat 类可以在区域的边界进行分割， 使 map 能够拿到单个的区域进行处理。TableOutputFormat将把 reduce 的结果 写人 HBase。

范例 20-2 中的 SimpleRowCounter 类(它是 HBase mapreduce 包中 RowCounter 类 的简化版本)使用 TablelnputFormat 来运行一个 map 任务，以计算行数。

范例 20-2. 一个计算 HBase 表中行数的 MapReduce 应用程序

public class SimpleRowCounter extends Configured implements Tool {

static class RowCounterMapper extends TableMapper<ImmutableBytesWritableJ Result〉 {

public static enum Counters { ROWS }

^Override

public void map(ImmutableBytesWritable row, Result value, Context context) { context.getCounter(Counters.ROWS).increment(1);

}

}

@0verride

public int run(String[] args) throws Exception { if (args.length != 1) {

System.err.pnintln(uUsage: SimpleRowCounter <tablename>H); return -1;

}

String tableName = args[0];

Scan scan = new Scan();

scan.setFilter(new FirstKeyOnlyFilter());

Job job = new 3ob(getConf()， getClass().getSimpleName()); job.setJarByClass(getClass());

TableMapReduceUtil.initTableMapper3ob(tableName, scan,

RowCounterMapper.class, ImmutableBytesWritable.class. Result.class, job);

job.setNumReduceTasks(O);

job.setOutputFonmatClass(NullOutputFormat.class); return job.waitForCompletion(true) ? 0 : 1;

}

public static void main(String[] args) throws Exception { int exitCode = ToolRunner.run(HBaseConfiguration.create()

new SimpleRowCounter(), args);

System.exit(exitCode);

嵌套类 RowCounterMapper 是 HBase 的 TableMapper 抽象类的一个子类。后者是

org.apache.hadoop.mapreduce.Mapper 的“特化” (specialization)0 它设定 map 的输入类型由 TablelnputFormat 来传递。输入的键为 ImmutableBytesWritable 对象 （行键），值为 Result 对象（扫描的行结果）。由于这个作业只对行进行计数，而且没有从 map中发送任何输出，因此我们仅为看到的每一行把 Counters.ROWS的值递增 1。

在 run（）方法中，我们创建了一个扫描对象。这个扫描对象通过调用 TableMapReduceUtil.initTableMap]ob（）实用方法来对作业进行配置，它除了设 置输入格式到 TablelnputFormat 之外，还可以做其他一些事情（例如设置要使用 的 map 类）。

注意这里是如何设置扫描过滤器（即 FirstKeyOnlyFilter 实例）的。这个过滤器告 诉服务器，让它在运行服务器端任务时只用每行的第一个单元格来填充 mapper 中 的 Result 对象。由于 mapper 忽略了单元格值，因此这个优化是有意义的。

S也可以通过在 HBase 的 shell 环境中输入 count ’tablename•命令来査询某个表 的行数。不过，它不是分布式命令，因此适用干 MapReduce 程序中的大 醜。

###### 20.4.3 REST 和 Thrift

HBase提供了 REST和 Thrift 接口。在使用 Java 以外的编程语言和 HBase 交互 时，会用到这些接口。在所有情况下，Java服务器上都运行着一个 HBase 客户端 实例，它负责协调 REST 和 Thrift 应用请求和 HBase 集群间的双向交互。有关服 务的运行以及客户端接口的详细情况，请参阅参考指南，网址为<http://hbase>. apache.org/book.html Q

##### 20.5创建在线查询应用

虽然 HDFS 和 MapReduce 是用于对大数据集进行批处理的强大工具，但对于读或 写单独的记录，效率却很低。在这个示例中，我们将看到如何用 HBase 来填补它 们之间的鸿沟。

前面几章描述的气象数据集包含过去 100 多年上万个气象站的观测数据。这个数 据集还在继续增长，它的大小几乎是无限的。在这个示例中，我们将构建一个简 单的在线查询（而不是批处理）界面，允许用户按时间顺序导航不同的观测站并浏览 历史气象观测值。我们将为此而构建一个简单的命令行 Java 应用程序，不过也不 难从中看出应当如何用相同的技术来构建一个具有同样效果的 Web 应用程序。

为此，让我们假设数据集非常大，观测数据达到上亿条记录，且气温更新数据到 达的速度很快——比如从全球观测站收到超过每秒几百到几千次更新。不仅如 此，我们还假设这个在线应用必须能够及时（most叩-to-date）显示观测数据，即在 收到数据后大约 1 秒就能进行显示。

对数据集的第一个要求使我们排除了使用 RDBMS。HBase是一个可能的选择。对 于查询延时的第二个要求排除了直接使用 HDFS。MapReduce作业可以用干建立 索引以支持对观测数据进行随机访问，但 HDFS 和 MapReduce 并不擅长在有更新 到达时维护索引。

###### 20.5.1模式设计

在我们的示例中，有两个表。

\1. stations 表

这个表包含观测站数据。行的键是 stationid。这个表还有一个列族 info，它能

作为键/值字典来支持对观测站信息的査找。字典的键就是列名 inf0:name、 info:location以及 info:description。这个表是静态的，在这里，列族 info的设计类似于 RDBMS 中表的设计。

\2. observations 表

这个表存放气温观测数据。行的键是 stationid 和逆序时间戳构成的组合键。这 个表有一个列族 data，它包含一列 airtemp，其值为观测到的气温值。

对模式的选择取决于我们知道最高效的读取 HBase 的方式。行和列以字典序升序 保存。虽然有二级索引和正则表达式匹配工具，但它们会损失其他性能。清楚地 理解查询数据最高效的方式对于选择最有效的存储和访问数据的设置非常关键。

在 stations 表中，显然选择 stationid 作为键，因为我们总是根据特定站点的 ID来访问观测站的信息。但 observations 表使用的是一个组合键（把观测的时间 戳加在键之后）。这样，同一个观测站的观测数据就会被分组放到一起，使用逆序 时间戳(Long.MAX_VALUE - epoch)的二进制存储，系统把每个观测站观测数据中 最新的数据存储在最前面。

![img](Hadoop43010757_2cdb48_2d8748-254.jpg)



站点 ID 是定长的这件事非常重要。在某些情况下我们需要对数字组件填零以 便行键能够正确排序，否则有可能会遇到一些问题，比如说在仅考虑按字节排 序时，10将会排在 2 之前，而 02 则排在 10 之前。

此外，如果键是整数，那么它会采用二进制来表示，而不是以数字的字符串版 本形式存储，因为前者消耗的空间更少。

在 shell 环境中，可以用以下方法来定义表：

hbase(main):001:0> create 'stations'{NAME => •info’}

0 row(s) in 0.9600 seconds

hbase(main):002:0> create 'observations*{NAME => •data'} 0 row(s) in 0.1770 seconds

在两个表中，我们都只对表单元格的最新版本感兴趣，所以把 VERSIONS 设为 1 (默认值是 3)。

###### 宽表，e调

在 HBase 中，所有访问都是通过主键的，因此在键的设计上应当尽量照

■

顾到如何查询这些数据。在对 HBase 这样的面向列(族)的存储 (http:"en.wikipedia.org/ wiki/Column-oriented_DBMS)设计模氏时，另一件需 要记住的是它可以以极小的开销管理较宽的稀疏表。

HBase并没有内置对数据库连接的支持。但是宽表使我们并不需要让第一 个表和第二个表或第三个表进行数据库连接。一个宽行有时可以容下一个 主键相关的所有姗

人難筆耀:，4

,■If-r<-.



###### 20.5.2加载数据

观测站的数量相对较少，所以我们可以使用任何一种接口来插入这些观测站的静态 数据。在示例代码中包括一个用于执行此操作的 Java 应用程序，运行方式如下：

% hbase HBaseStationlmporter input/ncdc/metadata/stations-fixed-idth.txt

①引自 Daniel J. Abadi 的文章，标题为 “Column-Stores for Wide and Sparse Data”，发表于 2007 年 1 月，网址为 [http://bit.ly/column-stores](http://bit.ly/column-stores%e3%80%82)[。](http://bit.ly/column-stores%e3%80%82)

但是，假设我们要加载数十亿条观测数据。这种数据导入是一个极为复杂的过 程，是一个需要长时间运行的数据库操作。MapReduce和 HBase 的分布式模型让 我们可以充分利用集群。通过把原始输入数据复制到 HDFS，接着运行 MapReduce作业，我们就能读到输人数据并将其写人 HBase。

范例 20-3展示了一个 MapReduce 作业，它将观测数据从前几章所用的输入文件导 入 HBaseo

范例 20-3•从 HDFS 向 HBase 表导入气温数据的 MapReduce 应用

public class HBaseTemperaturelmporter extends Configured implements Tool {

參

static class HBaseTemperatureMapper<K> extends MapperxLongWritable, Text, K, Put> {

private NcdcRecordParser parser = new NcdcRecordParser();

^Override

public void map(LongWritable key. Text value, Context context) throws

IOException, InterruptedException { parser.parse(value.toString()); if (parser.isValidTemperatureC)) {

byte[] rowKey =

RowKeyConverter.makeObservationRowKey(parser.getStationld (), parser.getObservationDate().getTime());

Put p = new Put(rowKey);

p.add(HBaseTemperatureQueny.DATA^COLUMNFAMILY^ HBaseTemperatureQuery.AIRTEMP—QUALIFIER,

Bytes.toBytes(parser.getAirTemperature()));

context.write(null4 p);

}

}

}

^Override

public int run(String[] args) throws Exception { if (args.length != 1) {

System.err.println(HUsage: HBaseTemperaturelmporter <input>•’)； return -1;

}

Job job = new 3ob(getConf()， getClass().getSimpleName()); job.set3arByClass(getClass());

FileInputFormat.addInputPath(job, new Path(args[0]));

job.getConfiguration().set(TableOutputFormat.OUTPUT_TABLE, "observations’ job.setMapperClass(HBaseTemperatureMapper.class); job.setNumReduceTasks(0);

job.setOutputFormatClass(TableOutputFormat.class); return job.waitForCompletion(true) ? 0 : 1;

}

public static void main(String[] args) throws Exception { int exitCode = ToolRunner.run(HBaseConfiguration.create()

new HBaseTemperatureImporter()， args);

System.exit(exitCode);

HBaseTemperatureimporter 有一个名为 HbaseTemperatureMapper 的嵌套 类，它类似于第 6 章的 MaxTemperatureMaper 类。外部类实现了 Tool，并 对调用这个仅含有 map 的作业进行设置。HBaseTemperatureMapper和 MaxTemperatureMapper的输入相同，所进行的解析方法——都使用第 6 章所介 绍的 NcdcRecordParser 来进行——也相同。解析时会检查输入是否为有效的气 温。但是不同于在 MaxTemperatureMapper 中仅把有效气温加到输出集合中，这 个类创建一个 Put 对象以便把有效的气温值添加到 HBase 的 observations 表的

data:airtemp 列。我们使用了从 HBaseTemperatureQuery 类中导出的 data 和

airtemp静态常量。后面对此会有介绍

我们所用的行的键在 RowKeyConverter 上的 makeObservationRowKey()方法 中，用观测站 ID 和观测时间创建：

public class RowKeyConverter {

private static final int STATION^ID^LENGTH = 12;

/**

\* ^return A roM key whose format is: <station一 id>〈reverse—order一 timestamp〉 */

public static byte[] makeObservationRowKey(String stationld, long observationTime) {

byte[] row = new byte[STATION_ID_LENGTH + Bytes.SIZEOF_LONG];

Bytes.putBytes(row, Q, Bytes.toBytes(stationld), 0, STATION一 ID 一 LENGTH); long reverseOrderTimestamp = Long.MAX—VALUE - observationTime;

Bytes•putLong(rowJ STATION—ID—LENGTH, reverseOrderTimestamp); return row;

}

}

观测站 ID 其实是一个定长字符串。在转换时利用了这一点。与前面的例子一样， 我们使用 HBase 的 Bytes 类在字节数组和常用的 Java 类型之间进行转换。 Bytes. SIZEOF_LONG常量用于计算行键字节数组的时间戳部分的大小。 putBytes()和 putLong()方法用于填充行键字节数组中的观测站 ID 和时间戳部 分，使它们处于相应的偏移位置。

该作业在 run()方法中被配置为使用 HBase 的 TableOutputFormat。将要写入的 表必须在作业配置中通过设置 TableOutputFormat.OUTPUTTABLE属性来指定。

TableOutputFormat的使用为我们提供了方便，

为它负责管理 HTable 实例的



创建，否则我们需要在 mapper 的 setup()方法中来做这件事(另外还需要在 cleanup()方法中调用 close())o TableOutputFormat 禁用了 HTable 的自动刷

新功能，



此可以缓存对 put()的调用以提高效率。这段示例代码包括一个名为

HBaseTemperatureDirectlmporter 的类，以演示如何在 MapReduce 程序中直 接使用 HTable。我们可以用下面的命令来运行程序：

% hbase HBaseTemperaturelmporter input/ncdc/all

\1.    加载的分布

要特别当心数据导入所引发的“步调一致”的情况。这时所有的客户端都对同一个 表的区域(在单个节点上)进行操作，然后再对下一个区域进行操作，依次进行。这 时加载操作并没有均匀地分布在所有区域上。这通常是由“排序后输入”(sorted i叩 ut)和切分的原理共同造成的。如果在插入数据前，针对行键按数据排列的次序 进行随机处理，可能有助于减少这种情况。在我们的示例中，基于当前 stationid值的分布情况和 TextlnputFormat 分割数据的方式，上传操作应该 能够保证足够的分布式特性。

如果一个表是新的，一开始它只有一个区域。此时所有的更新都会加载到这个区 域，直到区域分裂为止。即使数据行的键是随机分布的，我们也无法避免这种情 况。这种启动现象意味着上传数据在开始时比较慢，直到有足够多的区域被分布 到各个节点，集群的成员都能够参与到上传为止。不要把这种情况与前面一段所 描述的情况混为一谈，它们是不同的。

这两个问题都可以通过使用批量加载来避免，下面我们将讨论批量加载。

\2.    批量加载

HBase有一个高效的“批量加载” (bulk loading)工具。它从 MapReduce 把以内部

格式表示的数据直接写入文件系统，从而实现批量加载。顺着这条路，我们加载 HBase实例的速度比用 HBase 客户端 API 写入数据的方式至少快一个数量级。

批量加载的处理过程分为两步。第一步使用 HFileOutputFormat2 通过一个 MapReduce作业将 HFiles 写入 HDFS 目录。由于数据行必须按顺序写入，因此 这个作业需要执行行键的完全排序(参见 9.2.3节)。HFileOutputFormat2的 configureIncrementalLoad()方法可以完成所有必要的配置。

批量加载的第二步涉及将 HFiles 从 HDFS 移入现有的 HBase 表中。这张表在此

过程中可以是活跃的。在示例代码中包括了一个名为 HBaseTemperature Bulklmporter的类用于以批量加载方法来加载气象观测数据。

###### 20.5.3在线查询

为了实现在线查询应用，我们将直接使用 HBase 的 Java API。在这里，我们将深 刻体会到选择模式和存储格式的重要性。

1.观测站信息查询

最简单的查询就是获取静态的观测站信息。这是一个单行的查找，通过使用 get() 操作来执行。这一类査询在传统数据库中也很简单，但 HBase 提供了额外的控制 功能和灵活性。我们把 info 列族作为键/值字典(列名作为键，列值作为值)， HBaseStationQuery中的这段代码如下所示：

static final byte[] static final byte[] static final byte[] static final byte[]



INFO^COLUMNFAMILY = Bytes.toBytes("info");

NAME_QUALIFIER = Bytes.toBytes("name");

LOCATION一 QUALIFIER = Bytes.toBytes(Hlocation H; DESCRIPTION一 QUALIFIER = Bytes.toBytes("description");

public Map<String, String〉 getStationInfo(HTable table, String stationld) throws IOException {

Get get = new Get(Bytes.toBytes(stationld)); get.addColumn(INFO_COLUMNFAMILY);

Result res = table.get(get); if (res == null) {

return null;

}

Map<String, String〉 resultMap = new HashMap<String, String>(); resultMap.put(” name” , getValue(res, INFO_COLUMNFAMILY, NAME一 QUALIFIER)); resultMap.put(Hlocation", getValue(res, INFO^COLUMNFAMILY, LOCATION一 QUALIFIER)); resultMap.put(’.description", getValue(res, INFO_COLUMNFAMILY,

DESCRIPTION-QUALIFIER)); return resultMap;

}

private static String getValue(Result res, byte [] cf, byte [] qualifier) { byte [] value = res.getValue(cfqualifier); return value == null? .’： Bytes.toString(value);

}

在这个示例中，getstationlnfo()接收一个 HTable 实例和一个观测站 ID。为 了获取观测站的信息，我们使用 HTable.get()来传递一个 Get 实例。它被设置 为用于获取已定义列族 INFO_COLUMNFAMILY中由观测站 ID 所指明的列的值。

get()的结果在 Result 中返回。它包含数据行，你可以通过操作需要的列单元格

来取得单元格的值。getstationlnfo()方法把 Result 转换为更便于使用的由 String类型的键和值构成的 Map。

我们已经看出在使用 HBase 时为什么需要工具函数了。在 HBase 上，为了处理底 层的交互，我们已经开发出越来越多的抽象。但是，理解它们的工作机理以及各 个存储选项之间的差异，非常重要。

和关系型数据库相比，HBase的优势之一是不需要我们预先设定列。所以在将 来，如果每个观测站在这三个必有的属性以外还有几百个可选的属性，我们便可 以直接插入这些属性而不需要修改模式。当然，你的应用中读和写的代码是需要 修改的。在示例中，我们可以循环遍历 Result 来获取每个值，而不用显式获取 各个值。

以下是观测站査询的示例：

% hbase HBaseStationQuery 011990-99999 name SIHCCA3AVRI location (unknown) description (unknown)

2.观测数据查询

对 observations 表的査询需要输入的参数包括站点 ID、开始时间以及要返回的 最大行数。由于数据行是按观测站以观测时间逆序存储的，因此査询将返回发生

在开始时间之后的观察值。范例 20-4中的 getStationObservations()方法使用 HBase扫描器对表行进行遍历。它返回一个 NavigableMap<Long, Integer〉，

其中键是时间戳，值是温度。由于这个 map 按键的升序来排序，因此其中的数据 项是按时间顺序来排列的。

范例 20-4.检索 HBase 表中某范



内气象站观测数据行的程序



public class HBaseTemperatureQuery extends Configured implements Tool { static final byte[] DATA_COLUMNFAMILY = Bytes.toBytes(ndataH); static final byte[] AIRTEMP一 QUALIFIER = Bytes.toBytes(,,airtempM);

public NavigableMap<LongJ Integer〉 getStationObservations(HTable table.

String stationld, long maxStamp, int maxCount) throws IOException {

byte[] startRow = RowKeyConverter.makeObservationRowKey(stationldmaxStamp); NavigableMap<Long, Integer〉 resultMap = new TreeMap<Long, Integer>();

Scan scan = new Scan(startRow);

scan.addColumn(DATA_COLUMNFAMILY, AIRTEMP^QUALIFIER);

ResultScanner scanner = table.getScanner(scan); try {

Result res;

int count = 0;

while ((res = scanner.next()) != null && count++ < maxCount) { byte[] row = res.getRow();

byte[] value = res.getValue(DATA_COLUMNFAMILY, AIRTEMP—QUALIFIER);

Long stamp = Long•MAX-VALUE •

Bytes.toLong(row, row.length - Bytes.SIZEOF—LCNG, Bytes.SIZEOF_LONG); Integer temp = Bytes.tolnt(value); resultMap.put(stamp, temp);

}

} finally { scanner.close();

}

return resultMap;

}

public int run(String[] args) throws IOException { if (args.length != 1) {

System.err.println(HUsage: HBaseTemperatureQuery 〈station—id>")j return -1;

}

HTable table = new HTable(HBaseConfiguration.create(getConf()), "observations");

NavigableMap<Long> Integer〉 observations = getStationObservationsCtable^ args[0],

Long.MAX-VALUE， 10).descendingMap(); for (Map.Entry<LongJ Integer〉 observation : observations.entrySet()) {

// Print the date, time, and temperature

System.out.printf(H%l$tF %l$tR\t%2$s\nH, observation.getKey(), observation•getValue());

}

return 0;

} finally {

table.close();

}

}

public static void main(String[] args) throws Exception { int exitCode = ToolRunner.run(HBaseConfiguration.create()} new HBaseTemperatureQuery(), args);

System.exit(exitCode);

}

}

run()方法调用 geStationObservations()以请求最近的 10 个观测值，并通过 调用 descendingMap()使返回值仍然回归到降序。观测值被格式化并打印到控制 台(记住，温度的单位是十分之一度)。例如：

% hbase HBaseTemperatureQuery 011990-99999 1902-12-31 20:00 -106 1902-12-31 13:00 -83 1902-12-30 20:00 -78

1902-12-30

3 0 3 6 0 3 0 12 10 2 12



00 -100 00 -128 00 -111 00 -111 00 -117 00 -61

00 -22



1902-12-29

1902-12-29

1902-12-29

1902-12-28

1902-12-28

1902-12-27

按时间逆序存储时间戳的优点是，通常在线应用程序需要的是最新的观测值，而 这样做能更好地满足这一点。如果观测数据直接用实际的时间戳来存放，我们就 只能根据偏移量和限制范围高效地获取最老的观测数据。要获取最新的数据则意 味着要拿到所有的数据，直到最后才能获得结果。与此相比，获取前〃行然后退 出扫描程序(这种场景有时被称为“提早过滤”)的效率显然更高。

![img](Hadoop43010757_2cdb48_2d8748-256.jpg)



HBase 0.98新增了反向扫描的能力，也就是说现在可以按时间顺序存储观测 值，并从给定的起始行开始反向扫描。反向扫描比正向扫描要慢几个百分点。 若要使用反向扫描，请在开始扫描之前调用 Scan 对象的 setReversed(true)

方法。

##### 20.6 HBase 和 RDBMS 的比较

HBase和其他面向列的数据库常常被拿来和更流行的传统关系型数据库(或简写为 RDBMS)进行比较。虽然它们在实现和设计上的出发点有着较大的区别，但它们 都力图解决相同的问题。所以，虽然它们有很多不同点，但我们仍然能够对它们 进行客观的比较。

如前所述，HBase是一个分布式的、面向列的数据存储系统。它通过在 HDFS 上 提供随机读/写来解决 Hadoop 不能处理的问题。HBase自底层设计开始即聚焦干 各种可伸缩性问题：表可以很“高”(数十亿个数据行 h 表可以很“宽”(数百万 个列)；水平分区并在上千个普通商用机节点(commodity node)上自动复制。表的 模式是物理存储的直接反映，使系统有可能提供高效的数据结构的序列化、存储 和检索。但是，应用程序的开发者必须承担重任，选择以正确的方式使用这种存 储和检索方式。

严格来说，RDBMS 是一个遵循 “Codd 的 12 条规则” (<http://en.wikipedia.org>/wi/d/Codd%27s_l2_rules)的数椐库。标准的 RDBMS 是模式固定、面向行的数据 库且具有 ACID 性质和复杂的 SQL 查询处理引擎。RDBMS强调事务的“强一致 性” (strong consistency)、参照完整性(referential integrity)、数据抽象与物理存储层

相对独立，以及基于 SQL 语言的复杂查询支持。在 RDBMS 中，可以非常容易地 建立“二级索引” (secondary index)，执行复杂的内连接和外连接，执行计数、求 和、排序、分组等操作，或对表、行和列中的数据进行分页存放。

对于大多数中小规模的应用，如 MySQL 和 PostgreSQL 之类现有开源 RDBMS 解 决方案所提供的易用性、灵活性、产品成熟度以及强大、完整的功能特性几乎是 无可替代的。但是，如果要在数据规模和并发读/写这两方面中的任何一个(或全部) 上进行大规模向上扩展(scale up)，就会很快发现 RDBMS 的易用性会让你损失不 少性能，而如果要进行分布式处理，更是非常困难。RDBMS的扩展通常要求打破 Codd的规则，如放松 ACID 的限制，使 DBA 的管理变得复杂，并同时放弃大多 数关系型数据库引以为荣的易用性。

###### 20.6.1成功的服务

这里将简单介绍一个典型的 RDBMS 如何进行扩展 到大的生长过程。

下面给出一个成功服务从小



(1)    服务首次提供公开访问。

将服务从本地工作站迁移到拥有良好模式定义的、共享的远程 MySQL 实 例上。

(2)    服务越来越受欢迎；数据库收到太多的读请求。

用 memcached 来缓存常用查询结果。这时读不再是严格意义上的 ACID；缓存数据必须在某个时间到期。

(3)    对服务的使用继续增多；数据库收到太多的写请求。

通过购买一个 16 核、128GB RAM、配备一组 15k RPM硬盘驱动器的增 强型服务器来垂直升级 MySQL。非常昂贵。

(4)    新的特性增加了查询的复杂度；包含很多连接操作。

对数据进行反规范化以减少连接的次数。(这和 DBA 培训时所教的不一 样！)

(5)服务被广泛使用；所有的服务都变得非常慢。

停止使用任何服务器端计算(server-side computation)。

(6)有些查询仍然太慢。

定期对最复杂的查询进行“预物化” (prematerialize)，并尝试在大多数情

况下停止使用连接。

(7)读性能尚可，但写仍然越来越慢。

放弃使用二级索引和触发器。(没有索引了？)

迄今为止，如何解决以上扩展问题并没有一个清晰的解决办法。无论怎样，都需 要开始橫向进行扩展。可以尝试在大表上进行某种分区或査看一些能提供多主控 机的商业解决方案。

无数应用、行业以及网站都成功实现了 RDBMS的可伸缩性、容错和分布式数据 系统。它们都使用了前面提到的很多策略。但最终，你所拥有的已经不再是一个 真正的 RDBMS。由于妥协和复杂性问题，系统放弃了很多易用性特性。任何种类 的从属复本或外部缓存都会对反规范化的数据引入弱一致性(weak consistency)。连 接和二级索引的低效意味着绝大多数查询成为主键查找。而对于多写入机制 (multiwriter)的设置很可能意味着根本没有实际的连接，而分布式事务会成为一个 噩梦。这时，要管理一个单独用于缓存的集群，网络拓扑会变得异常复杂。即使 有一个做了那么多妥协的系统，你仍然会情不自禁地担心主控机崩溃或担心在几 个月后，数据或负载可能会增长到当前的 10 倍。

###### 20.6.2 HBase

让我们考虑 HBase，它具有以下特性。

没有真正的索引行是顺序存储的，每行中的列也是，所以不存在索引 膨胀的问题，而且插入性能和表的大小无关。

•自动分区在表增长的时候，表会自动分裂成区域，并分布到可用的节 点上。

•线性扩展和对于新节点的自动处理增加一个节点，把它指向现有集群 并运行 regionserver。区域自动重新进行平衡，负载均匀分布。

•普通商用硬件支持集群可以用 1000〜5000美金的单个节点搭建，而 不需要使用单个得花 5 万美金的节点。RDBMS需要支持大量 1/0，因此 要求更昂贵的硬件。

•容错大量节点意味着每个节点的重要性并不突出。不用担心单个节点 失效。

批处理 MapReduce 集成功能使我们可以用全并行的分布式作业根据 C据的位置” (location awareness)来处理它们。

(4 ikZ



如果没日没夜地担心数据库(正常运行时间、扩展性问题、速度)，应该好好考虑从 RDBMS转向使用 HBase0 应该使用一个针对扩展性问题的解决方案，而不是性能 越来越差却需要大量投入的曾经可用的方案。有了 HBase，软件是免费的，硬件 是廉价的，而分布式处理则是与生俱来的。

##### 20.7 Praxis

在这一小节，我们将讨论在应用中运行 HBase 集群时用户常常遇到的一些问题。

###### 20.7.1 HDFS

HBase使用 HDFS 的方式与 MapReduce 使用 HDFS 的方式截然不同。在 MapReduce中，首先打开 HDFS 文件，然后 map 任务流式处理文件的内容，最后 关闭文件。在 HBase 中，数据文件在启动时就被打开，并在处理过程中始终保持 打开状态，这是为了节省每次访问操作打开文件所需的代价。所以，HBase更容 易碰到 MapReduce 客户端不常碰到的问题：    .

1.文件描述符用完

由于我们在连接的集群上保持文件的打开状态，所以用不了太长时间就可能达到 系统和 Hadoop 设定的限制。例如，我们有一个由三个节点构成的集群，每个节点 上运行一个 datanode 实例和一个 regionserver。如果我们正在运行一个加载任务， 表中有 100 个区域和 10 个列族。我们允许每个列族平均有两个“刷入文件”

(flush file)。通过计算，我们知道同时打开了 100x10x2，即 2000 个文件。此

外，还有各种外部扫描器和 Java 库文件占用了其他文件描述符。每个打开的文件 在远程 datanode 上至少占用一个文件描述符。

一个进程默认的文件描述符限制是 1024。当我们使用的描述符个数超过文件系统 的 Ww/Z值，我们会在日志中看到“Too many open files”(打开了太多文件)的错 误信息。但在这之前，往往就已经能看出 HBase 的行为不正常。要修正这个问题 需要增加文件描述符的 ulimit 参数值。有关如何增加集群的 ulimit 值，请参阅 HBase 参考指南，网址为 [http://hbase.apache.org/book.html](http://hbase.apache.org/book.html%e3%80%82)[。](http://hbase.apache.org/book.html%e3%80%82)

\2. datanode上的线程用完

和前面的情况类似，Hadoop 1的 datanode 上同时运行的线程数不能超过 256 这一 限制值(dfs.datanode.max.xcievers)，这个限制值会导致 HBase 异常运行。 Hadoop 2将默认值提高到 4,096，因此最近几个版本的 HBase(仅在 Hadoop 2及更 高版本上运行)出现问题的可能性较小。可以通过在 hdfs-site.xml中配置 dfs. datanode. max. transfer .threads(此属性的新名称)来更改设置。

###### 20.7.2用户界

HBase在主控机上运行了一个 Web 服务器，它能提供运行中集群的状态视图。在 默认情况下，它监听 60010 端口。主界面显示了基本的属性(包括软件版本、集群 负载、请求频率、集群表的列表)和加人的 regionserver 等。在主界面上单击选中 regionserver会把你带到那个 regionserver 上运行的 Web 服务器，它列出了这个服 务器上所有区域的列表及其他基本的属性值，比如如使用的资源和请求频率。

###### 20.7.3度量

Hadoop有一个度量(metric)系统。可以用它每过一段时间获取系统重要组件的信 息，并输出到上下文(context)，详情参见 11.2.2节。启用 Hadoop 度量系统，并把 它捆绑入 Ganglia 或导出到 JMX，就能得到集群上正在做和刚才做的事情的视 图。HBase也有它自己的度量(比如请求频率、组件计数、资源使用情况等)。相关 信息可以参见 HBase conf目录下的 hadoop-metrics2-properties文件。

###### 20.7.4计数器

在 StumbleUpon 公司([https://www.stufnbleupon.com/)，](https://www.stufnbleupon.com/)%ef%bc%8c%e7%ac%ac%e4%b8%80%e4%b8%aa%e5%9c%a8)[第一个在](https://www.stufnbleupon.com/)%ef%bc%8c%e7%ac%ac%e4%b8%80%e4%b8%aa%e5%9c%a8) HBase 上部署的产 品特性是为 stumbleupon.com前端维护计数器。计数器以前存储在 MySQL 中， 但计数器的更新太频繁，计数器所导致的写操作太多，所以 Web 设计者必须对计 数值进行限定。使用 HTable 的 incrementColumnValue()方法以后，计数器每 秒可以实现数千次更新。

##### 20.8延伸阅读

本章对 HBase 做了简单介绍，并没有深入探讨 HBase 所具有的潜力。有关 HBase

更详细的描述，请参阅 O’Reilly在 2011 年出版的 H 从 we: The DefinitiveGuide，讽 址为 [http://hbase.apache.org/book.html，](http://hbase.apache.org/book.html%ef%bc%8c%e4%bd%9c%e8%80%85)[作者](http://hbase.apache.org/book.html%ef%bc%8c%e4%bd%9c%e8%80%85) Lars George，且其新版即将发行，或 者 Manning 出版社 2012 年在 HBase in Action，网址为 <http://www.manning.com/> ditnidukkhurana/，作者 Nick Dimiduk 和 Amandeep Khurana。


