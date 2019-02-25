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
   **docker pull**为docker拉取镜像的命令。  
   **redis:5.0.3-alpine**为镜像名称及版本号,一般为**镜像:版本号**。  
   运行结果如下:  
   ![拉取镜像](../images/redis/docker-pull-redis.jpg)

(2) **启动容器**  
    在命令行中输入以下命令，可以启动一个redis的docker容器。  
   ```shell
   docker run -d --name=redis -p 6379:6379 redis:5.0.3-alpine
   ``` 
   命令说明:  
   **docker run**为docker启动容器。  
   **-d**为后台启动程序。  
   **--name=redis**为docker容器名称。  
   **-p 6379:6379**为开放端口，一般命令为-p ${host_port}:${container_port}。其中${host_port}为宿主机端口,${container_port}为容器内部端口。  
   **redis:5.0.3-alpine**为镜像名称及版本号,一般为**镜像:版本号**。  
   运行结果如下:  
   ![启动容器](../images/redis/docker-run-redis.png)
   
(3) **检查容器**  
    在命令行中输入以下命令，查看容器是否正常运行。  
   ```shell
   docker ps -a
   ``` 
   命令说明:  
   docker ps -a 查看容器。  
   运行结果如下,如果存在redis且状态为Up，说明创建成功:  
   ![检查容器](../images/redis/docker-ps-a-redis.png)

(4) **进入容器**  
    在命令行中输入以下命令，可以进入容器。  
   ```shell
   docker exec -ti redis sh
   ``` 
   命令说明:  
   **docker exec -ti**表示通过交互式方式进入容器。  
   **redis**是之前创建的容器名。  
   **sh**为进入的命令。  
   运行结果如下:  
   ![检查容器](../images/redis/docker-exec-redis.png)

### 2.0 操作
#### 2.1 客户端
&emsp;如果为容器启动，需要首先进入容器中(主机安装可不用)。然后可以通过以下命令，进入redis的客户端。
```shell
redis-cli -h ${host} -p ${port} -a ${password}
```
命令说明:  
**redis-cli**为进入redis客户端命令。  
**-h ${host}**中的${host}为需要访问redis服务端的ip或主机名，默认本机可省略。  
**-p ${port}**中的${port}为需要访问redis服务端的端口，默认端口可省略。  
**-a ${password}**中的${password}为需要访问redis服务端的密码，无密码可省略。  
运行结果如下:  
![redis客户端](../images/redis/redis-cli.png)

#### 2.2 常用基本操作

