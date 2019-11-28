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
&emsp;Application Service相当于服务提供者的角色。

### 2.2 Application Client
&emsp;Application Client相当于服务消费者的角色。



## 3.0 区域



## 4.0 功能

### 4.1 集群数据同步



### 4.2 服务注册



### 4.2 服务续约



### 4.3 服务下线



### 4.4 获取服务注册列表



### 4.5 远程调用



