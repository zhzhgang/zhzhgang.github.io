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


-------------


# Redis 数据类型

Redis 不仅仅是简单的 key-value 存储器，同时也是一种数据结构服务器（data structures server）。

传统的 key-value 是指使用一个 key 字符串来索引 value 字符串的存储。而在 Redis 中，value 不仅仅支持字符串，还支持更多的复杂结构，包括列表、集合、哈希表等。

## Redis Strings 

字符串是最基本的 Redis 值类型。

Redis 字符串是二进制安全的，意味着一个 Redis 字符串能包含任意类型的数据。一个字符串类型的值最多能存储 512M 字节的内容。

用 set 和 get 命令来创建和检索 strings。

## Redis Lists

Redis 列表是简单的字符串列表，按照插入顺序排列。

lpush 命令插入一个新的元素到头部（左边），rpush 插入一个新元素到尾部（右边）。

push 一类的命令的返回值是 list 长度。

这些命令都是可变的命令，即可以一次将多个元素放入 list。

pop 命令取出 list 中的元素，和 push 类似，pop 可以选择从不同的方向取出元素。

pop 命令返回值为取出的元素。

## Redis Hashes

Redis Hashes 是字符串字段和字符串值之间的映射，因此是展现对象的完美数据类型。

hmset 命令设置一个多域的 hash 表。

hget 命令获取指定的单域。

hgetall 命令获取指定 key 的所有信息。

hmget 类似于 hget，只是返回一个value数组。

同样可以根据需要对 hash 表的表项进行单独的操作，例如 hincrby

## Redis 无序集合

Redis 集合（Set）是一个无序的字符串集合。

可以以 O(1) 的时间复杂度，完成添加、删除、测试元素是否存在。

sadd 命令产生一个无序集合，返回集合的元素个数。

smembers 命令用于查看集合。

sismember 用于查看集合是否存在，匹配项包括集合名和元素个数。匹配成功返回 1，失败返回 0。

## Redis 有序集合

有序集合的每一个成员都关联了一个评分，这个评分被用来按照从最低分到最高分的方式排列集合中的成员。

集合中的成员是唯一的，但是评分可以是重复的。

使用有序集合，可以以 O(logN) 的时间复杂度，添加，删除，和更新元素。

zadd 与 sadd 类似，但是在元素之前多了一个参数，这个参数便是用于排序的，形成一个有序的集合。
> zadd sortedset 2 abc
> 
> zadd sortedset 5 ccc

> 查看正序的集合：
> 
> zrange sortedset 0 -1
> 
> 查看反序的集合：
> 
> zrevrange sortedset 0 -1 

> 使用 withscores 参数返回记录值：
> 
> zrange sortedset 0 -1 withscores


