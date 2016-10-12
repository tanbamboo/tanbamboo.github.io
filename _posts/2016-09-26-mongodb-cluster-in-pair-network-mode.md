---
layout: post
title: "对等网络模式部署MongoDB集群"
tagline: ""
categories : [nosql]
tags: [mongodb, cluster, nosql]
---
{% include JB/setup %}

对等网络模式部署MongoDB集群

[TOC]

# MongoDB的Replica Set概念

**在MongoDB中，Shard负责数据分区，Replica Set负责数据冗余和HA，两者是相互独立的，可以相互配合。**

A MongoDB sharded cluster consists of the following components:

- shard: Each shard contains a subset of the sharded data. Each shard can be deployed as a replica set.
- mongos: The mongos acts as a query router, providing an interface between client applications and the sharded cluster.
- config servers: Config servers store metadata and configuration settings for the cluster. As of MongoDB 3.2, config servers can be deployed as a replica set.

A replication topology where only a single database instance accepts writes. 

Single-master replication ensures consistency and is the replication topology employed by MongoDB.

A replica set in MongoDB is a group of mongod processes that maintain the same data set. Replica sets provide redundancy and high availability, and are the basis for all production deployments.

- Replica Set: A cluster of MongoDB servers that implements master-slave replication and automated failover. MongoDB’s recommended replication strategy.

Replication Lag：复制延迟

# 部署集群

## 启动Shard Server

启动Shard Server 1:

```shell
mongod -shardsvr -replSet rs1 -port 27017 -dbpath /usr/local/var/mongodb/shard11 -logpath /usr/local/var/log/mongodb/shard11.log
```

启动Shard Server 2:

```shell
mongod -shardsvr -replSet rs1 -port 27018 -dbpath /usr/local/var/mongodb/shard12 -logpath /usr/local/var/log/mongodb/shard12.log
```

如果要通过配置文件的方式来启动mongod，可以在mongod.conf配置文件中添加Replica Set相关配置，如：

```yaml
replication:
   replSetName: <Replica Set Name>
   #oplogSizeMB: <int>
   #secondaryIndexPrefetch: <string>
   #enableMajorityReadConcern: <boolean>
```

然后启动mongod，执行：

```shell
mongod --config /usr/local/etc/mongod_replicaset.conf
```

## 配置Replica Set

Mongo连接到任意一个Shard Server节点，可以进行RepliSet配置。

```shell
config = {id:"rs1", members: [
  {_id:0, host:'127.0.0.1:27017'},
  {_id:0, host:'127.0.0.1:27018'}
]}
rs.initiate(config)
```

## 启动Config Server

```shell
mongod -configsvr -dbpath /usr/local/var/mongodb/config -port 20000 -logpath /usr/local/var/log/mongodb/config.log
```

根据MongoDB的官方文档，建议采用Replica Set模式部署Config Server，也即是CSRS模式。

如果通过配置文件启动Config Server，可以在configserver.conf添加如下配置：

```

```

## 启动Mongos:

```shell
mongos -configdb 127.0.0.1:20000 -port 30000 -chunkSize 5 -logpath /usr/local/var/log/mongodb/mongos.log
```

如果通过配置文件启动mongos，可以在mongos配置文件中设置

```yaml
replication:
  localPingThresholdMs: 15 #default 15 millisecond

sharding:
  autoSplit: true
  configDB: test/127.0.0.1:27017
  chunkSize: 64 #default 64MB
```

# 测试HA

## 新增Shard Server节点

启动Shard Server 3:

```shell
mongod -shardsvr -replSet rs1 -port 27019 -dbpath /usr/local/var/mongodb/shard13 -logpath /usr/local/var/log/mongodb/shard13.log
```

连接到任意一个Shard Server节点，可以进行RepliSet配置更新。执行如下命令可以给ReplSet新增加Shard节点

```shell
rs1:SECONDARY> rs.add({_id:3, host:'127.0.0.1:27019'}
```

加入后，会自动进行数据复制。

## 当Primary Shard Server挂掉

停掉Shard Server 1后，另外两个Replication节点会自动进行选举，选出新的Primary节点。

通过Mongos访问的会话，运行正常；

可以进行Collection查询操作、插入操作、修改操作。

