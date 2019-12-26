---
title: 10 关于ZooKeeper
toc: true
date: 2018-06-27 07:51:30
---
迄今为止，整本书都是在教我们大规模数据处理技术。但本章的内容则有所不 同，将介绍如何使用 ZooKeeper 来构建一般的分布式应用。ZooKeeper是 Hadoop 的分布式协调服务。

写分布式应用的主要困难在于会出现“部分失败” (partial failure)。当一条消息在 网络中两个节点之间传送时，如果出现网络错误，发送者无法知道接收者是否已 经收到这条消息。接收者可能在出现网络错误之前就已经收到这条消息，也有可 能没有收到，又或者接收者的进程已经死掉。发送者能够获得真实情况的唯一途 径就是重新连接接收者，并向它发出询问。这种情况就是部分失败，即我们甚至 不知道一个操作是否已经失败。

由于部分失败是分布式系统固有的特征，因此，使用 ZooKeeper 并不能避免出现 部分失败，当然它也不会隐藏部分失败。® ZooKeeper可以提供一组工具，使你 在构建分布式应用时能够对部分失败进行正确处理。

ZooKeeper具有以下特点。

•    ZooKeeper是简单的 ZooKeeper 的核心是一个精简的文件系统，它提 供一些简单的操作和一些额外的抽象操作，例如，排序和通知。

•    ZooKeeper是富有表现力的 ZooKeeper 的基本操作是一组丰富的“构

