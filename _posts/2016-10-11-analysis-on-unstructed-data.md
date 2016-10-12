非结构化数据上进行结构化查询的方法

[TOC]

# 目标

灵活：强大的分析能力

高效：满足交互式分析的时效性要求，以及降低系统资源消耗。

# 技术方案

主要包括四个部分部分：

## 查询条件Pushdown优化

涉及到查询计划(QL)的选择优化，不知道是不是类似Calcite的基于成本的优化(Cost-based Optimize).

如果没有Pushdown优化，基本上就是原始的MR计算模式。

Pushdown优化可以分为两级：

- Partition/Segment/Region/Chunk层级的过滤，可以考虑采用的技术包括：
  - 布隆过滤器(Boolean Filter)
  - 分区(Partitioning、Sharding)
  - Min-Max Filter
- 存储引擎层级的过滤，比如用到了全文索引、主键索引、二级索引、列存储：
  - 可以进行Query Rewrite，进行Pushdown。

这两级过滤都不支持的部分，就只能逐条处理，在Map阶段进行处理了。

比如PostgreSQL的pg_trgm索引、Regex Search Index技术都是采用了Query Rewrite技术；Google Code Search项目的Regex搜索也是采用了Query Rewrite技术。

## 动态字段提取

由于这个需求的存在，列存储方案(如Apache Druid、InfluxDB)无法满足需求。

列存储的前提在于，数据已经前置处理好成为结构化数据(一般通过ETL、流计算等方式)。

Hive的UDF可以某种程度上实现这个，不过灵活性比较差。

## Pipeline式处理流

算是MR的多Stage优化，比如Storm的Topology、Spark的DAG、Tez的DAG等。

## 基于Segment的MR式分布式计算

也就是支持Scater/Gather计算模式，从而发挥分布式并行计算的能力。

必须把计算(也就是执行计划Query Plan)推送到数据节点上，然后汇总数据；相反的，如果把数据汇总再统一处理，是不可行的。

# 技术难点

# 架构设计

## 基于ES的方案

## 基于Kafka的方案

## 基于CarbonData的方案

# 参考资料

[1] Splunk专利US8751486