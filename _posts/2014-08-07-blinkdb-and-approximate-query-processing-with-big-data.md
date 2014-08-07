---
layout: post
title: "BlinkDB and Approximate Query Processing with Big Data"
description: ""
category: [hadoop]
tags: [spark, blinkdb, hadoop]
---
{% include JB/setup %}

最近在处理产品中的统计分析准确性问题。
在海量数据的情况下，想同时满足实时性和准确性，的确很困难。

# Spark内存计算框架

Spark[1](http://spark.io)是一个很有意思的框架，在Hadoop的生态系统中脱颖而出，这几年风头正劲。
RDD是一个很好的概念，也借用了一下。

# BlinkDB

在了解Spark的时候，发现AmpLab还有其他几个作品，其中一个很有意思的就是BlinkDB，是一个近似查询处理框架，核心的思想是对查询的数据进行取样处理。

数据取样是统计学中很核心的概念，貌似水夜很深，各种取样算法不可枚举。

BlinkDB的特点在于提供了查询的时间约束、误差约束、可信度约束。在这些查询约束下，选择最适合的样本查询处理。

BlinkDB的效率号称是10-150x，这个太牛了，特别是在7.5T这样越大的数据集上，即使Spark的缓存也不好使了。

BlinkDB的主要组件，摘自[Blink and It’s Done: Interactive Queries on Very Large Data](http://www.cs.berkeley.edu/~sameerag/blinkdb_vldb12_demo.pdf)

BlinkDB adds two major components: (1) a component to create and maintain samples, (2) a component for predicting the query response time and accuracy and selecting a sample that best satisfies given constraints.

# BlinkDB参考资料
[BlinkDB官网](http://blinkdb.org)
[Blink and It’s Done: Interactive Queries on Very Large Data](http://www.cs.berkeley.edu/~sameerag/blinkdb_vldb12_demo.pdf)
[BlinkDB: Queries with Bounded Errors and Bounded Response Times on Very Large Data](http://www.cs.berkeley.edu/~sameerag/blinkdb_eurosys13.pdf)
