---
title: Hadoop HA 高可用集群的搭建
toc: true
date: 2019-10-29
---
# Hadoop HA 高可用集群的搭建

## hadoop部署服务器

| 系统      | 主机名   | IP            |
| --------- | -------- | ------------- |
| centos6.9 | hadoop01 | 192.168.72.21 |
| centos6.9 | hadoop02 | 192.168.72.22 |
| centos6.9 | hadoop03 | 192.168.72.23 |

## 基础环境准备

1.修改Linux主机名

2.修改IP

3.修改主机名和IP的映射关系 /etc/hosts

4.关闭防火墙

5.ssh免登陆

6.安装JDK，配置环境变量等

7.注意集群时间要同步

8.安装zookeeper集群

## 部署节点规划

集群部署节点角色的规划（3节点）

server01 namenode resourcemanager zkfc nodemanager datanode zookeeper journal node

server02 namenode resourcemanager zkfc nodemanager datanode zookeeper journal node

server03 datanode nodemanager zookeeper journal node

## 安装部署

上传编译好的hadoop安装程序到服务器上并解压到指定目录

```
[root@hadoop01 soft]# tar zxvf spark-2.2.0-bin-2.6.0-cdh5.14.0.tgz -C /usr/local/
[root@hadoop01 soft]# cd /usr/local/
[root@hadoop01 soft]# mv hadoop-2.6.0-cdh5.14.0 hadoop-HA
```

配置Hadoop环境变量，编辑/etc/profile添加Hadoop环境变量

[root@hadoop01 soft]# vim /etc/profile

```
############################HADOOP HA########################################
export HADOOP_HOME=/usr/local/hadoop-HA
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
```

修改Hadoop相关配置文件

修改hadoop-env.sh文件，配置HAVA_HOME如下

```
export JAVA_HOME=/usr/local/java/jdk1.8.0_201
```

修改core-site.xml

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
<property>
    <name>fs.defaultFS</name>
    <value>hdfs://cluster1</value>
</property>

<!-- 这里的路径默认是NameNode、DataNode、JournalNode等存放数据的公共目录 -->
<property>
    <name>hadoop.tmp.dir</name>
    <value>/usr/local/hadoop-HA/tmp</value>
</property>

<!-- ZooKeeper集群的地址和端口。注意，数量一定是奇数，且不少于三个节点-->
<property>
    <name>ha.zookeeper.quorum</name>
    <value>hadoop01:2181,hadoop02:2181,hadoop03:2181</value>
</property>
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)



修改hdfs-site.xml

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
<configuration>
<!--指定hdfs的nameservice为cluster1，需要和core-site.xml中的保持一致 -->
    <property>
        <name>dfs.nameservices</name>
        <value>cluster1</value>
    </property>
<!-- cluster1下面有两个NameNode，分别是nn1，nn2 -->
    <property>
        <name>dfs.ha.namenodes.cluster1</name>
        <value>nn1,nn2</value>
    </property>
<!-- nn1的RPC通信地址 -->
    <property>
        <name>dfs.namenode.rpc-address.cluster1.nn1</name>
        <value>hadoop01:9000</value>
    </property>
<!-- nn1的http通信地址 -->
    <property>
        <name>dfs.namenode.http-address.cluster1.nn1</name>
        <value>hadoop1:50070</value>
    </property>
<!-- nn2的RPC通信地址 -->
    <property>
        <name>dfs.namenode.rpc-address.cluster1.nn2</name>
        <value>hadoop02:9000</value>
    </property>
<!-- nn2的http通信地址 -->
    <property>
        <name>dfs.namenode.http-address.cluster1.nn2</name>
        <value>hadoop02:50070</value>
    </property>
<!-- 指定NameNode的edits元数据在JournalNode上的存放位置 -->
    <property>
                <name>dfs.namenode.shared.edits.dir</name>
        <value>qjournal://hadoop01:8485;hadoop02:8485;hadoop03:8485/cluster1</value>
    </property>
<!-- 指定JournalNode在本地磁盘存放数据的位置 -->
    <property>
                <name>dfs.journalnode.edits.dir</name>
        <value>/usr/local/hadoop-HA/journaldata</value>
    </property>
<!-- 开启NameNode失败自动切换 -->
    <property>
        <name>dfs.ha.automatic-failover.enabled</name>
        <value>true</value>
</property>
<!-- 指定该集群出故障时，哪个实现类负责执行故障切换 -->
    <property>
        <name>dfs.client.failover.proxy.provider.cluster1</name>
        <value>org.apache.hadoop.hdfs.server.namenode.ha.ConfiguredFailoverProxyProvider</value>
    </property>
<!-- 配置隔离机制方法，多个机制用换行分割，即每个机制暂用一行-->
    <property>
        <name>dfs.ha.fencing.methods</name>
        <value>sshfence</value>
    </property>