①详情参见 J. Waldo等人在 1994 年发表的文章，标题为“A Note on Distributed Computing”， 网址为 [http://research.sun.com/techrep/1994/smli](http://research.sun.com/techrep/1994/smli%e4%b8%80tr-94-29.pdfo)[一 tr-94-29.pdfo](http://research.sun.com/techrep/1994/smli%e4%b8%80tr-94-29.pdfo) 分布式编程与本地编程有着根本 的不同，不可忽视。

件” (building block)，可用于实现多种协调数据结构和协议。相关的例

子包括：分布式队列、分布式锁和一组节点中的“领导者选举” (leader election)。

ZooKeeper具有高可用性 ZooKeeper 运行于一组机器之上，并且在设 计上具有高可用性，因此应用程序完全可以依赖于它。ZooKeeper可以 帮助系统避免出现单点故障，因此可以用于构建一个可靠的应用程序。

![img](Hadoop43010757_2cdb48_2d8748-258.jpg)



ZooKeeper采用松耦合交互方式在 ZooKeeper 支持的交互过程中，参 与者不需要彼此了解。例如，ZooKeeper可以被用于实现“数据汇 集”(rendezvous)机制，让进程在不了解其他进程(或网络状况)的情况下

能够彼此发现并进行信息交互。参与的各方甚至可以不必同时存在， 为一个进程可以在 ZooKeeper 中留下一条消息，在该进程结束后，另外 一个进程还可以读取这条消息。

• ZooKeeper是一个资源库 ZooKeeper 提供了一个通用协调模式实现方 法的开源共享库，使程序员免于写这类通用的协议(这通常是很难写正确 的)。所有人都能够对这个资源库进行添加和改进，久而久之，会使每个 人都从中受益。

同时，ZooKeeper也是高性能的。在它的诞生地 Yahoo!公司，对于以写操作为主 的工作负载来说，ZooKeeper的基准吞吐量已经超过每秒 10000 个操作。对于常 规的以读操作为主的工作负载来说，吞吐量更是高出好几倍。®

##### 21.1安装和运行 ZooKeeper

首次尝试使用 ZooKeeper 时，最简单的方式是在一台 ZooKeeper 服务器上以独立 模式(standalone mode)运行。例如，可以在一台用于开发的机器上尝试运行。运行 ZooKeeper需要 Java，因此首先要确认已经安装了 Java。

可以从 Apache 的 ZooKeeper 发布页面(<http://hadoop>. apache.org/zookeeper/ releases.ZooKeeper的一个稳定版本，然后在合适的位置将下载的压缩包

①详细的基准数据可以参见 Patrick Hunt、Mahadev Konar、Flavio P. Junqueira 和 Benjamin Reed 发表的优秀论文，标题为 “ZooKeeper: Wait-free Coordination for Internet-Scale Systems”，网址为 [http://bit.ly/wait-free](http://bit.ly/wait-free%e4%b8%80coordination,%e5%8f%91%e8%a1%a8%e4%ba%8e)[一 coordination，发表于](http://bit.ly/wait-free%e4%b8%80coordination,%e5%8f%91%e8%a1%a8%e4%ba%8e) 2010 年 USENIX 年度技术大会。

解压:

% tar xzf zookeeper-x.y.z.tar.gz

ZooKeeper提供了几个能够运行服务并与之交互的二进制可执行文件，可以很方便 地将包含这些二进制文件的目录加入命令行路径：

% export ZOOKEEPER_HOME=^/sw/zookeeper-x.y.z % export PATH=$PATH:$ZOOKEEPER_HOME/bin

在运行 ZooKeeper 服务之前，我们需要创建一个配置文件。这个配置文件习惯上 被命名为 zoo.cfg，并被保存在 conf 子目录中（也可以把它保存在/Wc/zoMeeper子 目录中；如果设置了环境变量 zoocfgdir，也可以保存在该环境变量所指定的目录 中）。配置文件的内容示例如下：

tickTime=2000

dataDir二/Users/tom/zookeeper clientPort=2181

这是一个标准的 Java 属性文件，本例中定义的三个属性是以独立模式运行 ZooKeeper所需的最低要求。简单地说，tickTime属性指定了 ZooKeeper中的基 本时间单元似毫秒为单位）；dataDir属性指定了 ZooKeeper存储持久数据的本 地文件系统位置；clientPort属性指定了 ZooKeeper用于监听客户端连接的端口 （通常使用 2181 端口）。用户应该将 dataDir 属性的值修改为自己系统所要求的合 适位置。

定义好合适的配置文件之后，我们现在可以启动一个本地 ZooKeeper 服务器:

% zkServer.sh start

使用 nc（也可以使用 telnet）发送 nuok 命令（Are you OK?）到监听端口，检查 ZooKeeper是否正在运行：

% echo ruok | nc localhost 2181 imok

imok是 ZooKeeper 在说“I’m OK”。还有其他一些用于管理 ZooKeeper 的命令， 都采用类似的四字母组合，如表 21-1所示。

除了 mntr命令以外，ZooKeeper还通过 JMX 来披露统计信息。请访问 http://zookeeper.apache.orgl，找到 ZooKeeper 文档，获取详细的相关信息。在安装 目录的卯 ZnT）子目录中包含有相关的监控工具及方法。

类别    命令    描述

服务器状态    ruok    如果服务器正在运行并且未处于出错状态，则输出 imok

| conf | 输出服务器的配置信息（根据配置文件 ZOO.Cfg）                  |
| ---- | ------------------------------------------------------------ |
| envi | 输出服务器的环境信息，包括 ZooKeeper 版本、Java版本和其他系 统属性 |
| srvr | 输出服务器的统计信息，包括延迟统计、znode的数量和服务器运行 模式（standalone、leader 或 follower） |
| stat | 输出服务器的统计信息和已连接的客户端                         |
| srst | 重置服务器的统计信息                                         |
| isro | 显示服务器是否处于只读（ro）模式（由于网络分区），或者读/写（rw）模 式 |

| 客户端连接 | dump                               | 列出集合体中的所有会话和短暂 znodeo 必须连接到 leader 才能够使 用此命令（参考 srvr 命令） |
| ---------- | ---------------------------------- | ------------------------------------------------------------ |
| cons       | 列出所有服务器客户端的连接统计信息 |                                                              |
| erst       | 重置连接统计信息                   |                                                              |
| 观察       | wchs                               | 列出服务器上所有观察的摘要信息                               |
|            | wchc                               | 按连接列出服务器上所有的观察。注意：如果观察的数量较多，此命 令会影响服务器的性能 |

wchp 按 znode 路径列出服务器上所有的观察。注意：如果观察的数量较 多，此命令会影响服务器的性能

表 21-1. ZooKeeper 命令:



U!



字母组合



监控    mntr 按 Java 属性格式列出服务器统计信息。适合于用作 Ganglia 和

Nagios等监控系统的信息源

ZeeKeeper从 3.5.0版本开始内建了用于提供与“四字母组合”相同信息的 web server。可以访问 [http://localhost:8080/commands,](http://localhost:8080/commands,%e8%8e%b7%e5%8f%96%e5%91%bd%e4%bb%a4%e5%88%97%e8%a1%a8%e3%80%82)[获取命令列表。](http://localhost:8080/commands,%e8%8e%b7%e5%8f%96%e5%91%bd%e4%bb%a4%e5%88%97%e8%a1%a8%e3%80%82)

##### 21.2示例

假设有一组服务器用于为客户端提供某种服务。我们希望每个客户端都能找到其 中一台服务器，这样一来，它们就可以使用这项服务。在这个例子中，一个挑战 是如何维护这组服务器的成员列表。

这组服务器的成员列表显然不能存储在网络中的单个节点上，否则该节点的故障 将意味着整个系统的故障（我们希望这个成员列表是高度可用的）。我们先假设已经 有了一种可靠的方法来解决成员列表的存储问题。接下来，如果其中一台服务器 出现故障，我们需要解决如何从服务器成员列表中将它删除的问题。某个进程需 要去负责删除故障服务器，但注意不能由故障服务器自己来完成，因为故障服务 器已经不再运行！ 我们所描述的不是一个被动的分布式数据结构，而是一个主动的、能够在某个外 部事件发生时修改数据项状态的数据结构。ZooKeeper提供了这种服务，接卩来 让我们看看如何使用它来实现这种众所周知的组成员管理应用的。

###### 21.2.1 ZooKeeper中的组成员关系

理解 ZooKeeper 的一种方法是将其看作一个具有高可用性特征的文件系统。这个 文件系统中没有文件和目录，而是统一使用“节点”（node）的概念，称为 znode。 znode既可以作为保存数据的容器（如同文件），也可以作为保存其他 znode 的容器 （如同目录）。所有的 znode 构成了一个层次化的命名空间，一种自然的建立组成员 列表的方式就是利用这种层次结构，创建一个以组名为节点名的 znode 作为父节 点，然后以组成员名（服务器名）为节点名来创建作为子节点的 znode。图 21-1给出 了一组具有层次结构的 znode0

21-1. ZooKeeper 中的 znode

在这个示例中，我们没有在任何 znode 中存储数据，但在一个真实的应用中，你 可以想象将成员相关的数据存睹在它们的 znode 中，例如主机名。

608 第 21 章

###### 21.2.2创建组

让我们通过写一段程序的方式来介绍 ZooKeeper 的 Java API，这段示例程序用干

创建组名为/zoo的 znode，参见范例 21-1。

范例 21-1.该程序在 Zookeeper 中新建表示组的 znode public class CreateGroup implements Watcher {

private static final int SESSIONJTIMEOUT = 5000;

private ZooKeeper zk;

private CountDownLatch connectedSignal = new CountDownLatch(1);

public void connect(String hosts) throws IOException, InterruptedException { zk = new ZooKeeper(hosts, SESSION—TIMEOUT, this); connectedSignal.await();

}

^Override

public void process(WatchedEvent event) { // Watcher interface if (event.getState() == KeeperState.SyncConnected) <

connectedSignal.countDown();

}

}

public void create(String groupName) throws KeeperException, InterruptedException {

String path =    + groupName;

String createdPath = zk.create(path, null/*data*/. Ids.OPEN^ACL^UNSAFE, CreateMode.PERSISTENT);

System.out.println('Treated •• + createdPath);

}

public void close() throws InterruptedException { zk.close();

}

public static void main(String[] args) throws Exception {

CreateGroup createGroup = new CreateGroup(); createGroup.connect(args[0]);

createGroup.create(args[1]); createGroup.close();

}

}

在 main()方法执行时，创建一个 CreateGroup 的实例然后调用这个实例的 connect()方法。connect方法实例化了一个新的 ZooKeeper 类的对象，这个类

艮客户端 AH 中的主要类，用于维护客户端和 ZooKeeper 服务之间的连接。 ZooKeeper类的构造函数有三个参数：第一个参数是 ZooKeeper 服务的主机地址

(可指定端口，默认端口是 2181)；"第二个参数是以毫秒为单位的会话超时参数 (这里我们设成 5 秒)，后文中将给出该参数的详细解释；第三个参数是一个 Watcher对象的实例。Watcher对象接收来自于 ZooKeeper 的回调，以获得各种 事件的通知。在这个例子中，CreateGroup是一个 Watcher 对象，因此我们将它 传递给 ZooKeeper 构造函数。

当一个 ZooKeeper 的实例被创建时，会启动一个线程连接到 ZooKeeper 服务。由 于对构造函数的调用是立即返回的，因此在使用新建的 ZooKeeper 对象前一定要

等待其与 ZooKeeper 服务之间成功建立连接。我们使用 Java 的 CountDownLatch 类(位于 java.util.concurrent包中)来阻止使用新建的 ZooKeeper 对象，直到 这个 ZooKeeper 对象已经准备就绪。Watcher类用于获取 ZooKeeper 对象是否 准备就绪的信息，在它的接口中只有一个方法：

public void process(WatchedEvent event);

当客户端已经与 ZooKeeper 服务建立连接后，Watcher的 process()方法会被调

用，参数是一个用于表示该连接的事件。在接收到一个连接事件(以

Watcher.Event.KeeperState 的枚举型值 SyncConnected 来表示)时，我们通 过调用 CountDownLatch 的 countDown()方法来递减它的计数器。锁存器(latch)

创建时带有一个值为 1 的计数器，用于表示在它释放所有等待线程之前需要发生 的事件数。在调用一次 countDown()方法之后，计数器的值变为 0，则 await() 方法返回。

现在 connect()方法已经返回，下一个执行的是 CreateGroup 的 create()方 法。在这个方法中，我们使用 ZooKeeper 实例中的 create()方法来创建一个新 的 ZooKeeper 的 znode。所需的参数包括：路径(用字符串表示)、znode的内容(字 节数组，本例中使用空值)、访问控制列表(简称 ACL，本例中使用了完全开放的 ACL，允许任何客户端对 znode 进行读/写)和创建 znode 的类型。

有两种类型的 znode:短暂的(ephemeral)和持久的(persistent)。创建 znode 的客户 端断开连接时，无论客户端是明确断开还是因为任何原因而终止，短暂 znode 都 会被 ZooKeeper 服务删除。与之相反，当客户端断开连接时，持久 znode 不会被 删除。我们希望代表一个组的 znode 存活的时间应当比创建程序的生命周期要 长，因此在本例中我们创建了一个持久的 znode。

①对于复制模式下的 ZooKeeper 服务来说，这个参数是一个以逗号分隔的服务器(主机和可选端 口)列表。

create()方法的返回值是 ZooKeeper 所创建的节点路径，我们用这个返回值来打 印一条表示节点路径被成功创建的消息。当我们查看“顺序 znode” (sequential znode)时，会发现 create()方法返回的路径与传递给该方法的路径不同。

为了观察程序的执行，我们需要在本地机器上运行 ZooKeeper，然后可以输入以下 命令：

% export CLASSPATH=ch21-zk/target/classes/:$ZOOKEEPER_HOME/*:\ $ZOOKEEPER-HOME/lib/*：$ZOOKEEPER_HOME/conf

% java CreateGroup localhost zoo

Created /zoo

###### 21.2.3加入组

这个应用的下一部分是一段用于注册组成员的程序。每个组成员将作为一个程序 运行，并且加入到组中。当程序退出时，这个组成员应当从组中被删除。为了实 现这一点，我们在 ZooKeeper 的命名空间中使用短暂 znode 来代表一个组成员。

范例 21-2中的程序 JoinGroup 实现了这个想法。在基类 ConnectionWatcher 中， 对创建和连接 ZooKeeper 实例的程序逻辑进行了重构，如范例 21-3所示。

范例 21-2.该程序将成员加入组

public class DoinGroup extends ConnectionWatcher {

public void join(String groupName, String memberName) throws KeeperException, InterruptedException {

String path =    + groupName + ’•/" + memberName;

String createdPath = zk.create(path, null/*data*/, Ids.OPEN_ACL_UNSAFE, CreateMode.EPHEMERAL);

System.out.printIn("Created H + createdPath);

}

public static void main(String[] args) throws Exception { DoinGroup joinGroup = new 3oinGroup(); joinGroup.connect(args[0]);

joinGroup.join(args[1], args[2]);

// stay alive until process is killed or thread is interrupted Thread.sleep(Long.MAX_VALUE);

} 一

}

范例 21-3.该辅助类等待与 ZooKeeper 建立连接 public class ConnectionWatcher implements Watcher {

private static final int SESSION_TIMEOUT = 5000;

protected ZooKeeper zk;

private CountDownLatch connectedSignal = new CountDownLatch(l);

public void connect(String hosts) throws IOException, InterruptedException { zk = new ZooKeeper(hosts> SESSION_TIMEOUT, this); connectedSignal.await();

}

^Override

public void process(WatchedEvent event) { if (event.getState() == KeeperState.SyncConnected) { connectedSignal.countDown();

}

}

public void close() throws InterruptedException { zk.close();

}

}

DoinGroup的代码与 CreateGnoup 的非常相似。在它的 join()方法中，创建短 暂 znode 作为组 znode 的子节点，然后通过休眠来模拟正在做某种工作，直到该 进程被强行终止。接着，你会看到随着进程终止，这个短暂 znode 被 ZooKeeper 删除。

###### 21.2.4列出组成员

现在，我们需要一段程序来奄看组成员(参见范例 21-4)。

范例 21-4.用于列出组成员的程序 public class ListGroup extends ConnectionWatcher {

public void list(String groupMame) throws KeeperException, InterruptedException { String path =    + groupName;

try {

List<String> children = zk.getChildren(path^ false); if (children.isEmpty()) {

System.out.printf(*'No members in group %s\n’、groupName); Systerruexit(l);

}

for (String child : children) {

System•out.println(chiId);

}

} catch (KeeperException.NoNodeException e) { System.out.printf("Group %s does not exist\n", groupName); System.exit(l);

}

}

public static void main(String[] args) throws Exception { ListGroup listGroup = new ListGroup(); listGroup.connect(args[0]);

listGroup.list(args[1]); listGroup.close();

}

} 在 list()方法中，我们调用了 getChildren()方法来检索并打印输出一个 znode 的子节点列表，调用参数为该 znode 的路径和观察标志。如果在一个 znode 上设 置了观察标志，那么一且该 znode 的状态改变，关联的观察(Watcher)会被触发。 虽然在这里我们没有使用观察，但在查看一个 znode 的子节点时，通过设置观察 可以让应用程序接收到组成员加入、退出和组被删除的有关通知。

在这段程序中，我们捕捉了 KeeperException.NoNodeException异常，代表组 的 znode 不存在时，这个异常就会被抛出。

让我们看看 ListGroup 程序是如何工作的。起初，由于我们还没有在组中添加任 何成员，因此 zoo 组是空的：

% java ListGroup localhost zoo

No members in group zoo

我们可以使用］oinGroup来向组中添加成员。由于这些作为组成员的 znode 不会 自己终止(因为 sleep 语句)，所以我们以后台进程的方式来启动它们：

% java JoinGroup localhost zoo duck &

% java JoinGroup localhost zoo cow &

% java JoinGroup localhost zoo goat &

% goat_pid=$!

最后一行命令保存了将 goat 添加到组中的 Java 进程的 ID。我们需要保存这个进 程 ID，以便能够在查看组成员之后杀死该进程：

% java ListGroup localhost zoo

goat

duck

cow

为了从组中删除一个成员，我们杀死了 goat所对应的进程:

% kill $goat一 pid

几秒钟之后，由于该进程的 ZooKeeper 会话已经结束（超时设置为 5 秒），并且所对 应的短暂 znode 也已经被删除，所以 goat 会从组成员列表中消失。

% java ListGroup localhost zoo

duck

cow

让我们回顾一下，看看已经实现了哪些功能。对于参与到一个分布式系统中的节 点，我们已经有了一个建立节点列表的方法。这些节点相互之间并不了解。例 如，一个想使用列表中节点来完成某些工作的客户端，能够在这些节点不知情的 情况下发现它们。

最后要注意的是组成员关系管理并不能解决与节点通信过程中出现的网络问题。 在与一个组中的成员节点进行通信的过程中可能会出现故障，这些故障必须以一 种合适的方式来解决（重试、使用组中另外一个成员等）。

ZooKeeper命令行工

ZooKeeper提供了一个用于与其命名空间进行交互的命令行工具。我们可以使用这 个工具列出之下的 znode 列表，如下所示：

% zkCli.sh -server localhost Is /zoo [cowduck]

不使用任何参数直接运行这个命令行工具，可以显示该工具的使用帮助。

###### 21.2.5删除组

为了使这个例子比较完整，让我们来看看如何删除一个组。ZooKeeper类提供了 一个 delete（）方法，该方法有两个参数：节点路径和版本号。如果所提供的版本 号与 znode 的版本号一致，ZooKeeper会删除这个 znode。这是一种乐观的加锁机 制，使客户端能够检测出对 znode 的修改冲突。通过将版本号设置为-1，可以绕过 这个版本检测机制，不管 znode 的版本号是什么而直接将其删除。

ZooKeeper不支持递归的删除操作，因此在删除父节点之前必须先删除子节点。在 范例 21-5中，DeleteGroup类用于删除一个组及其所有成员。

范例 21-5.用于删除一个组及其所有成员的程序

public class DeleteGroup extends ConnectionWatcher {

public void delete(String groupName) throws KeeperException, InterruptedException {

String path =    + groupName;

try {

Vist<Strir\g> children = zk. getChildren^path, false);

for (String child : children) {

zk• delete(path + "/•• + child, -1);

}

zk.delete(path, -1);

} catch (KeeperException.NoNodeException e) {

System.out.printf(HGroup %s does not exist\n.、 groupName); System.exit(l);

}

}

public static void main(String[] args) throws Exception { DeleteGroup deleteGroup = new DeleteGroup(); deleteGroup.connect(args[0]);

deleteGroup.delete(args[l]); deleteGroup.close();

}

}

最后，我们可以删除之前所创建的 Z。。组：

% java DeleteGroup localhost zoo % java ListGroup localhost zoo

Group zoo does not exist

##### 21.3 ZooKeeper 服务

ZooKeeper是一个具有高可用性的高性能协调服务。在本小节中，我们将从三个方 面来了解这个服务：模型、操作和实现。

###### 21.3.1数据模型

ZooKeeper维护着一个树形层次结构，树中的节点被称为 znode。znode可以用于 存储数据，并且有一个与之相关联的 ACL。ZooKeeper被设计用来实现协调服务 （这类服务通常使用小数据文件），而不是用于大容量数据存储，因此一个 zrwde 能 存储的数据被限制在 1 MB以内。

ZooKeepei■的数据访问具有原子性。客户端在读取一个 znode 的数据时，要么读到 所有的数据，要么读操作失败，不会只读到部分数据。同样，一个写操作将替换

znode存储的所有数据。ZooKeeper会保证写操作不成功就失败，不会出现部分写 之类的情况，也就是不会出现只保存客户端所写部分数据的情况。ZooKeeper不支 持添加操作。这些特征都是与 HDFS 所不同的。HDFS被设计用于大容量数据存 储，支持流式数据访问和添加操作。

znode通过路径被引用。像 Unix 中的文件系统路径一样，在 ZooKeeper 中路径被 表示成用斜杠分隔的 Unicode 字符串。与 Unix 中的文件系统路径不同的是， ZooKeeper中的路径必须是绝对路径，也就是说每条路径必须从一个斜杠字符开 始。此外，所有的路径表示必须是规范的，即每条路径只有唯一的一种表示方 式，不支持路径解析。例如，在 Unix 中，一个具有路径/a/6的文件也可以通过路 径/a/./Z）来表示，原因在于在 Unix 的路径中表示当前目录（表示当前目 录的上一级目录）。在 ZooKeeper 中，不具有这种特殊含义，这样表示的路径 名是不合法的。

在 ZooKeeper 中，路径由 Unicode 字符串构成，并且有一些限制（参见 ZooKeeper 的参考文档）。字符串“zookeeper”是一个保留词，不能将它作为路径表示中的一 部分。需要特别指出的是，ZooKeeper使用/zwtoper子树来保存管理信息，例如 关于配额的信息。

注意，ZooKeeper的路径与 URI 不同，前者在 Java API中通过 java.lang.String 来使用，而后者是通过 Hadoop Path类（或 java.net.URI类）来使用。

znode有一些性质非常适合用于构建分布式应用，我们将在接下来的几个小节中进 行讨论。

1.短暂 znode

znode有两种类型：短暂的和持久的。znode的类型在创建时被确定并且之后不能 再修改。在创建短暂 znode 的客户端会话结束时，ZooKeepei•会将该短暂 znode 删 除。相比之下，持久 znode 不依赖于客户端会话，只有当客户端（不一定是创建它 的那个客户端）明确要删除该持久 znode 时才会被删除。短暂 znode 不可以有子节 点，即使是短暂子节点。

虽然每个短暂 znode 都会被绑定到一个客户端会话，但它们对所有的客户端还是 可见的（当然，还是要符合其 ACL 的定义）。

对于那些需要知道特定时刻有哪些分布式资源可用的应用来说，使用短暂 znode

是一种理想的选择。本章前面的例子就使用了短暂 znode 来实现一个组成员管理 服务，让任何进程都知道在特定的时刻有哪些组成员可用。

2.顺序号

顺序（sequential）znode是指名称中包含 ZooKeeper 指定顺序号的 znode。如果在创 建 znode 时设置了顺序标识，那么该 znode 名称之后便会附加一个值，这个值是 由一个单调递增的计数器（由父节点维护）所添加的。

例如，如果一个客户端请求创建一个名为/a/、的顺序 ziwde，则所创建 znode 的名 字可能是/a/&人®如果稍后，另外一个名为心/卜的顺序 znode 被创建，计数器会给 出一个更大的值来保证 znode 名称的唯一性，例如，/泰、在 Java 的 API 中，顺 序 znode 的实际路径会作为 create（）调用的返回值被传回客户端。

在一个分布式系统中，顺序号可以被用于为所有的事件进行全局排序，这样客户 端就可以通过顺序号来推断事件的顺序。21.4.3节介绍了如何使用顺序 znode 来实 现共享锁。

3.观察

znode以某种方式发生变化时，“观察”（watch）机制可以让客户端得到通知。可以 针对 ZooKeeper 服务的操作来设置观察，该服务的其他操作可以触发观察。例 如，客户端可以对一个 znode 调用 exists 操作，同时设定一个观察。如果这个 znode不存在，则客户端所调用的 exists 操作将返回 false。如果一段时间之 后，另外一个客户端创建了这个 znode，则这个观察会被触发，通知前一个客户端 这个 znode 被创建。在下一小节中，将完整介绍哪些操作会触发其他操作。

观察只能够触发一次\为了能够多次收到通知，客户端需要重新注册所需的观 察。在前面的例子中，如果客户端希望收到更多 znode 是否存在的通知（例如在这个 znode被删除时也能收到通知），则需要再次调用 exists 操作，设定一个新的 观察。

①    习惯上（并非必需）在顺序 znode 的路径名后会跟一个连字符，使其顺序号更易读并且易于（被 应用程序）解析。

②    对连接事件的回调除外，这种观察不需要重新注册。

在 21.4.1节中，将有一个例子来演示如何使用观察来更新集群的配置。

###### 21.3.2操作

如表 21-2所示，ZooKeeper中有 9 种基本操作。

表 21-2. ZooKeeper服务的操作

| 操作 create       | 描述.■    \    ..w•‘创建一个 znode（必须要有父节点） |
| ---------------- | --------------------------------------------------- |
| delete           | 删除一个 znode（该 znode 不能有任何子节点）            |
| exists           | 测试一个 znode 是否存在并且査询它的元数据             |
| getACL, set ACL  | 获取/设置一个 znode 的 ACL                             |
| getChildren      | 获取一个 znode 的子节点列表                           |
| getData, setData | 获取/设置一个 znode 所保存的数据                      |
| sync             | 将客户端的 znode 视图与 ZooKeeper 同步                  |

ZooKeeper中的更新操作是有条件的。在使用 delete 或 setData 操作时必须提 供被更新 zrwde 的版本号（可以通过 exists 操作获得）。如果版本号不匹配，则更 新操作会失败。更新操作是非阻塞操作，因此一个更新失败的客户端（由于其他进 程同时在更新同一个 znode）可以决定是否重试，或执行其他操作，并不会因此而 阻塞其他进程的执行。

虽然 ZooKeeper 可以被看作是一个文件系统，但出于简单性的需要，有一些文件 系统的基本操作被它摒弃了。由于 ZooKeeper 中的文件较小并且总是被整体读/ 写，因此没有必要提供打开、关闭或查找操作。

![img](Hadoop43010757_2cdb48_2d8748-260.jpg)



sync操作与 POSIX 文件系統中的 fsync（）操作是不同的。如前所述， ZooKeeper中的写操作具有原子性，一个成功的写操作会保证将数据写到 ZooKeeper服务器的持久存储介质中。然而，ZooKeeper允许客户端读到的数 据滞后于 ZooKeeper 服务的最新状态，因此客户端可以使用 sync 操作来获取 数据的最新状态。相关详情请参见 21.3.4节。

1.集合更新

ZooKeeper中有一个被称为 multi 的操作（Multiupdate），用于将多个基本操作集合 成一个操作单元，并确保这些基本操作同时被成功执行，或者同时失败，不会发 生其中部分基本操作被成功执行而其他基本操作失败的情况。

集合更新可以被用于在 ZooKeeper 中构建需要保持全局一致性的数据结构，例如 构建一个无向图。在 ZooKeeper 中用一个 znode 来表示无向图中的一个顶点，为 了在两个顶点之间添加或删除一条边，我们需要同时更新两个顶点所分别对应的 两个 znode，因为每个 znode 中都有指向对方的引用。如果我们只用 ZooKeeper 的 基本操作来实现边的更新，可能会让其他客户端发现无向图处于不一致的状态， 即一个顶点具有指向另一个顶点的引用而对方却没有对应的引用。将针对两个 znode的更新操作集合到一个 multi 操作中可以保证这组更新操作的原子性，也 就保证了一对顶点之间不会出现不完整的连接。

2.关于 API

对于 ZooKeeper 客户端来说，主要有两种语言绑定(binding)可以使用：Java和 C; 当然也可以使用 Perl、python和 REST 的 contrib 绑定。对于每一种绑定语言来

说，在执行操作时都可以选择同步执行或异步执行。我们已经看过同步执行的 Java API。下面是 exists 操作的签名，它返回一个封装有 znode 元数据的 Stat 对象(如果 znode 不存在，则返回 null):

public Stat exists(String path, Watcher watcher) throws KeeperException, InterruptedException

在 ZooKeeper 类中同样可以找到异步执行的签名，如下所示：

public void exists(String path. Watcher watcher^ StatCallback cb. Object ctx)

因为所有异步操作的结果都是通过回调来传送的，因此在 Java API中异步方法的 返回类型都是 void。调用者传递一个回调的实现，当 ZooKeeper 响应时，该回调 方法被调用。在这种情况下，回调采用 StatCallback 接口，它有以下方法：

public void processResult(int re. String path^ Object ctx, Stat stat);

其中 rc 参数是返回代码，对应于 KeeperException 的代码。每个非零代码都代 表一个异常，在这种情况下，stat参数是 null。path和 ctx 参数对应于客户端 传递给 exists()方法的参数，用于识别这个回调所响应的请求。ctx参数可以是 任意对象，当 path 参数不能提供足够的信息时，客户端可以通过 ctx 参数来区 分不同请求。如果 path 参数提供了足够的信息，可以将 ctx 参数设成 null。

实际上，有两个 C 语言的共享库。单线程库 zookeeper_st只支持异步 API，并 且主要在没有 pthread 库或 pthread 库不稳定的平台上使用。大部分开发人员都 使用多线程库 zookeepen_mt，它既支持同步 API 也支持异步 API。要想进一步 了解如何构建和使用 C 语言 API，请参考 ZooKeeper 安装目录下 5rc/c子目录中的 README 文件。

4    '■    二盧 Titf •• Cl 蜷：父 M. 5    A ” <?•    «1 C*' 4.广 t    » V :•.    'u ' 'ij    •• ••    ,d麈 », J I • v.    *• .. ••    f 4 <**'    1    ••* •< * "^iMrC *    T I iw#. : • ，• A Ijy \    ,. • •，。嘯 ky 2W    ，i ,Y n VJ^ i/f) '7i£C a    if,,

我该使用同步 API 还是异步 API?

两种类型的 AH 提供相同的功能，因此选择哪一种只是风格问题。例如，如果 你习惯于事件驱动的编程模型，则异步 API 更合适一些。

• • 了 二 X*:、'•    •    . y    .». '•/V,, J •' . , »i | *，、,,?. .Tjh '    ••    **•*'•» .*•/» .,V    A • •»$• A *. <T* , .*>•. i    r '乂‘飞:••’ " , , ' .    • • ’，•    攻’ .*•* V* , . • -    «' •々• .. 'u . ft' ' 9T,-    '    '    、，    ;    “    i*    ' '•• *    - '    r£*|<    » ■> ?    •    ' A,    ' *

异步 AH 允许你以流水线方式处理请求，这在某些情况下可以提供更好的呑吐 量。想象一下，你打算读取一大批 znode 并且分别对它们进行处理。如果使用 同步 API，每一个读操作都会阻塞进程，直到该读操作返回；但如果使用异步 API，你可以非常快速地启动所有的异步读操作并在另外一个单独的线程中来 处理读操作的返回。

3.观察触发器

在 exists、getChildren和 getData 这些读操作上可以设置观察，这些观察可 以被写操作 create、delete和 setData 触发。ACL相关的操作不参与触发任何 观察。当一个观察被触发时会产生一个观察事件，这个观察和触发它的操作共同 决定着观察事件的类型。

当所观察的 znode 被创建、删除或其数据被更新时，设置在 exists 操 作上的观察将被触发。

•当所观察的 znode 被删除或其数据被更新时，设置在 getData 操作上的 观察将被触发。创建 znode 不会触发 getData 操作上的观察，因为 getData操作成功执行的前提是 znode 必须已经存在。

所观察的 znode 的一个子节点被创建或删除时，或所观察的 znode 自己 被删除时，设置在 getChildren 操作上的观察将会被触发。可以通过 观察事件的类型来判断被删除的是 znode 还是其子节点：NodeDelete类 型代表 znode 被删除；NodeChildrenChanged类型代表一个子节点被 删除。

表 21-3列出了观察及其触发操作所对应的事件类型。

表 21-3.观察及其触发操作所对®的事件类型

观察触发器

设置观察的操作







创建 znode    创建子节点

exists    NodeCreated

鵬 znode 鵬子节点 S6tData

NodeDeleted

NodeData

Changed

| getData     |                     | NodeDeleted |                     | NodeDataChanged |
| ----------- | ------------------- | ----------- | ------------------- | --------------- |
| getChildren | NodeChildrenChanged | NodeDeleted | NodeChildrenChanged |                 |



一个观察事件中包含涉及该事件的 znode 的路径，因此对于 NodeCreated 和 NodeDeleted事件来说，可通过路径来判断哪一个节点被创建或删除。为了能够 在 NodeChildrenChanged 事件发生之后判断是哪些子节点被修改，需要重新调 用 getChildren 来获取新的子节点列表。与之类似，为了能够在 NodeDataChanged事件之后获取新的数据，需要调用 getData。在这两种情况 下，从收到观察事件到执行读操作(getChildren或 getData)期间，znode的状态 可能会发生改变，在写程序的时候必须牢记这一点。

4.ACL列表

每个 znode 创建时都会带有一个 ACL 列表，用于决定谁可以对它执行何种操作。

ACL依赖于 ZooKeeper 的客户端身份验证机制。ZooKeeper提供了以下几种身份 验证方式：

•    digest通过用户名和密码来识别客户端

•    sasl通过 Kerberos 来识别客户端

•    ip通过客户端的 IP 地址来识别客户端

在建立一个 ZooKeeper 会话之后，客户端可以对自己进行身份验证。虽然 znode 的 ACL 列表会要求所有的客户端是经过验证的，但 ZooKeeper 的身份验证过程却 是可选的，客户端必须自己进行身份验证来支持对 znode 的访问。这里有一个使 用 digest 方式(用户名和密码)进行身份验证的例子：

zk•addAuthlnfo("digest", "tom:secret".getBytes());

每个 ACL 都是身份验证方式、符合该方式的一个身份和一组权限的组合。例如， 如果我们打算给 IP 地址为 10.0.0.1的客户端对某个 znode 的读权限，可以使用

ip验证方式、10.0.0.1和 READ 权限在该 znode 上设置一个 ACL。在 Java 语言 中，我们可以如下所示来创建这个 ACL 对象：

new ACL(Perms.READ,

new Id("ip", "10.0.0.1"));

表 21-4列出了一个完整的权限集合。注意，exists操作并不受 ACL 权限的限 制，因此任何客户端都可以凋用 exists 来检索一个 znode 的状态或查询一个 znode是否存在。

表 21-4. ACL权限

ACL权限    允许的操作

CREATE    create(子节点)

| READ    | getChildren      |
| ------- | ---------------- |
| getData |                  |
| WRITE   | setData          |
| DELETE  | delete（子节点） |
| ADMIN   | setACL           |

在类 ZooDefs.Ids中有一些预定义的 ACL，OPEN_ACL_UNSAFE是其中之一， 它将所有的权限(不包括 ADMIN 权限)授予每个人。

此外，ZooKeeper■还支持插入式身份验证机制，如果需要的话，它可以集成第三方 的身份验证系统。

###### 21.3.3实现

ZooKeeper服务有两种不同的运行模式。一种是独立模式(standalone mode)，即只 有一个 ZooKeeper 服务器。这种模式较为简单，比较适合干测试环境(甚至可以在 单元测试中采用)，但是不能保证高可用性和可恢复性。在生产环境中的 ZooKeeper通常以复制模式(replicated mode)运行于一个计算机集群上，这个计算 机集群被称为一个集合体(ensemble)。ZooKeeper通过复制来实现高可用性，只要 集合体中半数以上的机器处于可用状态，它就能够提供服务。例如，在一个有 5 个节点的集合体中，任意 2 台机器出现故障，都可以保证服务继续，因为剩下的 3 台机器超过了半数。注意，6个节点的集合体也只能够容忍 2 台机器出现故障，因 为如果 3 台机器出现故障，剩下的 3 台机器没有超过集合体的半数。出于这个原 ，一个集合体通常包含奇数台机器。

从概念上来说，ZooKeeper是非常简单的：它所做的就是确保对 znode 树的每一个 修改都会被复制到集合体中超过半数的机器上。如果少于半数的机器出现故障， 则最少有一台机器会保存最新的状态，其余的副本最终也会更新到这个状态。

然而，这个简单想法的实现却不简单。ZooKeeper使用了 Zab协议，该协议包括 两个可以无限重复的阶段。

1.阶段 1:领导者选举

集合体中的所有机器通过一个选择过程来选出一台被称为领导者(leader)的机器， 其他的机器被称为跟随者(follower)。一旦半数以上(或指定数量)的跟随者已经将 其状态与领导者同步，则表明这个阶段已经完成。

2.阶段 2:原子广播

所有的写请求都会被转发给领导者，再由领导者将更新广播给跟随者。当半数以 上的跟随者已经将修改持久化之后，领导者才会提交这个更新，然后客户端才会 收到一个更新成功的响应。这个用来达成共识的协议被设计成具有原子性，因此 每个修改要么成功要么失败。这类似于数据库中的两阶段提交协议。

ZooKeeper 是否使用 Paxos?

?•••么'◊••• ’•    ••• '.•••，•    • •

否。ZooKeeper的 Zab 协议不同于众所周知的 Paxos 算法®。虽然有些类似，但 是 Zab 在操作方面是不同的，例如它依靠 TCP 来保证其消息的顺序。②

Google的 Chubby 锁服务®是基于 Paxos 的，其功能与 ZooKeeper 的功能

类似。

如果领导者出现故障，其余的机器会选出另外一个领导者，并和新的领导者一起 继续提供服务。随后，如果之前的领导者恢复正常，会成为一个跟随者。领导者 选举的过程是非常快的，根据一个已公布的结果(top.•///?//.‘⑽来

①    参见图灵奖得主 Leslie Lamport的文章，标题为“Paxos Made Simple”，发表于 2001 年 12 月 的 ACM SIGACT News，网址为 <http://bit.ly/simple-paxos0>

②    有关 Zab 的描述可以参见由 Benjamin Reed和 Flavio Junqueira的文章，标题为“A simple totally ordered broadcast protocol”，发布于 2008 年大规模分布式系统和中间件工作坊，

LADIS ’08 Proceedings of the 2nd Workshop，[http://bit.ly/ordered_protocol](http://bit.ly/ordered_protocol%e3%80%82)[。](http://bit.ly/ordered_protocol%e3%80%82)

③参见 Mike Burrows 的文章，标题为 “The Chubby Lock Service for Loosely-Coupled Distributed

Systems”，发布 f 2000 年 11 月，网址为 <http://research.google.com/archive/chubby.html0>

看，只需要大约 200 毫秒，因此在领导者选举的过程中不会出现系统性能的明显 降低。

在更新内存中的 znode 树之前，集合体中的所有机器都会先将更新写入磁盘。任 何一台机器都可以为读请求提供服务，并且由于读请求只涉及内存检索，因此非 常快。

###### 21.3.4 —致性

理解 ZooKeeper 的实现有助于理解其服务所提供的一致性保证。在集合体中所使 用的术语“领导者”和“跟随者”是恰当的，它们表明一个跟随者可能滞后干领 导者几个更新。这也表明在一个修改被提交之前，只需要集合体中半数以上机器 已经将该修改持久化即可。对 ZooKeeper 来说，理想的情况就是将客户端都连接 到与领导者状态一致的服务器上。每个客户端都有可能被连接到领导者，但客户 端对此无法控制，甚至它自己都无法知道是否连接到领导者，®参见图 21-2。

ZooKeeper service

follower    leader    follower

Client    Client Client    Client Client Client



![img](Hadoop43010757_2cdb48_2d8748-262.jpg)



21-2.跟随者负责响应读请求，领导者负责提交写请求

①可以对 ZooKeeper 进行配置，使领导者不接受任何客户端连接。在这种情况下，领导者的唯 一任务就是协调更新。可以通过将 leaderServes 属性设置为 no 来实现这一点。推荐在超过 3台服务器的集群中使用该设置。

毎一个对 znode 树的更新都被赋予一个全局唯一的 ID，称为 zx/冰代表

“ZooKeeper Transaction ID”）。ZooKeeper要求对所有的更新进行编号并排序， 它决定了分布式系统的执行顺序，如果小干 Z2，则 Zi—定发生在 Z2 之前。

在 ZooKeeper 的设计中，以下几点考虑保证了数据的一致性

1.顺序一致性

来自任意特定客户端的更新都会按其发送顺序被提交。也就是说，如果一个客户 端将 znode z的值更新为 a，在之后的操作中，它又将 z 的值更新为/?，则没有客 户端能够在看到 z 的值是 6 之后再看到值〃（如果没有其他对 z 的更新）。

2.原子性

每个更新要么成功，要么失败。这意味着如果一个更新失败，则不会有客户端看 到这个更新的结果。

3.单一系统映像

一个客户端无论连接到哪一台服务器，它看到的都是同样的系统视图。这意味 着，如果一个客户端在同一个会话中连接到一台新的服务器，它所看到的系统状 态不会比在之前服务器上所看到的更老。当一台服务器出现故障，导致它的一个 客户端需要尝试连接集合体中其他的服务器时，所有状态滞后于故障服务器的服 务器都不会接受该连接请求，除非这些服务器将状态更新至故障服务器的水平。

4.持久性

一个更新一旦成功，其结果就会持久存在并且不会被撤销。这表明更新不会受到 服务器故障的影响。

5.及时性

任何客户端所看到的滞后系统视图都是有限的，不会超过几十秒。这意味着与其 允许一个客户端看到非常陈旧的数据，还不如将服务器关闭，强迫该客户端连接 到一个状态较新的服务器。

出于性能的原因，所有的读操作都是从 ZooKeeper 服务器的内存获得数据，它们 不参与写操作的全局排序。如果客户端之间通过 ZooKeeper 之外的机制进行通 信，则客户端可能会发现它们所看到的 ZooKeeper 状态是不一致的。例如，客户

端 A 将 znode z的值从“更新为 6Z’，接着 A 告诉 B 去读 z 的值，而 B 读到的值是 a而不是。这与 ZooKeeper 的一致性保证是完全兼容的（这种情况称为“跨客户 端视图的同时一致性”）。为了避免这种情况发生，B应该在读 z 的值之前对 z 调 用 sync 操作。sync操作会强制 B 所连接的 ZooKeeper 服务器“赶上”领导者， 这样当 B 读 z 的值时，所读到的将会是 A 所更新的（或后来更新的）。

![img](Hadoop43010757_2cdb48_2d8748-263.jpg)



容易让人疑惑的是，sync操作只能以异步的方式被调用。你不需要等待 sync 调用的返回，ZooKeeper会保证任何后续的操作都在服务器的 sync 操作完成后 才执行，哪怕这些操作是在 sync 操作完成之前发出的。

###### 21.3.5会话

每个 ZooKeeper 客户端的配置中都包括集合体中服务器的列表。在启动时，客户 端会尝试连接到列表中的一台服务器。如果连接失败，它会尝试连接另一台服务 器，以此类推，直到成功与一台服务器建立连接或因为所有 ZooKeeper 服务器都 不可用而失败。

一旦客户端与一台 ZooKeeper 服务器建立连接，这台服务器就会为该客户端创建 一个新的会话。每个会话都会有一个超时的时间设置，这个设置由创建会话的应 用来设定。如果服务器在超时时间段内没有收到任何请求，则相应的会话会过 期。一旦一个会话已经过期，就无法重新被打开，并且任何与该会话相关联的短暂 znode都会丢失。会话通常都会长期存在，而会话过期则是一种比较罕见的事件， 但对干应用来说，如何处理会话过期仍是非常重要的，详情可以参见 21.4.2节。

只要一个会话空闲超过一定时间，都可以通过客户端发送 ping 请求（也称为心跳） 来保持会话不过期。（ping请求是由 ZooKeeper 的客户端库自动发送，因此在你的 代码中不需要考虑如何维护会话。）这个时间长度的设置应当足够低，以便能够检 测出服务器故障（由读超时体现），并且能够在会话超时的时间段内重新连接到另外 一台服务器。

ZooKeeper客户端可以自动地进行故障切换，切换至另一台 ZooKeeper 服务器，并

且关键的是，在另一台服务器接替故障服务器之后，所有的会话（和相关的短暂 znode）仍然是有效的。

在故障切换过程中，应用程序将收到断开连接和连接至服务的通知。当客户端断 开连接时，观察通知将无法发送；但是当客户端成功恢复连接后，这些延迟的通 知还会被发送。当然，在客户端重新连接至另一台服务器的过程中，如果应用程 序试图执行一个操作，这个操作将会失败。这充分说明在真实的 ZooKeeper 应用 中处理连接丢失异常的重要性，详倩可以参见 21.4.2节。

时间

在 ZooKeeper 中有几个时间参数。“滴答” （tick time）参数定义了 ZooKeeper中的

基本时间周期，并被集合体中的服务器用来定义相互交互的时间表。其他设置都 是根据滴答参数来定义的，或至少受它限制。例如，会话超时（session timeout）参 数的值不可以小于 2 个滴答并且不可以大于 20 个滴答。如果你试图将会话超时参

数设置在这个范围之外，它将会被自动修改到这个范围之内。

«

通常将滴答参数设置为 2 秒（2000毫秒），对应于允许的会话超时范围是 4 到 40 秒。在选择会话超时设置时有几点需要考虑。

较短的会话超时设置会较快地检测到机器故障。在组成员管理的例子中，会话超 时的时间就是用来将故障机器从组中删除的时间。但要避免将会话超时时间设得 太低，因为繁忙的网络会导致数据包传输延迟，从而可能会无意中导致会话过 期。在这种情况下，机器可能会出现“振动”（flap）现象：在很短的时间内反复出 现离开后又重新加入组的情况。

对于那些创建较复杂暂时状态的应用程序来说，由于重建的代价较大，因此比较 适合设置较长的会话超时。在某些情况下，可以对应用程序进行设计，使它能够 在会话超时之前重启，从 ffii 避免出现会话过期的情况（这适合于对应用进行维护或 升级）。服务器会为每个会话分配一个唯一的 ID 和密码，如果在建立连接的过程 中将它们传递给 ZooKeeper，可以用于恢复一个会话（只要该会话没有过期）。将会 话 ID 和密码保存在稳定存储器中之后，可以将一个应用程序正常关闭，然后在重 启应用之前凭借所保存的会话 ID 和密码来恢复会话环境。

你可以将这个特征看成是一种用来帮助避免会话过期的优化技术，但不能因此忽 略对会话过期异常的处理，因为机器的意外故障也会导致会话过期，或者，即使 应用程序是正常关闭，也有可能因任何原因而导致它没有在会话未过期之前完成 重启。

一般的规则是，ZooKeeper集合体中的服务器越多，会话超时的设置应越大。连接 超时、读超时和 ping 周期都被定义为集合体中服务器数量的函数，因此集合体中 服务器数量越多，这些参数的值反而越小。如果频繁遇到连接丢失的情况，应考 虑增大超时的设置。可以使用 JMX 来监控 ZooKeeper 的度量指标，例如请求延迟 的统计信息。

###### 21.3.6状态

ZooKeeper对象在其生命周期中会经历几种不同的状态(参见图 21-3)。你可以在 任何时刻通过 getState()方法来查询对象的状态：

public States getState()

States被定义成代表 ZooKeeper 对象不同状态的枚举类型值(不管是什么枚举 值，一个 ZooKeeper 的实例在一个时刻只能处于一种状态)。在试图与 ZooKeeper 服务建立连接的过程中，一个新建的 ZooKeeper 实例处于 CONNECTING 状态。一 旦建立连接，它就会进入 CONNECTED 状态。

new ZooKeeperO

Watcher.EventKeeperState

SyncConnected



Watcher.Event.KeeperState

Disconnected

ZooKeeper.doseO



Watcher.Event.KeeperState

Expired



![img](Hadoop43010757_2cdb48_2d8748-265.jpg)



Alive Not alive



![img](Hadoop43010757_2cdb48_2d8748-266.jpg)



21-3. ZooKeeper 状态转换

![img](Hadoop43010757_2cdb48_2d8748-267.jpg)



通过注册观察对象，使用了 ZooKeeper对象的客户端就可以收到状态转换通知。 一旦进入 CONNECTED 状态，观察对象就会收到一个 WatchedEvent 通知，其中 KeeperState 白勺值是 SyncConnected。

ZooKeeper的 watcher 对象肩负着双重责任：一方面它可以被用于获得 ZooKeeper状态变化的相关通知（如本节所述）；另一方面还可以被用于获得 znode变化的相关通知（参见 21.3.2节对观察触发器的讨论）。传递给

\+ ZooKeeper对象构造函数的（默认的）观察被用于监视其状态的变化。监视 znode的变化可以使用一个专用的观察对象（将其传递给适当的读操作），也可以 通过读操作中的布尔标识来设定是否共享使用默认的观察。

ZooKeeper实例可以断开然后重新连接到 ZooKeeper 服务，此时它的状态就在 CONNECTED和 CONNECTING 之间转换。如果它断开连接，观察会收到一个 Disconnected事件。注意，这些状态转换都是由 ZooKeeper 实例自己发起的， 如果连接丢失，它会自动尝试重新连接。

如果 close（）方法被调用或出现会话超时（观察事件的 KeeperState 值为 Expired财，ZooKeeper实例会转换到第三个状态 CLOSED。一旦处于 CLOSED 状 态，ZooKeeper对象不再被认为是活跃的（可以对 States 使用 isAlive（）方法来 测试），并且不能再用。为了重新连接到 ZooKeeper 服务，客户端必须创建一个新 的 ZooKeeper 实例。

##### 21.4使用 ZooKeeper 来构建应用

在一定程度上了解 ZooKeeper 之后，我们接下来用 ZooKeeper 写一些有用的应用 程序。

###### 21.4.1配置服务

配置服务是分布式应用所需要的基本服务之一，它使集群中的机器可以共享配置 信息中那些公共的部分。简单地说，ZooKeeper可以作为一个具有高可用性的配置 存储器，允许分布式应用的参与者检索和更新配置文件。使用 ZooKeeper 中的观 察机制，可以建立一个活跃的配置服务，使那些感兴趣的客户端能够获得配置信 息修改的通知。

让我们来写一个这样的服务。我们通过两个假设来简化所需实现的服务（稍加修改 就可以取消这两个假设）。第一，我们唯一需要存储的配置数据是字符串，关键字 是 znode 的路径，因此我们在每个 znode 上存储了一个键-值对。第二，在任何时 候只有一个客户端会执行更新操作。除此之外，这个模型看起来就像是有一个主 节点（类似于 HDFS 中的 namenode）在更新信息，而它的工作节点则需要遵循这些 信息。

我们在 ActiveKeyValueStore 类中写了如下代码：

public class ActiveKeyValueStore extends ConnectionWatcher {

private static final Charset CHARSET = Charset.forName(HUTF-8H);

public void write(String path. String value) throws InterruptedException^ KeeperException {

Stat stat = zk.existsCpath., false); if (stat == null) {

zk.create(path, value.getBytes(CHARSET), Ids.OPEN_ACLJJNSAFE, CreateMode.PERSISTENT);

} else {

zk.setData(pathJ value.getBytes(CHARSET), -1);

}

}

}

write()方法的任务是将一个关键字及其值写入 ZooKeepero 它隐藏了创建一个新 的 znode 和用一个新值更新现有 znode 之间的区别，而是使用 exists 操作来检测 znode是否存在，然后再执行相应的操作。其他值得一提的细节是需要将字符串值 转换为字节数组，因为我们只用了 UTF-8编码的 getBytes()方法。

为了说明 ActiveKeyValueStore 的用法，我们写了一个用来更新配置属性值的 类 ConfigUpdatep，如范例 21-6 所示。

范例 21-6.该程序随机更新 ZooKeeper 中配置属性值的程序

public class ConfigUpdater {

public static final String PATH = "/config";

private ActiveKeyValueStore store;

private Random random = new Random();

public ConfigUpdater(St ring hosts) throws IOException^ InterruptedException { store = new ActiveKeyValueStore(); store.connect(hosts);

}

public void run() throws InterruptedException^ KeeperException { while (true) {

String value = random.nextlnt(100) + store.write(PATH^ value);

System.out.printf(HSet %s to %s\n", PATH, value);

T imellnit. SECONDS, sleep (random, nextlnt (10));

} •

}

public static void main(String[] args) throws Exception {

ConfigUpdater configUpdater = new Configllpdater(args[0]) configUpdater.run();

}

这个程序很简单，ConfigUpdater中定义了一个 ActiveKeyValueStore，它在 ConfigUpdater的构造函数中连接到 ZooKeeper。run()方法永远在循环，在随 机时间以随机值更新 znodeo 接下来，让我们看看如何读取配置属性的值。首先，我们在 ActiveKey ValueStore中添加一个读方法：

public String read(String path^ Watcher watcher) throws InterruptedException，KeeperException {

byte[] data = zk.getData(path， watcher， null/*stat*/); return new String(data, CHARSET);

}

ZooKeeper的 getData()方法有三个参数：路径、一个观察对象和一个 Stat 对 象。Stat对象由 getData()方法返回的值填充，用来将信息回传给调用者。通过 这个方法，调用者可以获得一个 znode 的数据和元数据，但在这个例子中，由于 我们对元数据不感兴趣，因此将 Stat 参数设为 null。

作为配置服务的用户，ConfigWatcher(参见范例 21-7)创建了一个 ActiveKeyValueStore对象 store，并且在启动之后调用了 store的 read()方 法(在 displayConfig()方法中)，将自身作为观察传递给 store。 displayConfig()方法用于显示它所读到的配置信息的初始值。

范例 21-7.观察 ZooKeeper 中配置属性的更新情况并将其打印到控制台的应用 public class ConfigWatcher implements Watcher {

private ActiveKeyValueStore store;

public ConfigWatcher(String hosts) throws IOException, InterruptedException { store = new ActiveKeyValueStore(); store.connect(hosts);

}

public void displayConfig() throws InterruptedException, KeeperException { String value = store.read(ConfigUpdater.PATHthis);

System.out.printf(''Read %s as %s\n", ConfigUpdater.PATHvalue);

}

^Override

public void process(WatchedEvent event) { if (event.getType() == EventType.NodeDataChanged) {

try {

displayConfig();

} catch (InterruptedException e) {

System, err. print In ("Interrupted. Exiting.    ;

Thread.currentThread().interrupt();

} catch (KeeperException e) {

System.err•printf(HKeeperException: %s. Exiting•\n", e);

}

}

}

public static void main(String[] args) throws Exception { ConfigWatcher configWatcher = new ConfigWatcher(args[0]); configWatcher.displayConfig()j

// stay aLive until process is kiLLed or thread is interrupted Thread.sleep(Long.MAX—VALUE);

} _

}

632 第 21 章

当 ConfigUpdater更新 znode时，ZooKeeper产生一个类型为 EventType.NodeDataChanged 的事件，从而触发观察。ConfigWatcher 在它的 process()方法中对这个事件做出反应，读取并显示配置的最新版本。

由于观察仅发送单次信号，因此每次我们调用 ActiveKeyValueStore 的 read() 方法时，都将一个新的观察吿知 ZooKeeper，以确保我们可以看到将来的更新。尽 管如此，我们还是不能保证接收到每一个更新，因为在收到观察事件通知与下一 次读之间，znode可能已经被更新过，而且可能是很多次更新，由于客户端在这段 时间没有注册任何观察，因此不会收到通知。对于示例中的配置服务，这不是问 题，因为客户端只关心属性的最新值，最新值优先于之前的值。但在一般情况 下，这个潜在的问题是不容忽视的。

让我们看看如何使用这个程序。在一个终端窗口中运行 ConfigUpdater：

% java ConfigUpdater localhost

Set /config to 79 Set /config to 14 Set /config to 78

然后紧接着在另一个终端窗口启动 ConfigWatcher：

% java ConfigWatcher localhost

Read /config as 79

Read /config as 14

Read /config as 78

###### 21.4.2可复原的 ZooKeeper 应用

关于分布式计算邮）的第一个误区是“网络是可靠的”。按 照他们的观点，程序总是有一个可靠的网络，因此当程序运行在真正的网络中 时，往往会出现各种各样的故障。让我们看看各种可能的故障模式以及能够解决 故障的措施，使我们的程序在面对故障时能够及时复原。

在 Java API中的每一个 ZooKeeper 操作都在其 throws 子句中声明了两种类型的 异常，分别是 InterruptedException 和 KeeperException。

\1. InterruptedException 异常

如果操作被中断，则会有一个 InterruptedException 异常被抛出。在 Java 语言

中有一个取消阻塞方法的标准机制，即针对存在阻塞方法的线程调用 interrupt（） 0 一个成功的取消操作将产生一个 InterruptedException 异常。 ZooKeeper也遵循这一机制，因此你可以使用这种方法来取消一个 ZooKeeper 操 作。使用了 ZooKeeper的类或库通常会传播 InterruptedException 异常，使客 户端能够取消它们的操作。®

InterruptedException异常并不意味着有故障，而是表明相应的操作已经被取 消，所以在配置服务的示例中，可以通过传播异常来中止应用程序的运行。

\2. KeeperException 异常

如果 ZooKeeper 服务器发出一个错误信号或与服务器存在通信问题，抛出的则是 KeeperException异常。针对不同的错误情况，KeeperException异常存在不同 的子类。例如，KeeperException.NoNodeException 是 KeeperException 的一 个子类，如果你试图针对一个不存在的 znode 执行操作，就会抛出这个异常。

每一个 KeeperException 异常的子类都对应一个关干错误类型信息的代码。例如， KeeperException.NoNodeException 异常的代码是 KeeperException.Code.NONODE

（一个枚举值）。

①详情请参阅 Brian Goetz 的优秀文章，标题为 “Java theory and practice: Dealing with InterruptedException”，2006 年 5 月发表于 IBM 开发者网络，网址为 <http://www.ibm.com/> developerworks/java/library/j-jtp05236.html。

有两种方法被用来处理 KeeperException 异常：一种是捕捉 KeeperException 异常并

且通过检测它的代码来决定采取何种补救措施；另一种是捕捉等价的 KeeperException子类并且在每段捕捉代码中执行相应的操作。

KeeperException异常分为三大类。

•状态异常当一个操作因不能被应用于 znode 树而导致失败时，就会出 现状态异常。状态异常产生的原因通常是在同一时间有另外一个进程正 在修改 znode0 例如，如果一个 znode 先被另外一个进程更新了，根据 版本号执行 setData 操作的进程就会失败，并收到一个 KeeperException. BadVersionException异常，这是因为版本号不匹配。程序员通常都 知道这种冲突总是存在的，所以也都会写代码来进行处理。

一些状态异常会指出程序中的错误，例如 KeeperException. NoChildrenFor

EphemeralsException异常，试图在短暂 znode 下创建子节点时就会抛出该 异常。

•可恢复的异常可恢复的异常是指那些应用程序能够在同一个 ZooKeeper 会话中恢复的异常。一个可恢复的异常是通过 KeeperException. ConnectionLossException来表示的，它意味着已经丢失了与 ZooKeeper的连接。ZooKeeper会尝试重新连接，并且在大多数情况下 重新连接会成功，并确保会话是完整的。

但是 ZooKeeper 不能判断与 KeeperException .Connection LossException 异

常相关的操作是否成功执行。这种情况就是部分失败的一个例子(在本章开始时提 到的)。这时程序员有责任来解决这种不确定性，并且根据应用的情况来采取适当 的操作。

在这一点上，就需要对幂等(idempotent)操作和非幂等(Nonidempotent)操作进行区分。幕

等操作是指那些一次或多次执行都会产生相同结果的操作，例如读请求或无条件执 行的 setData 操作。对于幂等操作，只需要简单地进行重试即可。

对于非幂等操作，就不能盲目地进行重试，因为它们多次执行的结果与一次执行 是完全不同的。程序可以通过在 znode 的路径和它的数据中编码信息来检测是否 非幂等操作的更新已经完成。在 21.4.3节对可恢复异常的讨论中，我们将通过实 现一个锁服务来讨论如何处理失败的非幂等操作。

•不可恢复的异常在某些情况下，ZooKeeper会话会失效，也许因为超 时或因为会话被关闭(两种情况下都会收到 KeeperException.

SessionExpiredException 异常)，或因为身份验证失败(KeeperException. AuthFailedException异常)。无论上述哪种情况，所有与会话相关联 的短暂 znode 都将丢失，因此应用程序需要在重新连接到 ZooKeeper 之 前重建它的状态。

3.可靠的配置服务

让我们回到 ActiveKeyValueStore 的 write()方法，它由一个 exists 操作紧 跟着一个 create 操作或 setData 操作组成：

public void write(String path. String value) throws InterruptedException^ KeeperException {

Stat stat = zk.exists(path, false); if (stat == null) {

zk.create(path, value.getBytes(CHARSET)4 Ids.OPEN_ACL_UNSAFE,

CreateMode.PERSISTENT);

} else {

zk.setData(path, value.getBytes(CHARSET), -1);

}

}

作为一个整体，write()方法是一个幂等操作，所以我们可以对它进行无条件重 试。这里有一个 write()方法修改后的版本，能够循环执行重试。

其中设置了重试的最大次数 MAX_RETRIES和两次重试之间的时间间隔 RETRY

PERIOD_SECONDS：

public void write(String path. String value) throws InterruptedException, KeeperException {

int retries = 0; while (true) {

Stat stat = zk.exists(path， false); if (stat == null) {

zk.create(path, value.getBytes(CHARSET), Ids.OPEN」\CL—UNSAFE, CreateMode.PERSISTENT);

} else {

zk.setData(path, value.getBytes(CHARSET)stat.getVersion());

}

return;

} catch (KeeperException•SessionExpiredException e) { throw e;

} catch (KeeperException e) { if (retries++ == MAX—RETRIES) { throw e;

}

// sleep then retry

TimeUnit.SECONDS.sleep(RETRY_PERIOD_SECONDS);

} _

} -

}

这段代码没有在 KeeperException.SessionExpiredException异常处进行重 试，因为当一个会话过期时，ZooKeeper对象会进入 CLOSED 状态，此状态下， 它不能再进行重新连接(参见图 21-3)。我们只是简单地将这个异常重新抛出 $ 并且 让调用者创建一个新的 ZooKeeper 实例，以重试整个 write()方法。一个简单的 创建新实例的方法是创建一个新的 ConfigUpdater(实际上我们已将其改名为 ResilientConfigUpdater)用于恢复过期会话：

public static void main(String[] args) throws Exception { while (true) {

try {

ResilientConfigUpdater configUpdater = new ResilientConfigUpdater(args[0]);

configUpdater.run();

} catch (KeeperException.SessionExpiredException e) { // start a new session

} catch (KeeperException e) {

// aLready retried) so exit e•printStackTrace(); break;

}

}

} •

处理会话过期的另一种方式是在观察中(本例子中应该是 ConnectionWatcher)监 测类型为 Expired 的 KeeperState，然后在监测到的时候创新一个连接。即使收 到 Ke 印 erExc 印 tion.SessionExpiredExc印 tion 异常，但由于连接最终是能够重新 建立的，我们就可以使用这种方式在 write()方法内不断进行重试。不管我们采 用何种机制从过期会话中恢复，重要的是需要对这种不同于连接丢失的故障类型 进行不同的处理。

I    实际上，这里忽略了另一种故障模式。当 ZooKeeper 对象被创建时，它会尝试

连接一个 ZooKeeper 服务器。如果连接失败或超时，那么它会尝试连接集合体

J'靠

①另外一种写代码的方式是只使用一段用于捕捉 KeeperException 异常的代码，然后检测所捕 获异常的编码值是否为 KeeperExc 印 tion.Code.SESSIONEXPIRED。选择使用哪种方式取决于 编程风格，因为两种方式的效果相同。

中的另一台服务器。如果在尝试集合体中所有服务器之后仍然无法建立连接， 它会抛出一个 IOException 异常。由于所有 ZooKeeper 服务器都不可用的可 能性很小，所以一些应用程序选择循环重试操作，直到 ZooKeeper 服务可用 为止。

这仅仅是一种重试处理策略，还有许多其他策略，例如使用“指数退回” (exponential backoff)，每次将重试的间隔乘以一个常数。

###### 21.4.3锁服务

分布式锁能够在一组进程之间提供互斥机制，使得在任何时刻只有一个进程可以 持有锁。分布式锁可以用于在大型分布式系统中实现领导者选举，在任何时间 点，持有锁的那个进程就是系统的领导者。

I 不要将 ZooKeeper 自己的领导者选举和使用 ZooKeeper 基本操作实现的一般的 领导者选举服务混为一谈。事实上，ZooKeeper中包含有一个领导者选举服务 的实现。ZooKeeper自己的领导者选举机制是不对外公开的，我们这里所描述 的一般领导者选举服务则不同，它是为那些需要所有进程与主进程保持一致的 分布式系统所设计的。

为了使用 ZooKeeper 来实现分布式锁服务，我们使用顺序 znode 来为那些竞争锁 的进程强制排序。思路很简单：首先指定一个作为锁的 znode，通常用它来描述被 锁定的实体，称为/leader然后希望获得锁的客户端创建一些短暂顺序 znode，作 为锁 znode 的子节点。在任何时间点，顺序号最小的客户端将持有锁。例如，有 两个客户端差不多同时创建 znode，分别为/leader/lock-1和/leader/lock-2，那么创 惠/leader/lock-l的客户端将会持有锁，因为它的 znode 顺序号最小。ZooKeeper服 务是顺序的仲裁者，因为它负责分配顺序号。

通过删除 znode /leader/loch 1即可简单地将锁释放；另外，如果客户端进程死 亡，对应的短暂 znode 也会被删除。接下来，■/leader/lock』的客户端将持有 锁，因为它的顺序号紧跟前一个。通过创建一个关于 znode 删除的观察，可以使 客户端在获得锁时得到通知。

如下是申请获取锁的伪代码。

(1)在锁 znode 下创建一个名为知的短暂顺序 znode，并且记住它的实际路 径名(create操作的返回值)。

(2)查询锁 znode 的子节点并且设置一个观察。

(3)如果步骤 1 中所创建的 znode 在步骤 2 返回的所有子节点中具有最小的 顺序号，则获取到锁，退出。

(4)等待步骤 2 中所设观察的通知，转到步骤 2

1.羊群效应

虽然这个算法是正确的，但还是存在一些问题。第一个问题是这种实现会受到羊 群效应(herd effect)的影响。在有成百上千客户端的情况，所有的客户端都在尝试 获得锁，所以每个客户端都会在锁 znode 上设置一个观察，用于捕捉子节点的变 化。每次锁被释放或一个新进程开始申请锁的时候，观察都会被触发并且每个客 户端都会收到一个通知。“羊群效应”就是指这种大量客户端收到同一事件的通 知，但实际上只有很少一部分需要处理这一事件。在这种情况下，只有一个客户 端会成功地获取锁，但是维护的过程以及向所有客户端发送观察事件会产生峰值 流量，这会对 ZooKeeper 服务器造成压力。

为了避免出现羊群效应，我们需要优化发送通知的条件。关键在于仅当前一个顺 序号的子节点消失时才需要通知下一个客户端，而不是删除(或创建)任何子节点时 都进行通知。在我们的例子中，如果客户端创建了 znode /leader/lock-1 y /leader/lock-2 夭 W /leader/lock-3，那么只有当 /leader/lock-2 消失时才需要通知 /leader/lock-3 对应的客户端；/leader/lock-l 消失或有新的 znode /leader/lock-4 加入 时，不需要通知该客户端。

2.可恢复的异常

这个申请锁的算法目前还存在另一个问题，就是不能处理因连接丢失而导致的 create操作失败。如前所述，在这种情况下我们不知道操作是成功还是失败。由于创 建一个顺序 znode 是非幂等操作，所以我们不能简单地进行重试。原因在于如果 第一次创建已经成功，重试会使我们多出一个永远删不掉的孤儿 znode(至少到客户 端会话结束前)。最不幸的结果是还将会出现死锁。

问题在于，在重新连接之后客户端不能够判断它是否已经创建过子节点。解决方 案是在 znode 的名称中嵌入一个 ID，如果客户端出现连接丢失的情况，重新连接 之后它便可以对锁节点的所有子节点进行栓查，看看是否有子节点的名称中包含 其 ID。如果有一个子节点的名称包含其 ID，它便知道自己的创建操作已经成功， 不需要再创建子节点。如果没有子节点的名称中包含其 ID，则客户端可以安全地 创建一个新的顺序子节点。

客户端会话的 ID 是一个长整数，并且在 ZooKeeper 服务中是唯一的，因此非常适 合在连接丢失后用于重新识别客户端。可以通过调用 Java ZooKeeper类的 getSessionId()方法来获得会话的 ID。

在创建短暂顺序 znode 时应当采用-这样的命名方式，ZooKeeper 在其尾部添加顺序号之后，znode的名称会形如 lock-<sessionId>-<sequenceNumber〉0由于 顺序号对于父节点来说是唯一的，但对于子节点名并不唯一，因此采用这样的命 名方式可以让子节点在保持创建顺序的同时能够确定自己的创建者。

3.不可恢复的异常

如果一个客户端的 ZooKeeper 会话过期，那么它所创建的短暂 znode 将会被删 除，已持有的锁会被释放，或者是放弃了申请锁的位置。使用锁的应用程序应当 意识到它已经不再持有锁，应当清理它的状态，然后通过创建并尝试申请一个新 的锁对象来重新启动。注意，这个过程是由应用程序控制的，而不是锁，因为锁 是不能预知应用程序需要如何清理自己的状态。

4.实现

正确地实现一个分布式锁是一件棘手的事，因为很难对所有类型的故障都进行正 确的解释处理。ZooKeeper带有一个 Java 语言写的生产级别的锁实现，名为 WriteLock，客户端可以很方便地使用它。

###### 21.4.4更多分布式数据结构和协议

使用 ZooKeeper 可以实现很多不同的分布式数据结构和协议，例如“屏障” (barrier)，队列和两阶段提交协议。有趣的是它们都是同步协议，但我们可以使用 异步 ZooKeeper 基本操作(如通知)来实现它们。

ZooKeeper网站(http://zookeeper.apache.org)提供了一些用于实现分布式数据结构和 协议的伪代码。ZooKeeper本身也带有一些标准方法的实现(包括锁、领导者选举 和队列)，放在安装位置下的 rec/pes目录中。

Curator 项目提供了更多 ZooKeeper 方法的

实现。

BookKeeper 和 Hedwig

BookKeeper是一个具有高可用性和可靠性的日志服务。它可以用来实现预写式日 志(write-ahead logging)，这是一项在存储系统中用于保证数据完整性的常用技 术。在一个使用预写式日志的系统中，每一个写操作在被应用前都先要写入事务 日志。使用这个技术，我们不必在每个写操作之后都将数据写到永久存储器上， 因为即使出现系统故障，也可以通过重新执行事务日志中尚未应用的写操作来恢 复系统的最后状态。

BookKeeper客户端所创建的日志被称为 ledger、每一个添加到 ledger 的记录被称 为 ledger entry，每个 ledger entry就是一个简单的字节数组。ledger由保存有 ledger数据副本的 bookie 服务器组进行管理。注意，ledger数据不存储在 ZooKeeper中，只有元数据保存在 ZooKeeper 中。

传统上，为了让使用预写式日志的系统更加稳定，必须解决保存有事务日志的节 点的故障问题，这通常是通过某种方式复制事务日志来解决这个问题。前面描述 过的 HDFS 高可用性使用一组日志节点来提供高可用性编辑日志，这虽然与 BookKeeper相似，但它是为 HDFS 编写的独立服务，并且不采用 ZooKeeper 作为 协调引擎。

Hedwig是利用 BookKeeper 实现的一个基于主题的发布-订阅系统。以 ZooKeeper 作为基础，Hedwig提供了一个具有高可用性的服务，即使在订阅者长时间离线的 情况下它也能够保证消息的传递。

BookKeeper 是 ZooKeeper 的一个子项目，可以访问 <http://zookeeper.apache.org/> bookkeeper/，找到它和 Hedwig 的更多相关用法。

##### 21.5生产环境中的 ZooKeeper

在生产环境中，应当以复制模式运行 ZooKeeper。在这里，我们将讨论使用 ZooKeeper服务器的集合体时需要考虑的一些问题。但是本节的内容不够详尽，建 议参考《ZooKeeper管理员指南》(A即://加 7./>^加/«_^/利获得详细的最新操作指 南，包括支持的平台、推荐的硬件、维护过程和配置属性。

###### 21.5.1可恢复性和性能

在安放 ZooKeeper 所用的机器时，应当考虑尽量减少机器和网络故障可能带来的 影响。在实践过程中，一般是跨机架、电源和交换机来安放服务器，这样，这些 设备中的任何一个出现故障都不会使集合体损失半数以上的服务器。

对于那些需要低延迟服务（毫秒级别）的应用来说，最好将所有的服务器都放在同一 个数据中心的同一个集合体中。也有一些应用不需要低延迟服务，它们可以通过 跨数据中心（每个数据中心至少两台服务器）安放服务器来获得更好的可恢复性，领 导者选举和分布式粗粒度锁是这类应用的代表。这两个应用中的状态改变都相对 较少，因此相对于整个服务来说，数据中心之间传递状态改变消息所需的几十毫 秒开销是可以承受的。

![img](Hadoop43010757_2cdb48_2d8748-269.jpg)



ZooKeeper中有一个观察节点（observer node）的概念，是指没有投票权的跟随 者。由于观察节点不参与写请求过程中达成共识的投票，因此使用观察节点可

以让 ZooKeeper 集群在不影响写性能的情况下提髙读操作的性能。°使用观察

节点可以让 ZooKeeper 集群跨越多个数据中心，同时不会增加正常投票节点的 延迟。可以通过将投票节点安放在一个数据中心，将观察节点安放在另一个数 据中心来实现这一点。

ZooKeeper是具有高可用性的系统，对它来说，最关键的是能够及时地履行其职 能。因此，ZooKeeper应当运行在专用的机器上。如果有其他应用程序竞争资源， 可能会导致 ZooKeeper 的性能明显下降。

通过对 ZooKeeper 进行配置，可以使它的事务日志和数据快照分别保存在不同的 磁盘驱动器上。在默认情况下，两者都保存在 dataDir 属性所指定的目录中，但 是通过为 dataLogDir 属性设置一个值，便可以将事务日志写在指定的位置。通 过指定一个专用的设备（不只是一个分区），一个 ZooKeeper 服务器可以以最大速率 将日志记录写到磁盘，因为写日志是顺序写，并且没有寻址操作。由于所有的写 操作都是通过领导者来完成的，增加服务器并不能提高写操作的吞吐量，所以提 高性能的关键是写操作的速度。

如果写操作的进程被交换到磁盘上，则性能会受到不利的影响。这是可以避免

①详情参见 Henry Robinson 的文章，标题为 “Observers: Making ZooKeeper Scale Even Further”， 2009 年 12 月发表于 Cloudera，网址为 <http://www.cloudera.com/blog/2009/12/observers-making-zooker-scale-even-further/Q>

的，将 Java 堆的大小设置为小于机器上空闲的物理内存即可。ZooKeeper脚本可 以从它的配置目录中获取一个名为知 Vdf.eAZV的文件，这个文件被用来设置 3VMFLAGS 环境变量，包括设置 Java 堆的大小（和任何其他所需的 JVM 参数）。

###### 21.5.2配置

ZooKeeper服务器的集合体中，每个服务器都有一个数值型的 ID，服务器 ID 在集 合体中是唯一的，并且取值范围在 1〜255之间。可以通过一个名为町/6/的纯文 本文件设定服务器的 ID，这个文件保存在 dataDir 参数所指定的目录中。

为每台服务器设置 ID 只完成了工作的一半。我们还需要将集合体中其他服务器的 ID和网络位置告诉所有的服务器。在 ZooKeeper 的配置文件中必须为每台服务器 添加下面这行配置：

server.n=hostname:port:port

«是服务器的 ID。这里有两个端口设置：第一个是跟随者用来连接领导者的端 口；第二个端口用于领导者选举。这里有一个包含三台机器的复制模式下 ZooKeeper 集合体的配置例子：

tickTime=2000

dataDir=/diskl/zookeeper

dataLogDir=/disk2/zookeeper

clientPort=2181

initLimit=5

syncLimit=2

server.l=zookeeperl:2888:3888 server.2=zookeeper2:2888:3888 server.3=zookeeper3:2888:3888

服务器在 3 个端口上进行监听：2181端口被用于客户端连接；对于领导者来说， 2888端口被用于跟随者连接；3888端口被用于领导者选举阶段的其他服务器连 接。当一个 ZooKeeper 服务器启动时，它读取 myid 文件用于确定自己的服务器 ID，然后通过读取配置文件来确定应当在哪个端口进行监听，同时确定集合体中 其他服务器的网络地址。

连接到这个 ZooKeeper 集合体的客户端在 ZooKeeper 对象的构造函数中应当使用 zookeeperl:2181、zookeeper2:2181 和 zookeeper3:2181 作为主机字符串。

在复制模式下，有两个额外的强制参数：initLimit和 syncLimit，两者都是以 滴答参数的倍数进行度量。

initLimit参数设定了所有跟随者与领导者进行连接并同步的时间范围。如果在 没定的时间段内，半数以上的跟随者未能完成同步，领导者便会宣布放弃领导地 位，然后进行另外一次领导者选举。如果这种情况经常发生（可以通过日志中的记 录发现这种情况），则表明设定的值太小。

syncLimit参数设定了允许一个跟随者与领导者进行同步的时间。如果在设定的 时间段内，一个跟随者未能完成同步，会自己重启。所有关联到跟随者的客户端 将连接到另一个跟随者。

这些是建立和运行一个 ZooKeeper 服务器集群所需的最少设置。《ZooKeeper管理 员指南》（物列出了更多的配置选项，特别是性能调优方 面的。

##### 21.6延伸阅读

想要获取更多关于 ZooKeeper 的深度知识，请参阅 O’Reilly在 2013 年出版的 ZooKeeper一书，网址为 <http://shop.oreilly.com/product/0636920028901> .do），作者 Flavio 和 Benjamin Reed0


