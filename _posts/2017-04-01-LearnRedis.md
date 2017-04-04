---
layout: post
title: "Redis 学习之旅"
date: 2017/4/1 22:51:34  
description: "Redis 学习笔记"
categories: [数据库]
tags: [数据库, Redis]
---

* [Redis 简介与安装](#1)
* [Redis 数据类型](#2)
* [Redis 系统管理](#3)
* [Redis 的高级应用](#4)

# <span id="1">Redis 简介与安装</span>


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


# <span id="2">Redis 数据类型</span>

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


----

# <span id="3">Redis 系统管理</span>

## 适合全体类型的常用命令

启动 redis 服务和 redis-cli：

sudo service redis-server start

redis-cli

### exists and del

exists key 判断一个 key 是否存在，存在返回 1，否则返回 0。

del key 删除某个 key，或是一系列 key。del key1 key2 key3，成功返回 1，失败返回 0（key 值不存在）。

### type and keys

type key：返回某个 key 元素的数据类型（none: 不存在，string：字符，list, set, zset, hash）。key 不存在返回空。

keys key-pattern：返回匹配的 key 列表（keys foo*：查找 foo 开头的 key）

### randomkey and clear

randomkey：随机获得一个已存在的 key。如果当前数据库为空，则返回空字符串

clear: 清除界面

### rename and renamenx

rename oldname newname：改key的名字，新键如果存在将被覆盖

renamenx oldname newname：更改key的名字，如果名字存在则更改失败

### dbsize

dbsize：返回当前数据库的key的总数

## Redis 时间相关命令

### 限定 key 生存时间

这也是一个无视数据类型的命令，对于临时存储很有用处。避免进行大量的DEL操作。

expire：设置某个key的过期时间（秒），(expire bruce 1000：设置bruce这个key 1000秒后系统自动删除)。

注意：如果在还没有过期的时候，对值进行了改变，那么那个值会被清除。

### 查询 key 剩余生存时间

ttl：查找某个 key 还有多长时间过期，返回时间秒

### 清除 key

flushdb：清空当前数据库中的所有键

flushall：清空所有数据库中的所有键

## Redis 设置相关命令

Redis有其配置文件，可以通过 client-command 窗口查看或者更改相关配置

### config get 和 config set 

 config get：用来读取运行Redis服务器的配置参数。

config set：用于更改运行Redis服务器的配置参数。

auth：认证密码

> config get requirepass （查看密码）

> config set requirepass 123 （设置密码为123 ）

> config get requirepass  （报错，没有认证）

> auth test123

> config get requirepass

### 重置报告

config resetstat：重置数据统计报告，通常返回值为 OK。

## 查询信息

info [section]：查询Redis相关信息。info 命令可以查询 Redis 几乎所有的信息，其命令选项有如下：

1. server: Redis server的常规信息
2. clients: Client的连接选项
3. memory: 存储占用相关信息
4. persistence: RDB and AOF 相关信息
5. stats: 常规统计
6. replication: Master/slave请求信息
7. cpu: CPU 占用信息统计
8. cluster: Redis 集群信息
9. keyspace: 数据库信息统计
10. all: 返回所有信息
11. default: 返回常规设置信息

若命令参数为空，info命令返回所有信息。


# <span id="4">Redis 的高级应用</span>

## 安全性

设置密码的两种方式：

* 使用 config set 命令的 requirepass 参数，具体格式为：config set requirepass “password”
* 配置 redis.conf 中设置 requirepass 属性，后面为密码

输入认证的方式也有两种：

* 登录时可以 redis-cli -a password
* 登录后使用 auth password

## 主从复制

通过主从复制可以允许多个 slave server 拥有和 master server 相同的数据库副本。

从服务器只能读，不能写。

Redis 主从复制的特点：

* master 可以拥有多个 slave
* 多个 slave 可以连接同一个 master 外，还可以连接到其他的 slave。（当 master 宕机后，相连的 slave 转变为 master）
* 主从复制不会阻塞 master，再同步数据时，master 可以继续处理 client 请求
* 提高了系统的可伸缩性


Redis主从复制的过程：


1. Slave 与 master 建立连接，发送 sync 同步命令
2. Master 会启动一个后台进程，将数据库快照保存到文件中，同时 Master 主进程会开始收集新的写命令并缓存
3. 后台完成保存后，就将此文件发送给 Slave
4. Slave 将此文件保存到磁盘上

## 事务处理

Redis 的事务处理比较简单。只能保证 client 发起的事务中的命令可以连续的执行，而且不会插入其他的 client 命令。

当一个 client 在连接中发出 **multi** 命令时，这个连接就进入一个事务的上下文，该连接后续的命令不会执行，而是存放到一个队列中，当执行 exec 命令时，redis 会顺序的执行队列中的所有命令。

如果其中执行出现错误，执行正确的不会回滚，不同于关系型数据库的事务。

## 持久化机制

Redis 是一个支持持久化的内存数据库，Redis 需要经常将内存中的数据同步到磁盘来保证持久化。

Redis 支持两种持久化方式：

* snapshotting（快照），将数据存放到文件里，默认方式。

是将内存中的数据已快照的方式写入到二进制文件中，默认文件 dump.rdb，可以通过配置设置自动做快照持久化的方式。可配置 Redis 在 n 秒内如果超过 m 个 key 被修改就自动保存快照。

> save 900 1 #900秒内如果超过 1 个 key 被修改，则发起快照保存

* Append-only file（缩写为 aof），将读写操作存放到文件中

由于快照方式在一定间隔时间做一次，所以如果 Redis 意外 down 掉的话，就会丢失最后一次快照后的所有修改。

aof 比快照方式有更好的持久化性，是由于使用 aof 时，redis会将每一个收到的**写命令**都通过 write 函数写入到文件中，当 redis 启动时会通过重新执行文件中保存的写命令来在内存中重新建立整个数据库的内容。

## 虚拟内存的使用

Redis 的虚拟内存是暂时把不经常访问的数据从内存交换到磁盘中，从而腾出内存空间用于其他的访问数据。

对于 redis 这样的内存数据库，内存总是不够用的。除了分隔到多个 redis server 外，提高数据库的容量的方法就是使用虚拟内存，把那些不常访问的数据交换到磁盘上。

通过配置 vm 相关的 redis.config 配置。
