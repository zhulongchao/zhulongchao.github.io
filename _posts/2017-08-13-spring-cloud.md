---
layout: post
title: spring-cloud：组件小结
categories: [Middleware]
tags: DRPC 
---

Spring Cloud为开发者提供了在分布式系统(如配置管理、服务发现、断路器、智能路由、微代理、控制总线、一次性Token、全局锁、决策竞选、分布式会话和集群状态)等微服务组件

![](http://tech.lede.com/2017/03/15/rd/server/SpringCloud0/SpringCloudTechs.png)

### 服务注册中心 ###
注册，续约(心跳机制，防止剔除)，下线

![](http://www.itmuch.com/images/4.4.png)

服务提供者

服务消费者：负载均衡

feign 默认打开了ribbon(负载均衡)，以及hystrix(熔断).

client的一套规则(选择服务提供者)，默认线性选择

RandomRule，随机规则。并发修改upList导致取出的server为空。

RoundRobinRule：线性轮询，current++%serverCount

RetryRule:依赖RoundRobinRule选择，加上重试时间的choose

WeightResponseTimeRule： 根据响应时间，计算权重区间。计算随机权重，看看落到哪个区间，调用哪个服务

ClientConfigEnableRoundRobinRule

BestAvailableRule

PredicateBasedRule

AvailabilityFilterRule, 

ZoneAvoidanceRule 


feign 依赖ribbon实现服务的均衡调用。

Eureka：高可用，多示例

hystrix:断路器，依赖aop实现

服务降级，


### configcenter ###
多环境配置
![](http://tech.lede.com/2017/06/12/rd/server/springCloudConfig/spring-cloud-config-4.png)

### zuul ###
- 动态路由
- 监控
- 安全
- 认证鉴权
- 压力测试
- 金丝雀测试
- 审查
- 服务迁移
- 负载剪裁
- 静态应答处理

![](http://tech.lede.com/2017/05/16/rd/server/SpringCloudZuul/2.png)

### sleuth ###
数据收集，存储，展现


收集流程

![](http://tech.lede.com/2017/04/19/rd/server/SpringCloudSleuth/sleuthZipkinHttp.png)

业务示例

![](http://daixiaoyu.com/images/distributed-tracing/dt003.png)

zipkin具有存储展现能力，内存型存储和mysql、es和cassadra

日志收集涉及到一个采样频率

数据存储涉及到一个日志跟踪问题。traceId

http://tech.lede.com/2017/04/19/rd/server/SpringCloudSleuth/sleuthZipkinHttp.png


### 分布式锁 ###

db实现，主键不重复，行锁，selectForUpdate

唤醒机制：

连接池耗费完的问题，事务超时

redis：setNx 

没有唤醒机制，自带失效删除。

zookeeper ，创建节点，其他现在通过watch机制来唤醒 ，羊群效应，因为watch机制，接收了很多无关信息。

### 支持spdy和Http2 ###

当前service之间的调用都是基于 rest，是采用json的方式进行数据交互。是采用的http 1.0/1.2

采用okhttp作为通信组件，可以同时支持spdy和http/2 两种协议

spring boot支持http/2的方式
参考：https://toutiao.io/posts/dkrn1d/preview


### 集成非springboot项目###


mock了一个eureka 服务提供者的客户端

提供一个health的 rest服务

sidecar的实现参考:
http://blog.csdn.net/qq_32971807/article/details/53742783




### 系统上线问题 ###

代码级别：记录超时的log 

com.alibaba.common.lang.diagnostic.Profiler