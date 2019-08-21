---
title: "docker入门实战之二:安装"
date: 2019-08-21T11:20:40+08:00
draft: false
tags: ["docker"]
categories: "container"
toc: true
---
本文主要介绍如何在linux环境下安装Docker。
### 1.0 在线yum安装

#### 1.1 内核版本
&emsp;Docker要求内核版本高于3.10,Centos可以通过`name -r`命令查看当前操作系统的内核。
```shell
[root@joviblog ~]# uname -r
3.10.0-693.2.2.el7.x86_64
```

#### 1.2 yum安装
&emsp;运行如下命令，即可安装完成。
```aidl
yum install -y docker
```

#### 1.3 docker启动
&emsp;将docker启动，并加入开机自启动
```aidl
systemctl enable docker
systemctl start docker
```

#### 1.4 验证
&emsp;验证docker是否正常，通过以下命令验证即可。
```aidl
[root@iZwr8ljas8f52iZ ~]# docker version
Client:
 Version:         1.13.1
 API version:     1.26
 Package version: docker-1.13.1-94.gitb2f74b2.el7.centos.x86_64
 Go version:      go1.10.3
 Git commit:      b2f74b2/1.13.1
 Built:           Tue Mar 12 10:27:24 2019
 OS/Arch:         linux/amd64

Server:
 Version:         1.13.1
 API version:     1.26 (minimum version 1.12)
 Package version: docker-1.13.1-94.gitb2f74b2.el7.centos.x86_64
 Go version:      go1.10.3
 Git commit:      b2f74b2/1.13.1
 Built:           Tue Mar 12 10:27:24 2019
 OS/Arch:         linux/amd64
 Experimental:    false

```

