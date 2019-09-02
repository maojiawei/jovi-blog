---
title: "docker入门实战之五:仓库"
date: 2019-08-29T15:46:26+08:00
draft: false
tags: ["docker"]
categories: "container"
toc: true
---
&emsp;本文主要介绍如何用命令操作Docker仓库。Docker仓库是一个集中管理镜像与分发镜像的服务。

&emsp;Docker官方维护的公共仓库:http://hub.docker.com 。大部分镜像都可以在该镜像仓库中，但是由于镜像仓库在国外，可能存在网络延迟，一般企业内部都会有自己的镜像仓库。

### 1.0 仓库启动
&emsp;Docker官方提供了一个镜像仓库的镜像，我们以`registry:2.6`镜像为例。

&emsp;1.首先从官方镜像中拉取私有的镜像仓库。
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

&emsp;2.通过以下命令,运行docker容器。
```shell
[root@jovi ~]# docker run -d -p 5000:5000 --restart always --name registry registry:2.6
8b16e2b15d2e8004f7f7c264ba9a73686cae9a2037303a17096c1e57e9423725
```

&emsp;3.检查是否正在运行，输入`docker ps -a`如果存在registry的容器，且STATUS为UP即正常运行。
```shell
docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                  PORTS                               NAMES
8b16e2b15d2e        registry:2.6        "/entrypoint.sh /e..."   35 seconds ago      Up 34 seconds           0.0.0.0:5000->5000/tcp              registry
```

&emsp;4.在命令行中输入以下命令，检查仓库是否正常运行。
```shell
curl localhost:5000/v2/
{}
```

### 2.0 镜像上传

&emsp;1.将镜像修改为本地镜像仓库的镜像。

&emsp;一般来说，镜像格式为`[仓库]/[镜像]:[标签]` 。因此需要首先要将其添加仓库前缀,我们可以通过`docker tag ${原镜像名称} ${现镜像名称}`的命令进行修改。
```shell
docker tag busybox 127.0.0.1:5000/busybox
```

&emsp;2.不是所有的仓库Docker都能从其中拉镜像，需要将仓库加入docker客户端的授信中。
```aidl
vim /etc/docker/daemon.json
```
编辑`/etc/docker/daemon.json`,并输入以下内容:
```shell
{"insecure-registries": ["127.0.0.1:5000"]}
```
重启服务
```aidl
systemctl restart docker
```

&emsp;3.上传镜像
```shell
docker push 127.0.0.1:5000/busybox
The push refers to a repository [127.0.0.1:5000/busybox]
0d315111b484: Pushed 
latest: digest: sha256:895ab622e92e18d6b461d671081757af7dbaa3b00e3e28e12505af7817f73649 size: 527
```

&emsp;4.检查镜像是否上传成功
```shell
curl localhost:5000/v2/_catalog
{"repositories":["busybox"]}
```