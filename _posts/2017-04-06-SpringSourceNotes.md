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



