---
title: "docker入门实战之六:Dockerfile"
date: 2019-09-09T11:13:08+08:00
draft: false
tags: ["docker"]
categories: "container"
toc: true
---
&emsp;如果想要自己构建一个自定义镜像，Docker为我们准备了Dockerfile的方式。Dockerfile包含创建镜像所需要的全部指令。

### 1.0 镜像构建命令
&emsp;我们可以使用`docker build`命令，将Dockerfile构建成镜像。

&emsp;选项说明:

- `-f :`指定要使用的Dockerfile路径
- `--tag, -t: `镜像的名字及标签，通常 name:tag 或者 name 格式；可以在一次构建中为一个镜像设置多个标签,默认为`<NONE>`

```shell
docker build [OPTIONS] PATH | URL | -
```

### 2.0 Dockerfile指令详解

#### 2.1 FROM指令
&emsp;FROM指令用于声明该Dockerfile的基础镜像，即该镜像是基于哪个镜像来构建新的镜像，一般为Dockerfile的首行。
```shell
FROM <image>:<tag>
```
&emsp;参数说明:

- `<image> :`基础镜像名称
- `<tag>: `基础镜像的标签，默认:latest

#### 2.2 MAINTAINER指令
&emsp;MAINTAINER指令用于声明作者。
```shell
MAINTAINER <作者信息>
```
&emsp;参数说明:

- `<作者信息> :`维护该镜像的作者名称及相关信息

#### 2.3 ENV指令
&emsp;ENV指令用于定义环境变量。
```shell
ENV <key> <value>
ENV <key1>=<value1> <key2>=<value2>...
```
&emsp;参数说明:

- `<key> :`环境变量对应的key
- `<value> :`环境变量对应的value

#### 2.4 RUN指令
&emsp;RUN指令是在镜像构建时需要执行的命令
```shell
RUN <command>
```
&emsp;参数说明:

- `<command> :`镜像构建时执行的命令

**注意:**Docker采用的是联合文件系统，Dockerfile的每一个指令都是一层镜像。所以在写Dockerfile的时候，应该尽量避免多个`RUN`命令。具体可以[查看](http://jovi.io/post/docker-image-optimization/)

#### 2.5 WORKDIR指令
&emsp;指定工作目录，以后各个指令的基础目录就是该目录
```shell
WORKDIR <工作目录路径>
```
&emsp;参数说明:

- `<工作目录路径> :`工作目录路径，如不存在，WORKDIR会自动创建目录

#### 2.6 COPY指令
&emsp;将本地的文件或目录复制至镜像内的指定目录下
```shell
COPY <源路径>... <目标路径>
```
&emsp;参数说明:

- `<源路径> :`本地的文件路径或目录路径，相对于Dockerfile所在路径的相对路径
- `<目标路径> :`容器内的绝对路径（建议），也可以是相对于WORKDIR（工作目录）的相对路径

#### 2.6 ADD指令
&emsp;ADD的功能与COPY指令相似，但更高级，针对压缩包会自动解压。
```shell
ADD <源路径>... <目标路径>
```
&emsp;参数说明:

- `<源路径> :`本地的文件路径或目录路径，相对于Dockerfile所在路径的相对路径
- `<目标路径> :`容器内的绝对路径（建议），也可以是相对于WORKDIR（工作目录）的相对路径

#### 2.6 VOLUME命令
&emsp;将本地卷挂载至容器中
```shell
VOLUME <路径>
```
&emsp;参数说明:

- `<路径> :`容器中的目录

#### 2.7 EXPOSE指令
&emsp;容器运行时需要开放的端口
```shell
EXPOSE <端口> [<端口>...]
```
&emsp;参数说明:

- `<端口> :`端口号，可以为多个

#### 2.8 CMD指令
&emsp;容器运行时执行的命令，一般为Dockerfile的最后一行
```shell
CMD <命令>
```
&emsp;参数说明:

- `<命令> :`需要执行的shell命令
**注:**与`RUN`命令不同的是，`RUN`是在构建时需要执行的命令，而`CMD`是在容器启动是调用的命令。

#### 2.9 ENTRYPOINT指令
&emsp;容器运行时执行的命令，一般为Dockerfile的最后一行
```shell
ENTRYPOINT <命令>
```
&emsp;参数说明:

- `<命令> :`需要执行的shell命令
**注:**与CMD不同的是，当指定了ENTRYPOINT后，CMD不再是直接运行命令，而是将CMD的内容作为参数传给ENTRYPOINT指令。



