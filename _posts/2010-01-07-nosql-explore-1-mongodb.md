---
layout: post
title: "NoSQL探索1：认识MongoDB"
tagline: ""
categories : [develop]
tags: [nosql, mongodb]
---
{% include JB/setup %}


## 基本特性

直接引用官网描述

Combining the best features of document databases, key-value stores, and RDBMSes.

MongoDB (from "humongous") is a scalable, high-performance, open source, schema-free, document-oriented database. Written in C++, MongoDB features:


Document-oriented storage (the simplicity and power of JSON-like data schemas)


Dynamic queries


Full index support, extending to inner-objects and embedded arrays


Query profiling


Fast, in-place updates


Efficient storage of binary data large objects (e.g. photos and videos)


Replication and fail-over support


Auto-sharding for cloud-level scalability


MapReduce for complex aggregation


Commercial Support, Hosting, and Consulting


MongoDB bridges the gap between key-value stores (which are fast and highly scalable) and traditional RDBMS systems (which provide structured schemas and powerful queries).


## 索引方面


“Formally speaking, these indexes are implemented as "B-Tree" indexes.”


在索引方面，采用的还是B-Tree索引的，无法多索引做交集运算(intersect)，无法满足我的多字段组合查询的需求。


只能说符合了Schemaless, Key-Value store两个特点，加上基于索引的查询接口。


## 分布式架构


分布式特性、Replication、Fail-over、MapReduce这几个还是比较有价值的。

#### 后记

于2013.01.06 &raquo; 
迁移自[tanbamboo.blogspot.com博客](http://tanbamboo.blogspot.com/2010/01/nosql1mongodb.html)