PS1. 直接连接到Shard Server 1的Admin会话会出现异常提示：

```shell
> rs.status()
2016-09-26T16:45:19.263+0800 I NETWORK  trying reconnect to 127.0.0.1:27017 (127.0.0.1) failed
2016-09-26T16:45:19.264+0800 W NETWORK  Failed to connect to 127.0.0.1:27017, reason: errno:61 Connection refused
2016-09-26T16:45:19.264+0800 I NETWORK  reconnect 127.0.0.1:27017 (127.0.0.1) failed failed couldn't connect to server 127.0.0.1:27017 (127.0.0.1), connection attempt failed
2016-09-26T16:45:19.265+0800 E QUERY    Error: socket exception [CONNECT_ERROR] for 127.0.0.1:27017 (127.0.0.1) failed

at DBQuery._exec (src/mongo/shell/query.js:83:36)
at DBQuery.hasNext (src/mongo/shell/query.js:240:10)
at DBCollection.findOne (src/mongo/shell/collection.js:187:19)
at DB.runCommand (src/mongo/shell/db.js:58:41)
at DB.adminCommand (src/mongo/shell/db.js:65:21)
at Function.rs.status (src/mongo/shell/utils.js:937:37)
at (shell):1:4 at src/mongo/shell/query.js:83
```

这个时候重新连接到可用的mongod节点，即可进行Replica Set管理操作。

## 当两个节点Down掉之后

Primary节点也变成了Secondary，导致没有Primary，所以无法进行访问，会出现如下异常信息：

```shell
mongos> db.c1.find()
Error: error: {
       	"$err" : "ReplicaSetMonitor no master found for set: rs1",
       	"code" : 10009,
       	"shard" : "rs1"
}
```

必须确保Replica Set还有个活跃的大多数，否则Primary节点会退下来。参见MongoDB官方文档：

> ensure that the set has an active majority or the primary will step down.

## Down掉的节点重新加入

重新加入后，会进行数据同步复制。如果Replication Lag比较大、数据量比较大的时候，复制可能会需要比较多时间。

一般重新加入的节点会成为Replication。

## 添加Arbiter Member

```shell
mongod --port 27030 --dbpath /usr/local/var/mongodb/arbiter --replSet rs1
```

mongo连接到Primary节点，执行如下命令添加Arbiter节点

```shell
rs1:PRIMARY> rs.addArb("127.0.0.1:27030")
```

# 小结

在任何一个时刻，每个Replica Set都只有一个Primary用于写数据，和n个Secondary可用于读数据。在Replica Set模式下，无聊包括了多少个节点，也仅能达到Primary的写性能。



为了充分利用分布式多节点的性能，可以根据节点数进行Sharding规划；然后每个Shard采用Replica Set，然后让Collection按照Sharded Key的方式分布到多个Shard上，以提高集群整体的写性能。

根据MongoDB的官方文档，建议的是采用3节点部署Replica Set。

通过测试可以了解到，基本配置的RepliSet集群，需要3个节点才能确保在一个节点失效时，集群整体依然可用。

在两个节点组成的集群中，当一个节点失效后，集群将不可用，无法读写。但是在新增一个节点或失效节点重新加入后，集群能快速的恢复可用状态。

另外，MongoDB提供了Arbiter节点，仅参与Vote，不存储数据，不需要专用硬件，可以用于支持两个节点部署模式。参见MongoDB官方文档：

> An arbiter will always be an arbiter whereas a primary may step down and become a secondary and a secondary may become the primary during an election.

# 从Standalone升级到Replica Set

## 目标

最小化服务中断时间

## 备份数据

正所谓“有备无患”，首先是用mongodump进行数据备份。

## 启动Config Server 

RSCF，只有3.2以后才支持。

Mirror模式部署3台Config Server。

## 启动Mongos



## 停掉原来的mongod服务

从这个时候开始，系统中断服务。

## 以Replica Set模式启动Primary Shard节点

使用--replSet选项启动Shard Server，需要使用之前standalone方式已经存在的数据路径dbpath，执行

```shell
mongod -shardsvr -replSet rs1 -port 27017 -dbpath /usr/local/var/mongodb/shard11 -logpath /usr/local/var/log/mongodb/shard11.log
```