<!-- 使用sshfence隔离机制时需要ssh免登陆 -->
    <property>
        <name>dfs.ha.fencing.ssh.private-key-files</name>
        <value>/home/hadoop/.ssh/id_rsa</value>
    </property>
<!-- 配置sshfence隔离机制超时时间 -->
    <property>
        <name>dfs.ha.fencing.ssh.connect-timeout</name>
        <value>30000</value>
    </property>
</configuration>
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

修改mapred-site.xml

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
<configuration>
<!-- 指定mr框架为yarn方式 -->
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
</configuration>
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

修改yarn-site.xml

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
<configuration>
<!-- 开启RM高可用 -->
    <property>
        <name>yarn.resourcemanager.ha.enabled</name>
        <value>true</value>
    </property>

<!-- 指定RM的cluster id -->
    <property>
        <name>yarn.resourcemanager.cluster-id</name>
        <value>yrc</value>
    </property>

<!-- 指定RM的名字 -->
    <property>
        <name>yarn.resourcemanager.ha.rm-ids</name>
        <value>rm1,rm2</value>
    </property>

<!-- 分别指定RM的地址 -->
    <property>
        <name>yarn.resourcemanager.hostname.rm1</name>
        <value>hadoop01</value>
    </property>

    <property>
        <name>yarn.resourcemanager.hostname.rm2</name>
        <value>hadoop02</value>
    </property>

<!-- 指定zk集群地址 -->
    <property>
        <name>yarn.resourcemanager.zk-address</name>
        <value>hadoop01:2181,hadoop02:2181,hadoop03:2181</value>
    </property>

    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>

</configuration>
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

修改slaves(slaves是指定子节点的位置，因为要在hadoop01上启动HDFS、在hadoop03启动yarn，所以hadoop01上的slaves文件指定的是datanode的位置，hadoop03上的slaves文件指定的是nodemanager的位置)

```
hadoop01
hadoop02
hadoop03
```

## Hadoop集群启动

启动zookeeper集群（分别在hadoop01、hadoop02、hadoop03上启动zk）

bin/zkServer.sh start

\#查看状态：一个leader，两个follower

bin/zkServer.sh status

手动启动journalnode（分别在hadoop01、hadoop02、hadoop03上执行）

hadoop-daemon.sh start journalnode #运行jps命令检验，hadoop01、hadoop02、hadoop03上多了JournalNode进程

格式化namenode

在hadoop00上执行命令:

hdfs namenode -format #格式化后会在根据core-site.xml中的hadoop.tmp.dir配置的目录下生成个hdfs初始化文件

把hadoop.tmp.dir配置的目录下所有文件拷贝到另一台namenode节点所在的机器

scp -r hadoop-HA root@hadoop02:$PWD

\##也可以这样，建议hdfs namenode -bootstrapStandby

格式化ZKFC(在active上执行即可)

hdfs zkfc -formatZK

启动HDFS(在hadoop00上执行)

start-dfs.sh

启动YARN

start-yarn.sh

还需要手动在standby上手动启动备份的 resourcemanager

yarn-daemon.sh start resourcemanager

JPS查看启动进程

[![clip_image001](https://img2018.cnblogs.com/blog/421906/201903/421906-20190306105506732-778076578.png)](https://img2018.cnblogs.com/blog/421906/201903/421906-20190306105506131-2051919315.png)

## 集群验证

到此，hadoop-2.6.4配置完毕，可以统计浏览器访问:

[http://hadoop01:50070](http://hadoop01:50070/)

NameNode 'hadoop01:9000' (active)

[http://hadoop01:50070](http://hadoop01:50070/)

NameNode 'hadoop02:9000' (standby)

验证HDFS HA

首先向hdfs上传一个文件

hadoop fs -put /etc/profile /profile

hadoop fs -ls /

然后再kill掉active的NameNode

kill -9 <pid of NN>

通过浏览器访问：http://192.168.1.202:50070

NameNode 'hadoop02:9000' (active)

这个时候hadoop02上的NameNode变成了active

在执行命令：

hadoop fs -ls /

-rw-r--r-- 3 root supergroup 1926 2014-02-06 15:36 /profile

刚才上传的文件依然存在！！！

手动启动那个挂掉的NameNode

hadoop-daemon.sh start namenode

通过浏览器访问：http://192.168.1.201:50070

NameNode 'hadoop01:9000' (standby)

验证YARN：

运行一下hadoop提供的demo中的WordCount程序：

hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.4.1.jar wordcount /profile /out

测试集群工作状态的一些指令 ：

hdfs dfsadmin -report         查看hdfs的各节点状态信息

cluster1n/hdfs haadmin -getServiceState nn1                 获取一个namenode节点的HA状态

scluster1n/hadoop-daemon.sh start namenode 单独启动一个namenode进程

./hadoop-daemon.sh start zkfc 单独启动一个zkfc进程


# 相关

- [Hadoop HA 高可用集群的搭建](https://www.cnblogs.com/starzy/p/10481935.html)
