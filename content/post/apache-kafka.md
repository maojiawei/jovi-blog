---
title: "Apache Kafka:下一代分布式消息系统"
date: 2019-01-15T10:59:14+08:00
draft: false
tags: ["kafka"]
categories: "bigdata"
---
在当今世界，数据是互联网的主要构成要素，它包含了以下内容：

* 页面访问和用户点击
* 用户行为
* 登录相关事件
* 社交活动，例如：点赞，分享和评论等
* 应用特定指标，例如：日志，页面加载事件和行为等

这些数据可以用来实时处理分析，比方说：

* 投放广告
* 非正常用户行为跟踪
* 相关性搜索
* 根据之前的行为作个性化推荐

<b>问题：</b>收集数据不是一个容易的事情，因为数据有不同的类型，以不同的方式产生。  
<b>解决方案：</b>有一种方式可以解决这个问题：那就是使用消息系统。消息系统可以通过消息在各种分布式应用之间提供无缝整理。  

## Apache Kafka
Apache Kafka是一个分布式消息订阅系统，它最高由LinkedIn开发，而后成为了Apache项目的一部分。Kafka是一个快速的，敏捷的，易拓展的分布式系统。  

## 架构与术语
![架构](../images/kafka/kafka-cluster.jpg)
<b>Topic：</b>topic是一个特别的目录用于存放一系列的消息。  
<b>Producer（生产者）：</b>一个Producer可以是任意一个应用，它负责发送消息给topic。  
<b>Consumer（消费者）：</b>一个Consumer可以是任意一个应用，它负责订阅topic，消费消息。  
<b>Broker：</b>Kafka集群是有一系列服务组成，这些服务就是Broker。  

Kafka是易拓展的，它可以有多种类别的集群方式：

* 单节点单broker集群
* 单节点多broker集群
* 多节点多broker集群
![单节点单broker](../images/kafka/single-node-single-broker.jpg)

## zookeeper的角色
每个kafka的broker都可以通过zookeeper与其他broker协作。Zookeeper服务会通知Producer与Consumer关于新的broker服务加入或者旧的broker服务挂机的位置。
![单节点多broker](../images/kafka/single-node-multiple-brokers.jpg)
![多节点多broker](../images/kafka/multiple-node-multiple-broker.jpg)

## 其他使用kafka
<b>DataSift:</b> DataSift使用kafka作为监控数据收集以及用户消费数据的实时跟踪。
<b>Wooga:</b>Wooga使用kafka来收集和处理所有他们在Facebook上的小游戏数据。
<b>Spongecell:</b>Spongecell使用kafka来运行整个分析和监控工作流，包括实时和ETL 应用。
<b>Loggly:</b>Loggly是世界上最流行的基于云端的日志管理平台。它使用Kafka来作日志收集。

## 对比研究
Kafka与ActiveMQ与RabbitMQ
Kafka有最高效的存储格式。平均来说，每个消息在kafka中有9字节的开销，而在ActiveMQ中有144字节的开销。
![不同队列生产消息](../images/kafka/cmp1.png)
在ActiveMQ以及RabbitMQ中，broker包含写入磁盘来维护每个消息的传递状态，而在Kafka中没有磁盘的读写，因此更快。
![不同队列生产消息](../images/kafka/cmp2.png)

注：这篇文章翻译自:https://www.edureka.co/blog/apache-kafka-next-generation-distributed-messaging-system
