---
layout: post
title: "Windows 环境下搭建 Redis 集群"
date: 9/3/2017 4:45:06 PM
description: "Windows 环境下搭建 Redis 集群"
categories: [学习记录]
tags: [Redis]
---

目标：在本机 Windows 环境下部署一个 Redis 集群，一共 6 个节点，其中 3 台主 redis，其余三个则是各个主节点的从节点，每次数据都是同步的，当主节点挂掉了，那么从节点会变成主节点，如果之后刚开始的主节点启动以后，则会变为从节点。