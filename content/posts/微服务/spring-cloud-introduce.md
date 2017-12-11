---
title: "Spring Cloud 介绍"
date: 2017-12-11T22:46:33+08:00
draft: true
---
## 简介
Spring cloud是目前最流行的微服务框架。它是在spring boot的基础上构建的，用于快速构建分布式系统的通用模式的工具集。Spring Cloud 包含了许多子项目，提供了一些工具来快速构建分布式系统中一些常用模式，分布式配置管理、服务注册和发现、断路器、智能路由、全局锁等。  
## 与dubbo对比
很多人会说Spring Cloud和Dubbo的对比有点不公平，Dubbo只是实现了服务治理，而Spring Cloud下面有17个子项目（可能还会新增）分别覆盖了微服务架构下的方方面面，服务治理只是其中的一个方面。当然dubbo也有好消息，就是阿里巴巴开始重新维护dubbo了，但以目前来说，spring cloud更全面。以下是对spring cloud与dubbo的全面对比。

| 	功能    |dubbo	|spring Cloud|
|-------|---------|----|
|服务注册中心 |Zookeeper|	Spring Cloud Netflix Eureka|
|服务调用方式 |	RPC	REST |API|
|服务网关	   |无	|Spring Cloud Netflix Zuul|
|断路器	   |不完善	|Spring Cloud Netflix Hystrix|
|分布式配置   |无	|Spring Cloud Config|
|服务跟踪    |	无	|Spring Cloud Sleuth|
|消息总线	   |无	|Spring Cloud Bus|
|数据流	   |无	|Spring Cloud Stream|
|批量任务     |	无	|Spring Cloud Task|
|……	|……	|……|

## 组件
 spring cloud的组件详情如下图所示。
![Spring cloud组件](../images/springcloud.png)
