---
layout: post
title: "Windows 环境下搭建 Redis 集群"
date: 2017/9/3 16:45:06
description: "Windows 环境下搭建 Redis 集群"
categories: [学习记录]
tags: [Redis]
---

目标：在本机 Windows 环境下部署一个 Redis 集群，一共 6 个节点，其中 3 台主 redis，其余三个则是各个主节点的从节点，每次数据都是同步的，当主节点挂掉了，那么从节点会变成主节点，如果之后刚开始的主节点启动以后，则会变为从节点。

# 一、部署 Redis 集群

## 1. 安装 Ruby

Windows 可以安装 RubyInstaller。http://railsinstaller.org/en。


## 2. 安装Redis

Redis 官方不支持 Windows，但是 Microsoft Open Tech group 在 GitHub 上开发了一个 Win64 的版本,下载地址为：https://github.com/MSOpenTech/redis/releases。

安装或解压缩到本地（D:\software\redis）。

## 3. 新建 6 个 Redis 的文件夹和相应的配置文件（以端口命名文件夹）

在 Redis 的安装目录，新建一个 cluster 的文件夹，里面新建 6 个子文件夹，以端口为名，如下图所示：
![](https://i.imgur.com/lmXAK1U.png)

接下来，在每个文件夹内，新建一个后缀名为 conf 的配置文件：
![](https://i.imgur.com/fFkte9A.png)


打开编辑，加入以下内容：

配置文件内容（以 redis.7100.conf 为例）

	bind 127.0.0.1
	
	port 7100
	
	appendonly yes
	
	appendfilename "appendonly.7100.aof"
	
	cluster-enabled yes
	
	cluster-config-file nodes-7100.conf
	
	cluster-node-timeout 15000
	
	cluster-slave-validity-factor 10
	
	cluster-migration-barrier 1
	
	cluster-require-full-coverage yes

依次编辑其他五个配置文件，替换相应端口号即可。

## 4. 用 Redis 命令（redis-server）安装 6 个 Redis 服务，并启动

命令行进入 Redis 安装目录 D:\software\redis。

安装第一个 Redis 服务，输入如下安装命令，然后回车。

安装命令

> redis-server --service-install cluster/7100/redis.7100.conf --service-name redis7100

卸载命令

> redis-server --service-uninstall --service-name redis7100

看到下面这界面，第一个服务就安装成功了：
![](https://i.imgur.com/SgBqudF.png)

安装成功之后，打开系统的服务，可以看到安装了 redis7100 这个服务：
![](https://i.imgur.com/M1znGuc.png)

接下来，就启动该服务，输入如下启动命令（也可以直接在服务里面，右键，启动它）：

启动命令

> redis-server --service-start --service-name redis7100

停止命令

> redis-server --service-stop --service-name redis7100

至此，Redis7100 就安装成功并且启动了，如下图所示：
![](https://i.imgur.com/QwYJqDM.png)

然后，相同的方式对 7101、7200、7201、7300、7301 分别进行安装，并且启动它们。

全部完成后，在系统的服务里面，可以看到下图的内容：
![](https://i.imgur.com/w7NRKn6.png)

同时，在 Redis 的安装目录内，会出现多个文件，如下所示：
![](https://i.imgur.com/pTSwQiC.png)

至此，6 个 Redis 服务已经全部安装并且启动完毕。

## 5. 用 Ruby 命令（gem）安装 Redis 的相关组件

回到命令行窗口，输入以下命令：

> gem install redis

回车，直到看到以下内容：

![](https://i.imgur.com/wr3yiuj.png)

gem 安装指令成功，接下来，就是创建集群了。

## 6. 用 redis-trib.rb 创建集群

由于创建启动集群需要 redis-trib.rb 文件，它是一个 Ruby 程序， 这个程序通过向实例发送特殊命令来完成创建新集群，检查集群或者对集群进行重新分片（reshared）等工作。

Windows 的 redis 安装文件中是没有这个文件的，需要去官网下载 Redis，官网的 Redis 是 Linux 版本，在其源码 src 文件夹下，将 redis-trib.rb 拷贝到本机中 Redis 的安装目录中。

然后回到命令行窗口，输入以下命令：

> ruby redis-trib.rb create --replicas 1 127.0.0.1:7100 127.0.0.1:7200 127.0.0.1:7300 127.0.0.1:7101 127.0.0.1:7201 127.0.0.1:7301

这个命令在这里用于创建一个新的集群, 选项 –replicas 1 表示为集群中的每个主节点创建一个从节点。之后跟着的其他参数则是这个集群实例的地址列表，3 个 master，3 个 slave。IP 地址的前 3 个为主（Master），后 3 个为从（Slave）。

输入后回车，会看到如下提示：
![](https://i.imgur.com/datny60.png)

redis-trib 会打印出一份预想中的配置，如果觉得没问题的话，就可以输入 yes，redis-trib 就会将这份配置应用到集群当中,让各个节点开始互相通讯。输入 yes 回车后，会自动配置好主从关系，如下图所示：
![](https://i.imgur.com/6VxSC3m.png)

在上图中，那些 40 位的编码，就是每个 Redis 节点的 ID，在调整集群时，经常会用到。

至此，集群配置完毕。

# 二、集群维护
打开命令行窗口，进入 Redis 安装目录，用客户端（redis-cli）即可查看集群情况，命令如下所示：

查看集群内所有的节点以及其关系

> redis-cli -c -h 127.0.0.1 -p 7100 cluster nodes
![](https://i.imgur.com/4jJYg0p.png)

也可以用客户端（redis-cli）登录到集群内任一节点，输入集群的命令，来查看集群的信息。

客户端登录集群命令（参数顺序无所谓，-h：ip地址 | -p：端口 | -c：集群标识）

> redis-cli -c -p 7200 -h 127.0.0.1

客户端退出登录

> Quit

![](https://i.imgur.com/pVpvceo.png)

登录之后，可以进行每个节点的操作，如果只查看集群的信息，输入 cluster info，则可以看到：
![](https://i.imgur.com/PXhp3Xw.png)
![](https://i.imgur.com/FAfJaNX.png)

集群特有的命令有（需要登录）

> cluster info   // 集群总揽
>
> cluster nodes   // 列出集群当前已知的所有节点（node），以及这些节点的相关信息
>
> cluster meet <ip> <port>   // 将指定的节点（ip:port）添加到集群中，让它成为集群的一员
>
> cluster forget <node_id>   // 从集群中移除 node_id 节点

> cluster replicate <node_id>   // 将当前节点设置为 node_id 节点的从节点

> cluster saveconfig   // 将节点的配置文件保存到硬盘里面

> cluster addslots <slot> [slot ...]   // 将一个或多个槽（slot）指派（assign）给当前节点

> cluster delslots <slot> [slot ...]   // 移除当前节点的一个或多个槽

> cluster flushslots   // 移除当前节点的所有槽（删除集群内的最后一个主节点时，可用到）

> cluster setslot <slot> node <node_id>   // 将槽 slot 指派给 node_id 节点。如果槽已经指派给另一个节点，那么先让另一个节点删除该槽，再进行指派
> 
> cluster setslot <slot> migrating <node_id>   // 将本节点的槽 slot 迁移到 node_id 节点中去

> cluster setslot <slot> importing <node_id>   // 从 node_id 节点中导入槽 slot 到本节点来

> cluster setslot <slot> stable   // 取消对槽 slot 的导入（import）或者迁移（migrate）

> cluster keyslot <key> // 计算键 key 应该被放置在哪个槽上

> cluster countkeysinslot <slot> // 返回槽 slot 目前包含的键值对数量

> cluster getkeysinslot <slot> <count> // 返回 count 个 slot 槽中的键

关于 redis-trib.rb 的一些使用

删除某个节点的命令如下：

> redis-trib.rb del-node <ip>:<port> 'node_id'   // 单引号内放置节点id

如果是删除从（Slave）节点，上述命令即可。

如果是删除主（Master）节点，则要看情况：

1. 如果主节点上有从节点，则要将从节点删除或转移到其它主节点上去，该主节点才能被删除。
1. 如果主节点上有槽（Slot），则要将槽删除或转移到其它主节点上去，该主节点才能被删除。

转移槽的方法：

> redis-trib.rb reshard <ip>:<port>   // 取消分配的槽（Slot）的节点

> How many slots do you want to move (from 1 to 16384)? <number>   // 填入的数字应该是该节点的全部槽，从 reshard 命令列出来的条目中得到

> What is the receiving node ID? <node_id>   // 需要接收这些槽的主节点 id，就是那 40 位编码
> 
> Please enter all the source node IDs.

>  Type 'all' to use all the nodes as source nodes for the hash slots.

>  Type 'done' once you entered all the source nodes IDs.

> Source node #1: <node_id>   // 要删除的主节点的 id

> Source node #2: done   // 输入 done

> Do you want to proceed with the proposed reshard plan (yes/no)? yes   // 输入 yes

待转移完后，再执行上面删除节点的命令。

Redis集群数据分配策略：

采用一种叫做哈希槽 (hash slot) 的方式来分配数据，redis cluster 默认分配了 16384 个 slot，当我们 set 一个 key 时，会用CRC16 算法来取模得到所属的 slot，然后将这个 key 分到哈希槽区间的节点上，具体算法就是：CRC16(key) % 16384

注意的是：必须要 3 个以后的主节点，否则在创建集群时会失败，三个节点分别承担的 slot 区间是：

1. 节点A覆盖0－5460;
1. 节点B覆盖5461－10922;
1. 节点C覆盖10923－16383.
