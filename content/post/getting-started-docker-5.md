---
title: "docker入门实战之五:仓库"
date: 2019-08-29T15:46:26+08:00
draft: false
tags: ["docker"]
categories: "container"
toc: true
---
&emsp;本文主要介绍如何用命令操作Docker仓库。Docker仓库是一个集中管理镜像与分发镜像的服务。

&emsp;Docker官方维护的公共仓库:http://hub.docker.com。大部分镜像都可以在该镜像仓库中，但是由于镜像仓库在国外，可能存在网络延迟，一般企业内部都会有自己的镜像仓库。

### 1.0 仓库启动
&emsp;Docker官方提供了一个镜像仓库的镜像，我们以`registry:2.6`镜像为例。

&emsp;首先从官方镜像中拉取私有的镜像仓库。
```shell
docker pull registry:2.6
Trying to pull repository docker.io/library/registry ... 
2.6: Pulling from docker.io/library/registry
c87736221ed0: Pull complete 
1cc8e0bb44df: Pull complete 
219d1d2b6839: Pull complete 
42f802bbc165: Pull complete 
6f5d441a35a4: Waiting 
6f5d441a35a4: Pull complete 
Digest: sha256:6cab8c43c5288e6efa87df48019ccf6148def11e6de6fafd8e5270c2b4cdba0e
Status: Downloaded newer image for docker.io/registry:2.6

```