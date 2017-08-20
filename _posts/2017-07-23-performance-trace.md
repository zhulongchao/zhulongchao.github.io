---
layout: post
title: 性能问题定位：工具使用
categories: [TOOL]
tags: Performance-Trace
---

## 1.网速测试 ##

### 安装iperf ###
	yum install epel-release
	从epel源中安装
	yum install -y  iperf



### 带宽检测 ###

	iperf -s 开启服务端
	
	iperf -c ip


![](http://images.cnblogs.com/cnblogs_com/zhulongchao/771893/o_iperf.png)


### 丢包问题 ###

	tcpdump进行抓包
	
	tcpdump -i eth0 -s 3000 port 8080 -w /home/tomcat.pcap
	
	对于抓包文件采用wireshark进行分析
	
	丢包(TCP DUP ACK) 重传(retransmission)，超时重传，


## 2.cdn性能测试 ##


	cdn 缓存，回源问题  
	
	304请求，浏览器是否使用本地缓存。比较last_modified 和if_modified_since
	
	通过实践戳来判断，浏览器缓存和cdn缓存

![](http://img4.tbcdn.cn/L1/461/1/2118052340.jpg)


## 3.DNS基础 ##

路由解析

泛域名解析


## 4.分布式服务链路追踪 ##

![](http://images.cnblogs.com/cnblogs_com/zhulongchao/771893/o_traceid.png)

	http入口产生一个traceId
	
	分发到rpc调用，cache，db，jms调用链路中
	
	google的著名论文dapper和zipkin
	
	日志聚合，绑定链路日志和业务日志
	
	采样采集，慢请求，异常服务。
	
	日志量大。日志异步写入，环状数组，日志组件自研
	
	共享信息放在ThreadLocal中。比如traceId


## 5.网卡性能问题定位 ##

	tsar -l  -i 1 --traffic
	查看网卡的进出流量

## 6.CPU性能问题定位 ##

	tsar -l  -i 1 --cpu
	
	软件问题定位，perf 采样所有进程数据
	
	perf record -F 99 -a -g -- sleep 30
	
	java进程的函数map：java -cp attach-main.jar:$JAVA_HOME/lib/tools.jar net.virtualvoid.perf.AttachOnce PID
	
	输出函数和地址的map
	
	输出火焰图
	perf script | stackcollapse-perf.pl | flamegraph.pl --color=java --hash > flamegraph.svg

![](http://images.cnblogs.com/cnblogs_com/zhulongchao/771893/o_QQ%e6%88%aa%e5%9b%be20170724162019.png)


## 7.内存性能问题定位 ##

-堆内内存问题，
    
    采用jmap dump内存，采用离线工具分析
	
	jprofile、mat
	
-堆外内存问题

a.google-perftools

	yum install -y google-perftools graphviz
	
	export LD_PRELOAD=/usr/lib64/libtcmalloc.so.4
	
	export HEAPPROFILE=/home/testGperf.prof
	
	执行程序，结束程序，生成prof
	
	分析prof
	
	生成svg, pdf,text
	pprof --svg $JAVA_HOME/bin/java testGperf.prof.0001.heap > test.svg
	
	pprof --pdf $JAVA_HOME/bin/java testGperf.prof.0001.heap > test.pdf
	
	pprof --text $JAVA_HOME/bin/java testGperf.prof.0001.heap > test.txt

b.jemalloc定位（优势，适合长时间trace）
	 
![](http://images.cnblogs.com/cnblogs_com/zhulongchao/771893/o_QQ%e6%88%aa%e5%9b%be20170724161232.png)

sudo apt-get install graphviz
编译安装
./configure --enable-prof --enable-stats --enable-debug  --enable-fill
make
make install

运行配置
export MALLOC_CONF="prof:true,prof_gdump:true,prof_prefix:/home/jedump/jez,lg_prof_interval:30,lg_prof_sample:17"
 
export LD_PRELOAD=/usr/local/lib/libjemalloc.so.2
运行
java -jar target/spring-boot-jemalloc-example-0.0.1-SNAPSHOT.jar
 
jeprof --show_bytes --svg jez.*.heap  > app-profiling.svg

注明：如果在docker容器中，推荐用pprof，jemalloc只显示函数地址，不显示函数名


## 8.机器资源配额问题 ##

/etc/security/limits.conf

* soft nofile 65536
* hard nofile 65536

控制该用户文件句柄数


## 9.磁盘性能问题定位 ##

tsar -l  -i 1 --io