---
title: "docker入门实战之四:容器"
date: 2019-08-28T13:56:26+08:00
draft: false
tags: ["docker"]
categories: "container"
toc: true
---
&emsp;本文主要介绍如何用命令操作Docker容器。Docker容器是Docker运行时的实体，我们可以通过以下命令对容器进行操作。

### 1.0 列出容器
&emsp;`docker ps`可以查询出目前正在运行的容器。具体内容如下:

- `CONTAINER ID`：容器ID，在启动容器时，自动创建。
- `IMAGE`：容器依赖镜像。
- `COMMAND`：容器启动命令。
- `CREATED`：容器创建时间。
- `STATUS`：容器状态。
- `PORTS`：容器端口。
- `NAMES`：容器名称。

```aidl
docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                 PORTS                               NAMES
aa66e1625cd0        minio/minio         "/usr/bin/docker-e..."   2 weeks ago         Up 2 weeks (healthy)   0.0.0.0:9000->9000/tcp              minio
```

### 2.0 启动容器
#### 2.1 交互模式启动
&emsp;交互模式启动后，可以直接进入Docker容器中，直接在容器中进行操作。具体命令为`docker run -ti [镜像名] [命令]`

```shell
[root@iZwr8ljas8f52iZ ~]# docker run -ti busybox /bin/sh
/ # ls
bin   dev   etc   home  proc  root  run   sys   tmp   usr   var
/ # 
```
&emsp;在运行`docker run -ti busybox /bin/sh`命令后，即可进入容器内部，所做的操作也在容器内。

#### 2.2 后台运行
&emsp;Docker启动后，会在后台运行。具体命令为`docker run -d [镜像名] [命令]`
```shell
[root@iZwr8ljas8f52iZ ~]# docker run -d  busybox sleep 1000
96d315e1f0d88d69ab6c6874fc7bbf4cbbc60f57a20ce56e2f3542957eb49630
```
&emsp;在运行`docker run -d  busybox sleep 1000`命令后，Docker会返回一串字符串，即容器ID。

&emsp;我们通过`docker ps`查看目前存在的容器进程
```shell
[root@iZwr8ljas8f52iZ ~]# docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                 PORTS                               NAMES
96d315e1f0d8        busybox             "sleep 1000"             52 seconds ago      Up 51 seconds                                              relaxed_varahamihira
```

### 3.0 容器暂停
&emsp;`docker pause`命令挂起指定容器中的所有进程。

#### 3.1 容器暂停
&emsp;`docker pause [容器ID or 容器名称]`命令挂起指定容器中的所有进程。


#### 3.2 恢复容器运行
&emsp;`docker unpause [容器ID or 容器名称]`命令即可将暂停的容器重新运行。

### 4.0 容器停止
&emsp;`docker stop `命令可以停止正在运行的容器。

#### 4.1 容器停止
&emsp;`docker stop [容器ID or 容器名称]`命令停止容器。


#### 4.2 恢复容器运行
&emsp;`docker start [容器ID or 容器名称]`命令即可将停止的容器重新运行。

### 5.0 容器删除
&emsp;`docker rm [容器ID or 容器名称]`即可删除非运行时的容器。