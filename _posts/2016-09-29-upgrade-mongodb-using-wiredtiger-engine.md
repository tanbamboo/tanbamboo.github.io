---
layout: post
title: "MongoDB的WiredTiger引擎"
tagline: ""
categories : [nosql]
tags: [mongodb]
---
{% include JB/setup %}

[TOC]

# WiredTiger引擎是个什么鬼？

## Document Level Concurrency

WiredTiger uses *document-level* concurrency control for write operations. As a result, multiple clients can modify different documents of a collection at the same time.

WiredTiger uses only intent locks at the global, database and collection levels. When the storage engine detects conflicts between two operations, one will incur a write conflict causing MongoDB to transparently retry that operation.

# 升级到WiredTiger引擎

## Standalone模式升级

Standalone模式进行引擎升级，需要中断服务；中断时间的长短，取决于数据量和系统性能。

- 执行mongodump备份数据
- 停止mongod服务
- 为WiredTiger引擎准备新的数据路径
- 配置以WiredTiger引擎模式启动mongod服务，确保使用的是新的数据路径
- 执行mongorestore从备份中恢复数据

## Replica Set模式升级

Replica Set模式进行引擎升级，采用Rolling方式，可以不中断服务完成；升级过程中需要重新进行数据复制，会对系统性能和网络带宽等产生比较明显的影响。

- 从Secondary节点开始升级

- mongo直接连接到Secondary节点上，执行db.shutdownServer()停止Shard服务

- 为WiredTiger引擎准备新的数据路径，原MMAPv1引擎的数据可以在升级完成后清理掉。

- 配置以WiredTiger引擎模式启动mongod服务，确保使用的是新的数据路径

  ```shell
  mongod -shardsvr -replSet test -port 27017 --storageEngine wiredTiger --directoryperdb --wiredTigerDirectoryForIndexes -dbpath /usr/local/var/mongodb -logpath /usr/local/var/log/mongodb/mongo_rs_test.log 
  ```

- 启动重新加入Replica Set后，由于没有数据，会重新进行数据同步(initial sync)。

- 重复如上操作，直至Replica Set每个节点升级完成。

## Sharding Cluster升级

Sharding Cluster中，可以逐个Shard进行升级，根据Shard的类型，可以按照上面的Standalone、Replica Set模式进行升级。

# 进行Collection升级

# 性能测试

## 测试环节

## 测试步骤

## 监控指标

## 测试结果