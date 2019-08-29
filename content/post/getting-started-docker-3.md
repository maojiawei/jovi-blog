---
title: "docker入门实战之三:镜像"
date: 2019-08-21T22:29:29+08:00
draft: false
tags: ["docker"]
categories: "container"
toc: true
---
&emsp;本文主要介绍如何用命令操作Docker镜像。如前文所述，Docker会将应用程序打包成镜像，镜像就是生成容器的模板。

&emsp;Docker镜像一般格式为：
`[仓库]/[镜像]:[标签]`

- `仓库` :该Docker镜像所在的仓库地址。一般默认为docker.io，也可以为私有仓库，例如:127.0.0.1:9000。
- `镜像` :该Docker镜像的名称。例如:mysql等。
- `标签` :该镜像对应的标签，一般同一个镜像可以有多个标签，常为版本，例如:5.6等。

### 1.0 查找镜像
&emsp;`docker search`可以查询官方仓库中的镜像。默认地址为:hub.docker.com。共含有以下几个类型：

- `INDEX` :该镜像对应的仓库名，默认:docker.io。
- `NAME` :镜像名称。
- `DESCRIPTION` :镜像描述。
- `STARS` :镜像的STARS，即镜像的热度。
- `OFFICIAL` :是否为Docker官方提供的镜像。
- `AUTOMATED` :是否为automated build类型。

```shell
[root@iZwr8ljas8f52iZ ~]# docker search busybox
INDEX       NAME                                DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
docker.io   docker.io/busybox                   Busybox base image.                             1667      [OK]
docker.io   docker.io/progrium/busybox                                                          70                   [OK]
docker.io   docker.io/radial/busyboxplus        Full-chain, Internet enabled, busybox made...   25                   [OK]
docker.io   docker.io/arm32v7/busybox           Busybox base image.                             7
docker.io   docker.io/armhf/busybox             Busybox base image.                             5
docker.io   docker.io/yauritux/busybox-curl     Busybox with CURL                               5
docker.io   docker.io/arm64v8/busybox           Busybox base image.                             3
docker.io   docker.io/aarch64/busybox           Busybox base image.                             2
docker.io   docker.io/arm32v6/busybox           Busybox base image.                             2
docker.io   docker.io/armel/busybox             Busybox base image.                             2
docker.io   docker.io/i386/busybox              Busybox base image.                             2
```

### 2.0 获取镜像

&emsp;`docker pull [镜像名]`,即可从远程镜像仓库拉取镜像至本地。

```shell
[root@iZwr8ljas8f52iZ ~]# docker pull busybox
Using default tag: latest
Trying to pull repository docker.io/library/busybox ...
latest: Pulling from docker.io/library/busybox
ee153a04d683: Pull complete
Digest: sha256:9f1003c480699be56815db0f8146ad2e22efea85129b5b5983d0e0fb52d9ab70
Status: Downloaded newer image for docker.io/busybox:latest
```

### 3.0 查看本地镜像

&emsp;`docker images`列出本地所有的镜像名称。共有以下几个类型：

- `REPOSITORY` :该镜像对应的仓库及镜像名称，一般格式为：[仓库]/[镜像]
- `TAG` :该镜像对应的标签属性
- `IMAGE ID` :镜像ID，该镜像生成的唯一ID
- `CREATED` :镜像的创建时间
- `SIZE` :镜像的大小

```shell
[root@iZwr8ljas8f52iZ ~]# docker images
REPOSITORY                TAG                 IMAGE ID            CREATED             SIZE
docker.io/busybox         latest              db8ee88ad75f        5 weeks ago         1.22 MB
docker.io/nextcloud       stable              e92e9b831ee8        2 months ago        662 MB
docker.io/mysql           8.0.16              990386cbd5c0        3 months ago        443 MB
docker.io/mysql           5.7                 7faa3c53e6d6        3 months ago        373 MB
```

### 4.0 查看镜像详情

&emsp;`docker inspect [镜像名 or 镜像ID]`,即可查看镜像详细细节。

```shell
[root@iZwr8ljas8f52iZ ~]# docker inspect busybox
[
    {
        "Id": "sha256:db8ee88ad75f6bdc74663f4992a185e2722fa29573abcc1a19186cc5ec09dceb",
        "RepoTags": [
            "docker.io/busybox:latest"
        ],
        "RepoDigests": [
            "docker.io/busybox@sha256:9f1003c480699be56815db0f8146ad2e22efea85129b5b5983d0e0fb52d9ab70"
        ],
        "Parent": "",
        "Comment": "",
        "Created": "2019-07-18T21:20:21.810680491Z",
        "Container": "d6bc4fd488f31e0e72c06b4f268e475ff54ca7c17bc3a867da366aa581576057",
       ...
    }
]
```

### 5.0 打包镜像

&emsp;如果需要将本地的镜像移动至其他服务器，可以通过将镜像进行打包，在传至对应服务器，导入即可。

&esmp;`docker save [镜像名] > [镜像压缩文件名]`即可将镜像压缩。如下例所示，在当前目录下即生成对应对应镜像压缩文件。

```shell
[root@iZwr8ljas8f52iZ ~]# docker save busybox > busybox.tar.gz
```

### 6.0 镜像导入

&emsp;在`5.0`的基础上，可以将压缩后的镜像文件，在其他含有docker的服务器中进行镜像导入。

&esmp;`docker load < [镜像压缩文件名]`即可。

```shell
[root@iZwr8ljas8f52iZ ~]# docker load < busybox.tar.gz
```

### 7.0 删除镜像

&emsp;`docker rmi [镜像名 or 镜像ID]`,即可在本地删除镜像。

```shell
[root@iZwr8ljas8f52iZ ~]# docker rmi busybox
Untagged: busybox:latest
Untagged: docker.io/busybox@sha256:9f1003c480699be56815db0f8146ad2e22efea85129b5b5983d0e0fb52d9ab70
Deleted: sha256:db8ee88ad75f6bdc74663f4992a185e2722fa29573abcc1a19186cc5ec09dceb
Deleted: sha256:0d315111b4847e8cd50514ca19657d1e8d827f4e128d172ce8b2f76a04f3faea
```