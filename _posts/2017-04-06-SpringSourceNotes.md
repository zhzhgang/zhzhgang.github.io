---
layout: post
title: "Spring 源码阅读笔记"
date: 2017/4/6 23:00:30  
description: "Spring 源码阅读笔记"
categories: [框架]
tags: [Spring]
---

* [一、Spring 整体架构和环境搭建](#1)
* [二、容器的基本实现](#2)

# <span id="1">一、Spring 整体架构和环境搭建</span>

## 1.1 Spring 的整体架构

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

 
## 1.2 环境搭建

Spring 源码托管在 GitHub 上，基于 Gradle 的构建来构建项目。

所以要构建 Spring 源码环境，首先要安装 GitHub 和 Gradle。

### 1.2.1 安装 GitHub



### 1.2.2 安装 Gradle

Gradle 是一个基于 Groovy 的构建工具

（1）下载，解压

（2）配置环境变量

* 新建系统变量，变量名：GRADLE_HOME；变量值：Gradle 解压目录（例如：D:\ruanjian\Gradle\gradle-3.4.1-all\gradle-3.4.1）
* 将 Gradle 对应的环境变量加入到 path 中：在 path 变量后添加 ;%GRADLE_HOME%\bin

（3）测试
配置好环境变量后，打开命令窗口，输入命令 gradle -version，如果安装成功会出现 Gradle 对应的版本信息。

### 1.2.3 下载 Spring

（1）在 GitHub 上找到 spring-projects/spring-framework，fork。

（2）通过 cd 命令将当前操作目录切换到想要存储源码的目录。然后输入以下命令：

> git https://github.com/zhzhgang/spring-framework.git

（3）下载完成。但是当前的源码并不能直接导入 eclipse，需要进行转换。

（4）只对感兴趣的工程进行 eclipse 工程转换。例如要查看 Spring 事务部分的源码，将当前目录切换到 Spring-tx 文件夹下，执行 gradle cleanidea eclipse。

转换失败，报错：“-XX:MaxMetaspaceSize=1024m” 及 “could not create the java Virtual machine”。原因是：下载的 Spring 是 4.0 或以上的版本，需要将 jdk 升级到 1.8 或以上版本。

（5）转换完成，对应文件夹下出现了作为 eclipse 工程所必须的 .project 和 .classpath 文件。

（6）打开 eclipse，将工程导入。

（7）工程面前有感叹号，说明存在错误：

* spring-core 项目里丢失了两个 jar 包，spring-cglib-repack-3.2.5.jar 和 spring-objenesis-repack-2.5.1.jar

解决方法：在 bulid.gradle 文件里在找到了两个 task：cglibRepackJar和objenesisRepackJar。将当前工作目录切换到 spring-framework，按如下方式构建就行了：

> F:\GitRepo\spring-framework>gradle cglibRepackJar
> 
> F:\GitRepo\spring-framework>gradle objenesisRepackJar

* 当前工程还要依赖于其他 Spring 中的工程

解决方法：以同样的方式继续导入其他源码工程，或者，直接找到对应的 jar 包加入编译路径。



# <span id="2">二、容器的基本实现</span>


## 容器基本用法


## 功能分析


## 工程搭建



