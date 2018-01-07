---
title: "Docker镜像的优化之道"
date: 2018-01-07T18:42:50+08:00
draft: true
---
# 简介
本文将介绍如何构建docker镜像可以使镜像最小化，构建速度最快。

## 降低拉取镜像的时间
在构建镜像时，需要拉取基础镜像。docker官方提供了一个镜像仓库 https://hub.docker.com 。但由于服务器在国外，每次下载会非常慢，这里建议大家使用国内的镜像仓库，例如:[时速云](https://hub.tenxcloud.com/)、[docker官方中国加速区](https://registry.docker-cn.com)。有条件的可以自己安装私有仓库，安装方式如下:
```
docker run -d -p 5000:5000 --restart=always --name registry \
-v /mnt/date/registry:/var/lib/registry \
registry:2.6
```
> -p 5000:5000 表示暴露端口。  
> --restart=always 表示自动重启。  
> --name registry 为容器名称。  
> -v /mnt/date/registry:/var/lib/registry 为仓库镜像挂载目录 /mnt/date/registry可根据实际情况修改，由于一般镜像较大，建议挂盘或者采用较大的硬盘。  
> registry:2.6 为仓库版本，目前最高的版本。  

## 减少层级
Docker采用的是联合文件系统（UnionFS），即一个Docker镜像的尺寸是每一个独立镜像层的尺寸之和。因此，减少Dockerfile的层级能够有效的减小镜像的大小。
![联合文件系统](../images/docker/docker-unionfs.jpg)

### 链式指令
在下面的例子中，采用了四个RUN方法，创建了四个目录。在Docker镜像中只会增加镜像的层数。
```
FROM openjdk:8-jre-alpine
MAINTAINER jovi
RUN mkdir -p /one/packages 
RUN mkdir -p /one/applications 
RUN mkdir -p /one/config
RUN mkdir -p /one/data
COPY zookeeper-3.4.10 /one/applications/zookeeper-3.4.10
RUN cp /one/applications/zookeeper-3.4.10/conf/zoo_sample.cfg /one/config/zoo.cfg
CMD ["/one/applications/zookeeper-3.4.10/bin/zkServer.sh","start-foreground","/one/config/zoo.cfg"]
```
因此，可以使用链式指令的方式，减少层级。结果如下所示:
```
FROM openjdk:8-jre-alpine
MAINTAINER jovi
RUN mkdir -p /one/packages && mkdir -p /one/applications  && mkdir -p /one/config && mkdir -p /one/data 
COPY zookeeper-3.4.10 /one/applications/zookeeper-3.4.10
RUN cp /one/applications/zookeeper-3.4.10/conf/zoo_sample.cfg /one/config/zoo.cfg
CMD ["/one/applications/zookeeper-3.4.10/bin/zkServer.sh","start-foreground","/one/config/zoo.cfg"]
```

### 无需清理不必要的文件
有些人喜欢使用`rm -rf`的方式来删除不必要的文件（如下文所示）。在Docker的镜像是每一个独立镜像层之和，每一个指令要么保持镜像尺寸不变，要么增加它的尺寸，因此该步骤没有真正删除相应的磁盘空间。
```
FROM openjdk:8-jre-alpine
MAINTAINER jovi
RUN mkdir -p /one/packages && mkdir -p /one/applications && mkdir -p /one/config && mkdir -p /one/data
RUN cd /one/packages && wget http://mirror.bit.edu.cn/apache/zookeeper/zookeeper-3.4.10/zookeeper-3.4.10.tar.gz
RUN cd /one/applications && tar -xvf /one/packages/zookeeper-3.4.10.tar.gz
RUN rm -rf /one/packages/zookeeper-3.4.10.tar.gz
RUN cp /one/applications/zookeeper-3.4.10/conf/zoo_sample.cfg /one/config/zoo.cfg
CMD ["/one/applications/zookeeper-3.4.10/bin/zkServer.sh","start-foreground","/one/config/zoo.cfg"]
```
我的建议是在使用过程中，有很多文件，例如:.git文件等不必要的文件可以不打入镜像中，可以使用.dockerignore，忽略文件。

## 减少自动安装使用
减少apt-get或者yum的使用，频繁使用会使得Dockerfile构建变慢。

## 备注
最后说一下，docker的官方镜像时区都默认为美国时区，因此，大家别忘了调整为中国时区。



