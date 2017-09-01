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
