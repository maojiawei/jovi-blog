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

&emsp;以下是Docker与虚拟机在架构方面的差别，左边为虚拟机，右边为docker容器：

![Docker与虚拟机](../images/docker/docker与虚拟机.png)
&emsp;虚拟机和容器都是在硬件和操作系统之上的，容器与虚拟机的差别就在于虚拟机的Hypervisor、Guest OS 与Docker容器的Docker Engine。

&emsp;在虚拟机中，Hypervisor是核心所在，它为虚拟机提供了虚拟化的运行平台，以此来虚拟化CPU、内存、IO设备等。Guest OS是一个完全独立的组件，主要用于实现环境隔离。

&emsp;而对于容器而言，容器通过自身的Docker Engine取代了Hypervisor和Guest OS。(Docker不需要Hypervisor实现硬件资源虚拟化，它直接使用实际物理机的硬件资源。Docker利用的是宿主机内核，也不需要Guest OS。)Docker Engine对Linux的NameSpace、Cgroup、镜像管理文件系统操作的封装。Namespace实现了系统环境的隔离，Cgroup实现了资源限制，镜像实现了根目录环境的隔离。

&emsp;Docker在各方面对比虚拟机总结:

|特性|容器|虚拟机|
|----|----|------|
|启动|秒级|分钟级|
|硬盘占用|一般为MB|一般为GB|
|性能|接近原生|弱于原生|
|系统支持度|单机支持上千个容器|一般几十个|

### 3.0 Docker优势

### 4.0 Docker基本概念
&emsp;Docker包括三个基本概念:

- 镜像(Image)
- 容器(Container)
- 仓库(Repository)

#### 4.1 镜像
&emsp;Docker把应用程序及其依赖，打包在镜像文件里面。Docker会根据镜像生成容器的实例，镜像就是生成容器的模板。同一个镜像，可以生成多个同时运行的容器。

&emsp;镜像本质上是一个特殊的文件系统，它可以提供容器运行时所需要的程序、库、资源和配置等文件，还包括了一些运行时所需要的配置参数（例如：数据卷、环境变量、用户等）。

&emsp;Docker利用Union File System 以构建镜像。镜像构建时，会一层层构建，前一层是后一层的基础。