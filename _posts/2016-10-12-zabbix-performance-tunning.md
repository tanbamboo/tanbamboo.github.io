Zabbix Performance Tuning

[TOC]

# KPIs

指标一:NVPS，New Values Per Second

指标二:UP，Update Percent

# Factors about Performance

| 因素         | 慢                               | 快                  |
| ---------- | ------------------------------- | ------------------ |
| 数据库大小      | 巨大                              | 适应内存大小             |
| 触发器表达式的复杂度 | min(),max(),avg()               | last(),nodata()    |
| 数据收集方法     | Polling(SNMP,No Proxy,被动式Agent) | Trapping(主动式Agent) |
| 数据类型       | 文本，字符串                          | 数值                 |

Fig 1: 	影响性能因素




1. 监控项类型,值类型,SNMPv3,触发器数量和触发器类型
2. Housekeeper 设置
3. 数据库大小
4. 前端用户数量
5. 触发器复杂性
6. 监控主机数量

zabbix数据库共有73个表加上临时表和mysql表

# Database

ZABBIX works at least 1.5 times faster (comparing to MyISAM) if InnoDB is used.

'history', 'history_str, 'items' 'functions', triggers', and 'trends' are most heavily used tables.

for large installations, keeping of MySQL temporary files in tmpfs is recommended

tune 'Update interval' for all items. Keeping a small update interval may be good for nice graphs, however, this may overload Zabbix

avoid the use of triggers with long period given as function argument. For example, max(3600) will be calculated significantly slower than max(60).

# Viewing Zabbix process performance with "ps" and "top"

Since Zabbix 2.2 processes change their commandlines to display current activity and meaningful statistics

On Linux systems ps command can be used together with watch command for observing how Zabbix is doing. For example, to run ps command 5 times per second to see process activities:

```shell
watch -n 0.2 ps -fu zabbix
```

To show only Zabbix proxy and agent processes:

```shell
watch -tn 0.2 'ps -f -C zabbix_proxy -C zabbix_agentd'
```

To show only history syncer processes:
```shell
watch -tn 0.2 'ps -fC zabbix_server | grep history'
```
The ps command produces a wide output (approximately 190 columns) as some activity messages are long. If your terminal has less than 190 columns of text you can try
```shell
watch -tn 0.2 'ps -o cmd -C zabbix_server -C zabbix_proxy -C zabbix_agentd'
```
to display only commandlines without UID, PID, start time etc.

top command also can be used for observing Zabbix performance. Pressing 'c' key in top shows processes with their commandlines. In our tests on Linux top and atop correctly displayed changing activities of Zabbix processes, but htop was not displaying changing activities.

# 使用分区表并且禁用 HouseKeeper

HouseKeeper降低了MySQL 的性能,因此一个简单的替代就是使用MySQL 分区本地资源。

Zabbix 的每个监控项都会拥有自己的housekeeper 值,正是这个特点使得进程 housekeeping成为性能杀手,因为delete_history()函数在每个监控项每次运行时都会执行DELETE。如果你有100k的监控项,就会执行100K的DELETE 查询。尤其是对于innode引擎,问题将会很严重,因为对于大表的DELELTE 非常慢而且删除行并不能释放磁盘空间。所以建议禁用housekeeper并对表进行分区。

# Reference

[[10 Performance tuning](https://www.zabbix.com/documentation/3.4/manual/appendix/performance_tuning)] 

http://junqilee.com/zabbix/zabbix-performance-tunning/

```

```