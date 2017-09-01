---
layout: post
title: "Windows环境下搭建Kafka集群"
date: 2017/9/1 15:28:13
description: "Windows环境下搭建Kafka集群"
categories: [学习记录]
tags: [Kafka]
---

目标：在本机 Windows 环境下部署三个 zookeeper 的 server 集群，并在此基础上部署三个 Kafka节点 的 server 集群。

# 一、部署 Zookeeper 集群

## 1. 建立文件夹，存放项目

在 D:\software 建立一个名为 zookeeper 的文件夹，并在该文件夹下建立三个目录，分别名为 server1，server2 和 server3。

## 2. 下载zookeeper


## 3. 解压文件并创建一些目录

将下载好的压缩包解压到 server1 目录下，并在 server1 目录下建三个文件夹，分别名为 data，dataLog 和 logs。如图：
![](http://i.imgur.com/lyfxvUV.png)

## 4. 在 server1/data 目录下，创建一个叫做 myid 的文件
打开 myid 文件，在里面添加对应的 id。因为现在配置的是 server1，所以添加 id 为 1。

## 5. 修改配置文件。
在 server1\zookeeper-3.5.3-beta\conf 目录下，将 zoo_sample.cfg 复制一份，重命名为 zoo.cfg。打开并编辑，加入如下内容：

	dataDir= D:/software/zookeeper/server1/data
	
	dataLogDir= D:/software/zookeeper/server1/dataLog
	
	clientPort=2181
	
	server.1=127.0.0.1:2888:3888
	
	server.2=127.0.0.1:2889:3889
	
	server.3=127.0.0.1:2890:3890

其中 dataDir 和 clientPort 原本就有，修改为上述值即可。

## 6. 复制 server1 中的内容到 server2 和 server3 中，并修改相应配置

将 server1 中的内容分别复制到 server2 和 server3 中，并修改 /data/myid 文件和 /zookeeper-3.5.3-beta/conf/zoo.cfg 文件。

myid 文件只需要将其中的内容改成服务器的序号即可。

zoo.cfg 文件需要修改 dataDir，dataLogDir 和 clientPort。修改后的文件如下：

server2：

	dataDir=D:/software/zookeeper/server2/data
	
	dataLogDir=D:/software/zookeeper/server2/dataLog
	
	clientPort=2182
	
	server.1=127.0.0.1:2888:3888
	
	server.2=127.0.0.1:2889:3889
	
	server.3=127.0.0.1:2890:3890

server3：

	dataDir=D:/software/zookeeper/server3/data
	
	dataLogDir=D:/software/zookeeper/server3/dataLog
	
	clientPort=2183
	
	server.1=127.0.0.1:2888:3888
	
	server.2=127.0.0.1:2889:3889
	
	server.3=127.0.0.1:2890:3890

## 7. 启动 zk 服务

分别进入三个 server 的 /zookeeper-3.5.3-beta/bin 目录下，运行：zkServer。

第一个 server 启动的时候会报错，这是由于它想要和其他两个 server 进行通信，而另外两个 server 还没有搭建起来。不用理会这个错误，继续打开新的 cmd 窗口，启动其他 server 就好。

## 8. 连接客户端

进入任意一个 zookeeper-3.5.3-beta 目录下，用以下命令启动客户端：zkCli –server 127.0.0.1:2181

输入help，显示可以执行的指令。

# 二、部署 Kafka 集群

## 1. 建立对应目录

在 D:\software 目录下，建立一个叫做 kafka 的文件夹，里面建立三个子目录，分别叫做：kafka1，kafka2 和 kafka3。

## 2. 下载 Kafka 并解压到每个文件夹

下载完成后，直接解压到 kafka1，kafka2 和 kafka3这三个文件夹。解压完后，在 kafka1，kafka2 和 kafka3 目录下分别添加一个 kafkaLog 目录。

![](http://i.imgur.com/cGSd7qy.png)

## 3. 修改配置文件。

分别打开 kafka1，kafka2 和 kafka3 三个目录中的 kafka_2.10-0.10.0.1 /config 路径下的 consumer.properties 文件，将里面的内容改成：

	zookeeper.connect=127.0.0.1:2181,127.0.0.1:2182,127.0.0.1:2183

再打开这三个目录下的 server.properties 文件，按照下面内容进行修改：

Kafka1:

	broker.id=0
	
	log.dirs=D:/software/kafka/kafka1/kafkaLog
	
	listeners=PLAINTEXT://127.0.0.1:9092
	
	port=9092
	
	zookeeper.connect=127.0.0.1:2181,127.0.0.1:2182,127.0.0.1:2183

Kafka2:

	broker.id=1
	
	log.dirs=D:/software/kafka/kafka2/kafkaLog
	
	listeners=PLAINTEXT://127.0.0.1:9093
	
	port=9093
	
	zookeeper.connect=127.0.0.1:2181,127.0.0.1:2182,127.0.0.1:2183

Kafka3:

	broker.id=2
	
	log.dirs=D:/software/kafka/kafka3/kafkaLog
	
	listeners=PLAINTEXT://127.0.0.1:9094
	
	port=9094
	
	zookeeper.connect=127.0.0.1:2181,127.0.0.1:2182,127.0.0.1:2183

## 4. 启动 kafka 服务器。

进入 kafka\kafka1\kafka_2.10-0.10.0.1\ 目录下，执行以下命令。

> bin\windows\kafka-server-start.bat config\server.properties

同样地，进入另外两个 kafka2 和 kafka3 目录下，把另外两个 kafka 服务器也启动起来。

# 三、使用 Kafka

## 1. 创建主题

进入 kafka 安装目录的 \bin\windows，执行以下命令：

> kafka-topics.bat --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test1

## 2. 创建 Producer 及 Consumer 来测试服务器

在 kafka 安装目录的 \bin\windows 启动新的命令窗口，producer 和 consumer 需要分别启动命令窗口。

启动 producter，启动命令如下：

> kafka-console-producer.bat  --broker-list localhost:9092  --topic test1

启动 consumer，启动命令如下：

> kafka-console-consumer.bat  --zookeeper localhost:2181  --topic test1

在 producter 窗口输入内容，如果在 consumer 窗口能看到内容，则说明 kafka 安装成功。

## 3. kafka常用命令

列出主题：

> kafka-topics.bat --list --zookeeper localhost:2181

描述主题：

> kafka-topics.bat --describe --zookeeper localhost:2181 --topic [topic name]

结果中，第一行给出了各个分区的概况，额外的每行都给出了一个分区的信息。如果只有一个主题的分区，所以只有一行。

Leader 是负责当前分区的所有读写请求。每个节点都将领导一个随机选择的分区。

replicas 是节点列表。

isr 是in-sync的集合。这是replicas列表当前还活着的子集。

从头读取消息：

> kafka-console-consumer.bat --zookeeper localhost:2181 --topic [topic name] --from-beginning

删除主题：

> kafka-run-class.bat kafka.admin.TopicCommand --delete --topic [topic_to_delete] --zookeeper localhost:2181
> 
> kafka-topics.bat --zookeeper localhost:2181 --delete --topic [topic name]

查看 topic 详细信息：



> kafka-topics.bat --zookeeper localhost:2181 --describe --topic [topic name]

为topic增加 partition：



> kafka-topics.bat --zookeeper localhost:2181 --alter --partitions 20 --topic [topic name]

测试容错性：

节点 1 是领导者，现在 kill 它。

> netstat -ano | findstr 9092
> 
> taskkill /pid 17004 /f

领导关系已经改为了从节点中的一个，节点 1 也不再 in-sync 复本集中。


