---
layout: post
title: "Spring 源码阅读笔记"
date: 2017/4/6 23:00:30  
description: "Spring 源码阅读笔记"
categories: [框架]
tags: [Spring]
---

* [Spring 整体架构和环境搭建](#1)

# <span id="1">Spring 整体架构和环境搭建</span>

## Spring 的整体架构

Spring 框架是一个分层架构，分为大约 20 个模块：

![](http://i.imgur.com/f63JVji.png)


模块总结：

* **Core Container**

核心容器包含 Core、Beans、Context 和 Expression Language

Core 和 Beans 模块是框架的基础部分，提供控制反转（IOC）和依赖注入（DI）特性。

* **Data Access/Integration**

包含有 JDBC、ORM、OXM、JMS 和 Transaction 模块，其中：

JDBC 模块提供了一个 JDBC 抽象层。

ORM 模块为对象-关系映射 API，

* **Web**



* **AOP**


* **Test**

支持使用 Junit 和 TestNG 对 Spring 进行测试。

 
## 环境搭建





# 容器的基本实现


## 容器基本用法


## 功能分析

