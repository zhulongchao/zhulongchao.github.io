---
layout: post
title:  集群问题集
categories: [BIG_DATA]
tags: problem
---

##hadoop集群搭建遇到的问题

1.hadoop的环境变量写到 /etc/profile中


2.hadoop_env.sh 脚本中换行符为 \r



3.hadoop_env.sh 需要显示申明 JAVA_HOME

4.Name or service not knownstname
  slaves文件的格式是dos的，要转成unix
  ssh登录，提示yes

5.dfshealth.html net::ERR_CONNECTION_RESET
 端口号映射的区间太高


6.hibench工程中的bin文件 被windows污染


7.未导出report文件

需要安装yum install -y  bc

8.terasort

http://dongxicheng.org/mapreduce/hadoop-terasort-analyse/


benchmark：

wordcount

sort

terasort

sleep

dfsioe

kmeans

bayes

nutchindex

pagerank


集群监控：

ganglia

Ambari


apache eagle 策略安全

java 运行class失败
是classpath有问题


##hbase


hbase的高版本客户端可以连接低版本的server

低版本的server不可以连接高版本的server