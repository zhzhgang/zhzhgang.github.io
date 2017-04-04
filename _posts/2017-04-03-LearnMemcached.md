---
layout: post
title: "Memcached 学习之旅"
date: 2017/4/3 17:47:35  
description: "Memcached 学习笔记"
categories: [数据库]
tags: [数据库, Memcached, 缓存]
---

* [Memcached 简介](#1)
* [Memcached 的安装与配置](#2)
* [Memcached 特性实战](#3)

# Memcached 简介

## Memcached 是什么

Memcached 是一套高性能的、分布式内存对象缓存系统。它不是数据库。

它由 C 写成，它以 key-value 的方式将数据储存在内存中。

## Memcached 的特征

### 协议简单

* Memcached 的服务端客户端通信使用简单的文本协议，而不是笨重的 XML 等格式，因此，通过 telnet 也能在 memcached 上存取数据。
* 官方还提供二进制协议（Binary Protocol），不需要解析文本，还能减少文本协议的漏洞，使得原本高速的 memcached 的性能更上一层楼。

### 基于 libevent 的事件处理

* libevent 是个程序库，它将 Linux 的 epoll、BSD 类操作系统的 kqueue 等事件处理功能封装成统一的接口。
* 即使对服务器的连接数增加，也能发挥 O(1)的性能。Memcached 使用这个 libevent 库，因此能在 Linux、BSD、Solaris 等操作系统上发挥其高性能。

### 内置内存存储方式

* Memcached 中保存的数据都存储在 Memcached 内置的内存存储空间中。
* 由于数据仅存在于内存中，因此，重启 Memcached、重启操作系统会导致全部数据丢失。
* 另外，内容容量达到指定值（启动时可通过 -m 参数配置）之后，就基于 LRU(Least Recently Used，最近最少使用)算法自动删除不使用的缓存。
* 该功能是可以配置的，Memcached 启动时通过 -M 参数可以禁止 LRU。不过，Memcached 本身是为缓存而设计的，建议开启 LRU。

### Memcached 不互相通信的分布式

* Memcached 虽然称为“分布式”缓存服务器，但服务器端并没有“分布式”功能。Memcached 的分布式是完全由客户端程序库实现的。这种分布式是 Memcached 的最大特点。通过这种方式，Memcached server 之间的数据不需要同步，也就不需要互相通信了。
* 函数库通过使用与数据保存时相同的算法（一种是求余 Hash，另一种是 Consistent Hashing)，根据“键”选择服务器。使用的算法相同，就能选中与保存时相同的服务器。只要数据还在，就能获得之前保存的值。
* 求余 Hash 分布式算法会导致 Cache 命中率（缓存很重要的指标之一）降低，而 consistent hash 算法，会影响新加入的 server 逆时针方向的 server 节点的命中率。
* 这样，Memcached 服务器增多后，键就会分散，即使一台 Memcached 服务器发生故障无法连接，也不会影响其他的缓存，系统依然能继续运行。

## Memcached 能做什么

做缓存系统。Memcached 可以存储各种格式的数据，包括图像、视频、文件等。

### 使用场景

* 网站包含了大访问量的动态网页，因而数据库的负载将会很高, 且大部分数据库请求都是读操作。
* 数据库服务器的负载比较低，CPU 使用率却很高。
* 小型需要共享的数据，如 session 等临时数据。
* 缓存一些很小但是被频繁访问的文件。图片这种大点儿的文件就由 CDN（内容分发网络）来处理。

### 不适用场景

* 缓存对象大于 1 MB, Memcached 本身就不是为了处理庞大的多媒体和巨大的二进制块而设计的，如果非要存这么大的数据，可以修改源代码，它是开源的，不过要慎改。
* key 的长度大于 250 字符（硬性要求）。
* 应用运行在不安全的环境中，Memcached 未提供任何安全策略，仅仅通过 telnet 就可以访问到 memcached。
* 业务需要的是持久化数据时，应使用数据库。

## Memcached的使用


## 相关链接
* [Memcached 官网](http://memcached.org/ "Memcached 官网")
* [Memcached 源码](https://github.com/memcached/memcached)

# <span id="2">Memcached 的安装与配置</span>

## 安装

### Linux 下安装

安装方式有两种：

* 包管理工具安装
* 源码安装

#### 包管理工具安装

> sudo apt-get install memcached

Memcached 的卸载

> sudo apt-get --purge remove memcached

#### 源码安装

查看 libevent 是否已经安装。它是 Memcached 的依赖包， 必须在 Memcached 之前装好:

dpkg -l | grep libevent

安装 libevent：

> wget http://labfile.oss.aliyuncs.com/libevent-2.0.21-stable.tar.gz
> 
> cd libevent-2.0.21-stable
> 
> ./configure --prefix=/usr && make test && sudo make install

安装 Memcached：

> wget http://labfile.oss.aliyuncs.com/memcached-1.4.22.tar.gz

> ./configure --with-libevent=/usr && make test && sudo make install

### Windows 下的安装


### Mac 下的安装

## 配置

启动 Memcached：

> memcached -d -m 256 -u root -l localhost -p 11210 -c 256 -P /tmp/memcached.pid

查看帮助文档： 

> memcached -h


# <span id="3">Memcached 特性实战</span>

## Memcached 的内存存储

slab allocator 缓存内存碎片化。

Memcached 采用的是 slab allocator 缓存碎片化的机制来管理内存。

page: 分配给 Slab 的内存空间，默认是 1 MB。分配给 Slab 之后根据 slab 的大小切分成 chunk。

Chunk: 用于缓存记录的内存空间。

Slab class: 特定大小的 chunk 的组。

也就是说，slab allocator 预先按照规定的大小，将内存分割为多个特定长度的块以解决内存存储的问题。

memcached 保存了 slab 空闲 chunk 表，当需要内存的时候，通过判断内存大小，选择合适的 Chunk 进行存储，将数据缓存起来。

memcached 虽然减少了碎片化，但是却会造成内存浪费。

可以通过调整 grow factor 来调节 slab class 的大小，memcached 在启动时可以指定特定的 grow factor（-f），其默认值是 1.25，指定的大小是 1-2 之间。

> memcached -f 1.5 -vvv

在 memcached 中，数据是不会消失的，memcached 不会自己释放已经分配的内存，而且不会监视该内存是否过期。

当追加新记录却空间不足时，采用 Least Recently Used(LRU)机制来分配空间。

