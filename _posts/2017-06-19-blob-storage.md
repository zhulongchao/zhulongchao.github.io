---
layout: post
title:  海量小文件存储：blob-storage
categories: [BIG_DATA]
tags: file
---


### 架构 ###
![](https://github.com/linkedin/ambry/wiki/images/clustermap.png)

### 解决多媒体文件CDN回源问题 ###

The ID has the appropriate information encoded in it that helps to locate a blob on a GET


### 集群管理 ###

hardware layoout that contains the list of machines, disks in each of the machines and capacity of each of the disks.

partition layout that contains the list of partitions, their placement information and their states. A partition in Ambry is a logical slice of the cluster

### 存储管理 ###

we pre-allocate a file for each on-disk log. The basic idea for the replicated store is the following : on put, append blobs to the end of the pre-allocated file so as to encourage a sequential write workload

To be able to service random reads of either user metadata or blobs, the replicated store must maintain an index that maps blob IDs to specific offsets in the on-disk log

![](https://github.com/linkedin/ambry/wiki/images/store.png)

Each index segment also contains a bloom filter to optimize on IO. The search first looks up the bloom filter to identify if the entry is present on disk. Bloom filters are great at providing a probabilistic answer to whether the blob is present on disk or not. This probability is completely tunable at the cost of more storage. 

The log serves two purpose - it acts as the data log to store the blobs and also as the transaction log to recover the index on a crash.

The recovery of the store is done by scanning the log from the last known checkpointed offset. On reading the log, the index entries are populated that failed to be checkpointed before the crash. Also, since the delete entries in the log follow the original entries, the recovery would naturally update the blobs as deleted in the index. The recovery as restores the bloom filters if they are corrupt.

Zero copy for gets

Maximum page cache usage 

Using fallocate to preallocate the log 

### 复制 ###

The assignment strategy of partition to threads is done in such a way to isolate data centers and increase batching

![](https://github.com/linkedin/ambry/wiki/images/replication2.pnghttps://github.com/linkedin/ambry/wiki/images/replication2.png)

Replica 2 then scans the ids and looks up its local store to find if the blob is missing. The reason that the blob could be available is because replica 2 could have got the blob from the router or from another replica. Replica 2 makes a list of blobs that it does not have.
![](https://github.com/linkedin/ambry/wiki/images/replication4.png)

![](https://github.com/linkedin/ambry/wiki/images/replication5.png)

![](https://github.com/linkedin/ambry/wiki/images/replication6.png)

This is a simplified version of the actual protocol. There are more optimizations such as **zero copy, crc checking, prioritizing higher lag** etc in the actual implementation. This **gossip** protocol helps Ambry to converge really fast.


### 路由 ###

The frontend, apart from providing a REST interface to the clients, also provides support for interacting with external services. Within LinkedIn, this layer performs antivirus checks, creates the notification system (kafka) and notifies it about blob creations and deletions, and communicates with other databases.

Today, the frontend and the router are blocking. This means that an operation holds on to the request thread until the entire blob is sent or received to/from a data node. This severely impacts the throughput and latency and makes it infeasible to support large blobs (which is the main reason for not supporting large blobs today). The absence of any chunking also means that for large blobs, the partition usage can be uneven and inefficient, besides affecting the throughput.

In order to fix this problem, we need to make both the frontend layer and the routing layer non-blocking and support streaming. This document describes the non-blocking router design. The non-blocking frontend is going to be written in a way that allows for using Netty or Rest.li in a non-blocking way
![](https://github.com/linkedin/ambry/wiki/images/router.png)

![](https://github.com/linkedin/ambry/wiki/images/putentry.png)

![](https://github.com/linkedin/ambry/wiki/images/deleteentry.png)

### FrontEnd ###

non-blocking

The non-blocking front end can be split into 3 well defined components:-

Remote Service layer - Responsible for interacting with any service that could potentially make network calls or do heavy processing (e.g. Router library) to perform the requested operations.
Scaling layer - Acts as a conduit for all data that goes in and out of the front end. Responsible for enforcing the non-blocking paradigm and providing scaling independent of all other components.
NIO layer - Performs all network related operations including encoding/decoding HTTP.

remote service layer:

This layer mainly interacts with the remote service library by calling the right APIs but is also responsible for doing any pre processing (like ID transformations, anti virus checks etc) before making those calls. One instance of a single RemoteService is started by the RestServer.

Scaling Layer:

This layer is the core of the non-blocking front end. It enforces the non-blocking paradigm and acts as a conduit for data flowing between the remote service layer and the NIO layer

NIO Layer:

The NIO layer is responsible for all network related operations including encoding/decoding HTTP. On the receiving side, the NIO framework is expected to provide a way to listen on a certain port for requests from clients, accept them, decode the HTTP data received and handoff this data to the scaling framework in a NIO framework agnostic format

PUT
![](https://raw.githubusercontent.com/wiki/linkedin/ambry/images/image2016-3-10%2015_27_16.png)

POST
![](https://github.com/linkedin/ambry/wiki/images/image2015-11-25%2018_12_21.png)

DELETE
![](https://github.com/linkedin/ambry/wiki/images/image2015-11-25%2018_13_1.png)

HEADER
![](https://github.com/linkedin/ambry/wiki/images/image2015-11-25%2018_13_43.png)

### 安全 ###

Multi-tenancy is an essential requirement in any distributed system, and when multi-tenancy kicks in, security is more or less a must have requirement which any data system is expected to have

- Any interaction between frontend and servers and between servers(for replication purposes) should be encryptable if need be. We use SSL/TLS encryption for this purpose.
- Configs should be available to define which interactions should be enabled. By default no encryption should be enabled.
- Separate ports should be available on ambry-servers to speak plain text and SSL.

![](https://github.com/linkedin/ambry/wiki/images/SSL_Design1.png)


### Hard Delete ###

Currently, Ambry does not support compaction but provides the ability to hard delete. This is a common requirement for IDPC. The basic idea is to zero out the blobs on disk. This support will be replaced by the compaction support in the future.
