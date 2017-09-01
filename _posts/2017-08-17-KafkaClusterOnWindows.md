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