## Replica Set配置和初始化

mongo连接到Shard Server进行Replica Set配置和初始化，执行

```shell
rs.initiate()
```

添加新的Shard Server到Replica Set中，

## 在Mongos中添加Replica Set作为Shard

完成后，系统恢复服务。

## 为Replica Set添加Secondary Shard节点

可能会消耗比较多时间。。。

## 为Replica Set添加Arbiter节点

## 再起一个Mongos服务节点

# 升级踩的坑

## 时钟同步的要求

如果多个节点上时钟不同步，偏差较大，会出现如下问题：

```shell
[mongosMain] error upgrading config database to v6 :: caused by :: DistributedClockSkewed clock skew of the cluster 172.16.7.50:27019,172.16.7.57:27019,172.16.7.58:27019 is too far out of bounds to allow distributed locking.
```

## Mirror模式部署Config Server的问题



# 和Elasticsearch集群对比

Elasticsearch中，每个Index，根据配置会自动创建多个Shard，每个Shard会根据配置分配对应的Master和Slave。每个Elasticsearch服务进程，可以自动加入多个Shard中；

而MongoDB中，每个ShardServer的mongod进程，仅能加入一个Replica Set，如果要充分利用分布式性能，每个节点上需要运行多个mongod进程，当然带来的好处是多个进程间相互隔离。

比较来看，Elasticsearch集群配置和管理起来更简单方便；MongoDB集群需要比较多的配置操作，而且运维管理起来也相对比较困难。

# 延伸主题

## Read Operations or ReadPreference

By default, clients read from the primary [1]; however, clients can specify a read preference to send read operations to secondaries. Asynchronous replication to secondaries means that reads from secondaries may return data that does not reflect the state of the data on the primary. For information on reading from replica sets, see Read Preference.

## ReadConcern

In MongoDB, clients can see the results of writes before the writes are durable:

Regardless of write concern, other clients using "local" (i.e. the default) readConcern can see the result of a write operation before the write operation is acknowledged to the issuing client.
Clients using "local" (i.e. the default) readConcern can read data which may be subsequently rolled back.
For more information on read isolations, consistency and recency for MongoDB, see Read Isolation, Consistency, and Recency.

## Replica Set supported special members

Replica sets also support dedicated members for reporting, disaster recovery, or backup functions.

See Priority 0 Replica Set Members, Hidden Replica Set Members and Delayed Replica Set Members for more information.

## Stop Mongo DB Service by command

通过mongo连接到服务节点上， 使用admin数据库，执行shutdownServer()可以关闭服务。

但是如果是Replica Set的Primary节点，而且无法从Secondary选举出新的Primary，shtudownServer()操作会失败。这个很贴心，避免操作手误导致服务中断。

具体会出现如下的异常信息：

```shell
test:PRIMARY> db.shutdownServer()
assert failed : unexpected error: Error: shutdownServer failed: No electable secondaries caught up as of 2016-09-29T11:14:24.020+0800
Error: assert failed : unexpected error: Error: shutdownServer failed: No electable secondaries caught up as of 2016-09-29T11:14:24.020+0800
    at Error (<anonymous>)
    at doassert (src/mongo/shell/assert.js:11:14)
    at assert (src/mongo/shell/assert.js:20:5)
    at DB.shutdownServer (src/mongo/shell/db.js:212:9)
    at (shell):1:4
2016-09-29T11:14:24.021+0800 E QUERY    Error: assert failed : unexpected error: Error: shutdownServer failed: No electable secondaries caught up as of 2016-09-29T11:14:24.020+0800
    at Error (<anonymous>)
    at doassert (src/mongo/shell/assert.js:11:14)
    at assert (src/mongo/shell/assert.js:20:5)
    at DB.shutdownServer (src/mongo/shell/db.js:212:9)
    at (shell):1:4 at src/mongo/shell/assert.js:13
```

## auditLog

操作审计功能，此功能仅在MongoDB企业版可用。

auditLog.destination支持三种方式：

- Syslog
- Console
- File

auditLog.format支持两种格式：

- JSON
- BSON

auditLog.filter支持条件过滤

# 参考资料

https://docs.mongodb.com/manual/

http://www.open-open.com/lib/view/open1329745095312.html