---
layout: post
title: "MongoDB 学习之旅"
date: 2017/4/4 15:31:02 
description: "MongoDB 学习笔记"
categories: [数据库]
tags: [数据库, MongoDB, NoSQL]
---

* [数据库和集合基本操作](#1)
* [数据查询](#2)
* [文档基本操作](#3)
* [查询、索引和聚合](#4)
* [高级查询与索引](#5)


# <span id="1">数据库和集合基本操作</span>

## MongoDB 简介

### 简介

MongoDB 是一个介于关系数据库和非关系数据库之间的产品，是非关系数据库中功能最丰富，最像关系数据库的。

它支持的数据结构非常松散，是类似 json 的 bson 格式，因此可以存储比较复杂的数据类型。

### 面向集合的存储

在 MongoDB 中，一个数据库包含多个集合，类似于 MySQL 中一个数据库包含多个表。

一个集合包含多个文档，类似于 MySQL 中一个表包含多条数据。

## 基本概念

### 数据库

* 一个 MongoDB 可以创建多个数据库
* 使用 show dbs 可以查看所有数据库的列表
* 使用 db 命令则可以查看当前数据库对象或者集合
* 使用 use 命令可以连接到指定的数据库

> 进入到mongo命令行：
>
> mongo
> 
> 链接到 test 数据库：
> 
> use test


### 文档

文档是 MongoDB 的核心，类似于关系数据库中的每一行数据。

多个键及其关联的值放在一起就是文档。

在 Mongodb 中，使用一种类 json 的 bson 存储数据，bson 数据可以理解为在 json 的基础上添加了一些 json 中没有的数据类型。

### 文档的逻辑联系

* 嵌入式关系：将一个文档嵌入到另一个文档中
* 引用式关系：将两个文档分开，通过引用文档的 _id 字段来建立关系

### 集合

集合就是一组文档的组合，相当于是关系数据库中的表。

在 MongoDB 中，可以存储不同文档结构的文档。

### 元数据

数据库的信息存储在集合中，他们统一使用系统的命名空间：DBNAME.system.* 

DBNAME 可用 db 或数据库名替代

* DBNAME.system.namespaces ：列出所有名字空间
* DBNAME.system.indexs ：列出所有索引
* DBNAME.system.profile ：列出数据库概要信息
* DBNAME.system.users ：列出访问数据库的用户
* DBNAME.system.sources ：列出服务器信息

## 数据库的创建和销毁

### 创建数据库

启动服务后，进入 MongoDB 命令行操作界面：

> mongo

使用 use 命令创建数据库：

> use mydb

查看当前连接的数据库：

> db

查看所有的数据库：

> show dbs

### 销毁数据库

使用 db.dropDatabase() 销毁数据库：

> use local
> 
> db.dropDatabase()


## 集合（collection）的创建和删除

### 创建集合

在数据库 mydb 中创建一个集合：

> use mydb
> 
> db.createCollection("users")

查看创建的集合

> show collections

### 删除集合

删除 users 集合：

> show collections
> 
> db.users.drop()


## 向集合中插入数据

### 使用 insert()

插入数据时，如果集合没有创建，会自动创建。

	use mydb 
	
	db.users.insert([
		{name : "jam",
		 email : "jam@qq.com"
		},
		{name : "tom",
		 email : "tom@qq.com"
		}
	])

### 使用 save()

插入数据时，如果 users 集合没有创建，会自动创建。

	use mydb2
	
	db.users.save([
		{name : "jam",
		 email : "jam@qq.com"
		},
		{name : "tom",
		 email : "tom@qq.com"
		}
	])


## 总结

本节介绍了 MongoDB 和集合的基本操作，在 MongoDB 中使用一种类 json 的 bson 存储数据。

可以使用 use 创建和切换数据库，show dbs 可以查看有哪些数据库。dropDatabase 可以删除数据库。

createCollection 可以创建集合，show collections 可以查看集合，insert() 和 save() 可以插入数据。


# <span id="2">数据查询</span>

## 查询语句

### find() 语句

find() 用法：db.COLLECTION_NAME.find()

### pretty() 语句

pretty() 可以使查询输出的结果更美观：

> db.COLLECTION_NAME.find().pretty()

## MongoDB 中的 AND

当 find() 中传入多个键值对时，MongoDB 就会将其作为 AND 查询处理:

> db.COLLECTION_NAME.find({ key1: value1, key2: value2 }).pretty()

## MongoDB 中的 OR

### OR

OR 查询语句以 $or 作为关键词。

	db.COLLECTION_NAME.find({
		$or:[
			{key1 : value1},
			{key2 : value2}
		]
	}).pretty()


## 同时使用 AND 和 OR

使用范例：

	db.COLLECTION_NAME.find({
		key : value,
	
		$or:[
			{key1 : value1},
			{key2 : value2}
		]
	}).pretty()

