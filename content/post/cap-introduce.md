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
- `Partition tolerance（分区容错性）`: 在分布式系统中，如果集群中的机器分成了两部分，两部分不能互相通信，系统仍然能够继续工作。

### 2.0 CAP定理论证
&emsp;我们来证明下，为什么不能同时满足三个特性？
&emsp;假设网络中有两个节点，分别为G1和G2，节点中存在两个应用(A1与A2)以及两个相同的数据(D1)。
![网络节点分布](../images/distributed/网络节点分布.jpg)
&emsp;在正常情况下，
