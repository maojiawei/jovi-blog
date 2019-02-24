---
title: "redis入门实战之二:安装与使用"
date: 2019-02-19T18:31:35+08:00
draft: true
tags: ["redis"]
categories: "microservice"
---
本篇文章主要介绍redis的安装以及相关的使用。
## 1.0 安装

### 1.1 docker安装(推荐)

&emsp;本文推荐使用docker容器的方式进行redis的安装，首先有一台安装docker的mac或者linux服务器上。  
(1) **拉取镜像**  
   在命令行中输入以下命令，可以从docker仓库(默认:http://hub.docker.com)中拉取redis镜像。目前最新的redis官方的最新版本为5.0.3，我们在仓库中查找最新的稳定版本。
   ```shell
   docker pull redis:5.0.3-alpine
   ```
   命令说明:  
   docker pull 为docker拉取镜像的命令  
   redis:5.0.3-alpine为镜像名称及版本号,一般为**镜像:版本号**。  
   运行结果如下:  
   ![image-20190223153607605](../images/redis/docker-pull-redis.jpg)

(2) **启动容器**  
