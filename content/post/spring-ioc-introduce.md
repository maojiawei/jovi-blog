---
title: "Spring IoC介绍"
date: 2019-10-25T17:18:30+08:00
draft: false
tags: ["spring"]
categories: "microservice"
toc: true
---
### 1.0 Spring IoC是什么？
&emsp;Spring Framework 是java常用的轻量级全栈引用程序框架。其中Spring最核心的思想就是Spring IoC（Inversion of Control），中文称为控制反转。

&emsp;在介绍控制反转之前，我们先来看看如果是一个传统的面向对象的情况。我们假定一个用户类（User）和一个订单类（Order），订单类依赖于用户类。按照下图来说，我们需要经历以下操作：

1. 创建订单类
2. 创建用户类
3. 将用户类主动注入到订单类

![传统程序](../images/spring/传统程序.jpg)

&emsp;在Spring中，提出了IoC的思想:借助于“第三方”实现具有依赖关系对象之间的解耦。我们可以将类以及类的相关依赖提交给Spring的Ioc容器，当用户需要使用的Spring容器就会给我们需要的对象。

![Spring的Ioc容器](../images/spring/spring-ioc.png)

&emsp;Spring Ioc很多人会将其与依赖注入(Dependency Injection)联系起来。因为依赖注入是Ioc中最重要的一环，就是描述对象。

- 在依赖注入中，我们不是创建好对象，再将其注入进去。而是定义好哪些组件需要哪些服务。
- Ioc容器会将其装配在一起。

### 2.0 依赖注入方式

&emsp;一般来说，依赖注入的话有三种方式。

- 接口注入
- 构造函数注入
- setter方法注入

目前，在Spring框架中，只支持构造方法与setter注入这两种方式。

### 3.0 IoC 的好处

- 减小代码量
- 以最小的影响和最少的侵入机制促进松耦合
- 即时的实例化和延迟加载 Bean 对象




