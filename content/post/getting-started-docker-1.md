---
title: "docker入门实战之一:入门篇"
date: 2019-08-17T15:30:03+08:00
draft: false
tags: ["docker"]
categories: "container"
toc: true
---
本篇文章作为docker入门实战的第一课，将会为大家介绍关于docker的基本概念。  
### 1.0 介绍
&emsp;Docker是以Docker容器为资源分割和调度的基本单位，封装整个软件运行时环境，为开发者和系统管理员设计的，用于构建、发布和运行分布式应用的平台。它是一个跨平台、可移植并且简单易用的容器解决方案。Docker可移在容器内部快速自动化地部署应用，并通过操作系统内核技术（namespace、cgroups等）为容器提供资源隔离与安全保障。

### 2.0 Docker与虚拟机
#### 虚拟机
&emsp;虚拟机是一种基于硬件的虚拟化技术，每个虚拟机对应的都是一整套的物理主机，包括:CPU、内存、磁盘、网卡等设备。  
#### Docker
&emsp;Docker容器技术是基于操作系统的虚拟化技术，所有的容器与宿主机公用一个系统内核。相比虚拟机，容器引擎提供的是进程级别的隔离。  

&emsp;基础层
![Docker与虚拟机](../images/docker/docker与虚拟机.png)
&emsp;