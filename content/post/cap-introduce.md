---
title: "CAP介绍"
date: 2019-09-04T22:20:31+08:00
draft: true
tags: ["分布式"]
categories: "microservice"
toc: true
---
&emsp;现在分布式系统越来越成为企业主流的架构模式。分布式系统的最大难点，在于各个节点的状态如何同步，CAP是该方面的基本定理。

### 1.0 三个指标
&emsp;CAP定理由三个指标组成，指的是一个分布式系统中，Consistency（一致性）、 Availability（可用性）、Partition tolerance（分区容错性），三者不可得兼。

- `Consistency（一致性）`: 在分布式系统中所有的数据备份，在同一时刻进行读操作都会返回同样的值。
- `Availability（可用性）`: 在分布式系统中，一部分节点故障后，是否还能响应客户端的读写请求。
- `Partition tolerance（分区容错性）`: 