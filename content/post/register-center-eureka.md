---
title: "服务注册中心: Eureka"
date: 2019-11-28T09:07:42+08:00
draft: true
tags: ["Eureka"]
categories: "microservice"
toc: true
---
&emsp;我们在微服务架构中提到过[服务注册中心](http://jovi.io/post/getting-started-microservice-3/)。在微服务架构中，服务注册中心扮演着至关重要的作用，因为他相比于一个手机通讯录，当手机通讯录出现问题的时候，我们无法联络上希望联络的人。在微服务中，服务也无法正常连接。

&emsp;Eureka是Netflix开源的服务发现组件，主要基于REST( Representational State Transfer )服务，在Netflix中主要以AWS云服务为支撑，提供服务发现并实现负载均衡和故障转移。

## 1.0 架构图

&emsp;我们首先来看Eureka官方wiki的一张架构图。

![Eureka架构图](../images/eureka/eureka_architecture.png)

&emsp;后面将以角色、区域和功能一一拆解这张架构图。

## 2.0 角色

### 2.1 Eureka Server
&emsp;Eureka Server表示服务注册中心，他们负责记录下所有服务的注册信息。同时Eureka Server本身支持支持分布式集群的方式，多台Eureka Server 之间也会进行数据同步。

### 2.2 Application Service
&emsp;Application Service相当于服务提供者的角色。服务提供者将自身的服务提交至注册中心，并等待服务消费者来调用。

### 2.2 Application Client
&emsp;Application Client相当于服务消费者的角色。服务消费者向注册中心获取所有服务注册的列表，并根据需要向服务提供者发送请求。

## 3.0 区域

&emsp;在Eureka中有区域的概念，正如架构图中的`us-east-1c`、`us-east-1d`和`us-east-1e`这三个区域。这是由于对于大的平台来说（例如：亚马逊、阿里等），一般会存在多个机房。当服务消费者调用时，会先调用同一个机房内的服务，再去调用其他机房的服务，以达到减少延时的作用。

&emsp;Eureka提供了region和zone两个概念：

- **region:** 这个可以理解为地理上的分区，例如：北京地区，上海地区，香港地区等等。这个没有具体的限制，可以根据实际情况，进行划分。region可以通过配置文件进行配置，在不配置的情况下，会默认使用us-east-1。
- **zone:** region作为地区的划分，zone可以认为是机房的划分。例如：上海一区（陆家嘴机房）和上海二区（外高桥机房）等等。 Zone也可以配置，在不配置的情况下，会默认使用defaultZone。 


## 4.0 功能

### 4.1 集群数据同步

&emsp;当Eureka Server重启或者新的Eureka Server节点加进来的时候，会执行初始化，从集群其他节点中获取所有的实例注册信息，从而能够正常提供服务。

&emsp;Eureka Server之间的状态同步都是异步方式的，所以不保证节点间的状态一定是一致的，不过基本能保证最终状态一致性。

### 4.2 服务注册

&emsp;服务提供者将自身的服务信息（例如： 服务名称、实例名称、实例IP、实例端口等）注册至Eureka Server中。

&emsp;相关参数：

&emsp;` eureka.client.fetchRegistry=true ` 该参数表示会在本地缓存所有的实例注册信息。

&emsp;` eureka.client.registry-fetch-interval-seconds ` 该参数表示向注册中心更新实例的时间，默认30s。

### 4.2 服务续约

&emsp;已注册的服务需要通过心跳定时向Eureka Server续约以更新自己的状态。

&emsp;相关参数:

&emsp;` eureka.instance.lease-renewal-interval-in-seconds `该参数定义心跳的频率，默认30s。

### 4.3 服务下线

&emsp;服务的下线有两种：

- 服务提供者在shutdown之前，主动通知Eureka把自己剔除，避免客户端调用已下线的服务。
- Eureka Server会定时进行检查，如果发现实例在在一定时间内没有收到心跳，则会注销此实例。

&emsp;相关参数:

&emsp;`eureka.server.eviction-interval-timer-in-ms` Eureka清理无效节点的间隔时间。默认为60s。

&emsp;`eureka.instance.lease-expiration-duration-in-seconds`超过这个时间点没有心跳，注销实例。默认90s。

### 4.4 获取服务注册列表

&emsp;服务的消费者会定时向注册中心拉取服务注册信息列表。

&emsp;相关参数:

&emsp;` eureka.client.registry-fetch-interval-seconds ` 该参数可以设置拉取服务注册信息的时间，默认30s。

### 4.5 自我保护模式

&emsp;根据服务下线，Eureka会自动剔除没有心跳的节点。但是当Eureka Server在短时间内失去过多客户端的时候，那么借点会进入自我保护模式。Eureka Server会保护注册表中的信息，不在删除注册表中的节点。当网络故障恢复之后，会自动退出自我保护模式。

&emsp;相关参数:

&emsp;`eureka.server.enable-self-preservation`自我保护模式，默认true。

### 4.6 远程调用

&emsp;远程调用是有Eureka的服务提供客户端与服务消费客户端时间发送的交互，详细可以了解[远程服务调用]( http://jovi.io/post/getting-started-microservice-4/ )。

