---
title: "redis入门实战之七:集群模式"
date: 2019-05-04T10:43:38+08:00
draft: true
---
&emsp;目前Redis主要的集群模式是Redis Cluster和Redis Sentinel两种模式。

## Redis Sentinel
&emsp;Redis Sentinel诞生于2012年，当时主要基于redis 2.6稳定版。它主要用来管理Redis实例。  
&emsp;它是一个独立运行的进程，它能监控你的master节点和slave节点。它会不时地监控redis是否照常预期良好的运行，当你的master节点宕机后，它会通知其他节点，并进行自动切换。它主要采用选举的方式将其中的一个salve节点推举成为master节点。  
&emsp;Redis Sentinel可以管理多个master-slave节点。但是如果Sentinel是单节点的情况下，就会存在单点故障。所以，至少需要三个哨兵进程才能构成一个健壮的分布式系统来持续监控Redis主实例状态。  

## Redis Cluster
&emsp;Redis Cluster是Redis的分布式解决方案，在Redis 3.0版本正式推出的。当Redis中的数据量急剧增长时，必须对其进行分区。  
&emsp;Redis Cluster采用虚拟槽分区，虚拟槽分区巧妙地使用了哈希空间，使用分散度良好的哈希函数把所有的数据映射到一个固定范围内的整数集合，整数定义为槽（slot）。比如Redis Cluster槽的范围是0 ～ 16383。槽是集群内数据管理和迁移的基本单位。采用大范围的槽的主要目的是为了方便数据的拆分和集群的扩展，每个节点负责一定数量的槽，具体如下所示：  
![Redis虚拟槽](../images/redis/Redis虚拟槽.png)
## 相关资料
关于Redis Sentinel集群相关文档:https://redis.io/topics/sentinel。  
关于Redis Cluster集群相关文档:https://redis.io/topics/cluster-tutorial。  
https://blog.csdn.net/men_wen/article/details/72853078 