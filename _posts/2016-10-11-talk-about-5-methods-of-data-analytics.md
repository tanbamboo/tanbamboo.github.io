闲扯数据分析的五种方式

[TOC]



# MapReduce查询引擎

(Sequence Scan模式)

# 列存储OLAP引擎

(又叫Cube方式，采用列存储)

如Apache Druid、InfluxDB等

# 倒排索引＋DocValue

DocValue是一种列存储方案

如Elasticsearch

# TSD分析

比如OpenTSDB这种

最早的包括RddTool等。

# 物化视图的Cube引擎

比如Apache Kylin

# Splunk模式

全文索引＋动态Pipeline处理

# 如何选择

当然，一切取决于两点：

- 你有什么样的数据
- 你想要做什么样的分析