---
layout: post
title: zookeeper：读写流程
categories: [BIG_DATA]
tags: Config-Center 
---

## 基本架构 ##

![](https://tangcong.github.io/images/myblog/zkservice.jpg)

节点数要求是奇数。

常用的接口是 get/set/create/getChildren.

## 读写流程 ##
![](http://blog.pic.xiaokui.io/429d6246100a1529d1fa84ac0854f426)

### 写流程 ###

客户端连接到集群中某一个节点

客户端发送写请求

服务端连接节点，把该写请求转发给leader

leader处理写请求，一半以上的从节点也写成功，返回给客户端成功。


### 读流程 ###
客户端连接到集群中某一节点

读请求，直接返回。


## 故障恢复 && leader选举 ##
![](http://blog.pic.xiaokui.io/1eda39769fedacddb8ffe7f386be4000)

当leader down掉时。

集群暂停服务，进行leader选举，采用fast paxos协议

首先所有server，提交自己作为leader，log的ID（epoch+1），id作用交互数据

通过比较接收的日志事务Id和自身的事务ID。

等待一个周期，确定出最新的leader。

加载snapshot，执行log。


## 最终一致性 ##

读数据时，有可能会脏读。比较推荐watch的方式，实现数据的及时生效。



## 各节点数据完全一致 ##

各节点存储了全量的数据。


## 存储策略 ##
持久化存储是基于内存快照(snapshot)和事务日志（txlog）来存储。

snapshot和txlog的存储目录定义在zoo.cfg中，txlog存储磁盘和snapshot存储磁盘分开，避免io争夺。

txlog的刷盘阈值是1000。txlog是生成snapshot之后生成。

snapshot的保存数量和清理时间间隔配置在zoo.cfg中。


## 时间复杂度 ##

zookeeper 使用concurrenthashmap进行存储。锁的粒度是segment，减少锁竞争，segment里对应一个hashtable 的若干桶.

所以时间复杂度都是 O(1)