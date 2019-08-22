---
title: "docker入门实战之三:镜像"
date: 2019-08-21T22:29:29+08:00
draft: false
tags: ["docker"]
categories: "container"
toc: true
---
&emsp;本文主要介绍如何用命令操作Docker镜像。Docker镜像一般格式为：
`[仓库]/[镜像]:[标签]`

- `仓库` :该Docker镜像所在的仓库地址。一般默认为docker.io，也可以为私有仓库，例如:127.0.0.1:9000。
- `镜像` :该Docker镜像的名称。例如:mysql等。
- `标签` :该镜像对应的标签，一般同一个镜像可以有多个标签，常为版本，例如:5.6等。

### 1.0 查看本地镜像

&emsp;当我们

```shell
[root@iZwr8ljas8f52iZ ~]# docker images
REPOSITORY                TAG                 IMAGE ID            CREATED             SIZE
docker.io/nextcloud       stable              e92e9b831ee8        2 months ago        662 MB
docker.io/mysql           8.0.16              990386cbd5c0        3 months ago        443 MB
docker.io/mysql           5.7                 7faa3c53e6d6        3 months ago        373 MB
```