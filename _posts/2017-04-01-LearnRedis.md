---
layout: post
title: "Redis学习之旅"
date: 2017/4/1 22:51:34  
description: "Redis学习笔记"
categories: [数据库]
tags: [数据库, Redis]
---

# Redis 简介与安装


## Redis 简介


### Redis 是什么

Redis 是一个 key-value 存储系统。

Redis 提供了丰富的数据结构：lists, sets, ordered sets, hashes, strings。还包括了对这些数据结构的丰富操作。

### Redis 的优点

* 性能极高
* 丰富的数据类型
* 原子：Redis 的所有操作都是原子性的。
* 丰富的特性：支持 publish/subscribe，通知，key 过期等特性。

## Redis 安装


进入 root 目录，并下载 Redis 的安装包：

> cd 
> 
> wget http://labfile.oss.aliyuncs.com/files0422/redis-2.8.9.tar.gz

在目录下，解压安装包：

> tar xvfz redis-2.8.9.tar.gz

进入解压之后的目录，进行编译：

> cd redis-2.8.9 
> 
> make 
> 
> make install

说明：如果没有明显错误，表示编译成功。

安装成功之后，可以运行测试，确认 Redis 的功能是否正常：

> make test

## Redis 启动

### 查看重要文件

一些重要的文件，服务端：src/redis-server，客户端：src/redis-cli，默认配置文件：redis.conf。

### 将可执行文件放在 $PATH 环境目录下，以后执行程序时可以不用输入完整的路径

> cp redis-server /usr/local/bin/

> cp redis-cli /usr/local/bin

### 启动 Redis

> redis-server

### 查看Redis

	ps -ef | grep redis

	通过启动命令检查Redis服务器状态
	$ netstat -nlt | grep 6379

### 启动 Redis-client

> redis-cli