---
title: "Jenkins的安装"
date: 2018-02-16T11:33:43+08:00
draft: false
tags: ["jenkins"]
categories: "devops"
toc: false
---
## 简介
[jenkins](https://jenkins.io)是目前最主流的CI产品，是一套基于java开发的持续集成工具，同时，jenkins作为一个开放的平台，集成了大量开发者为其定义的插件。  

## 安装条件
服务器配置：  
&nbsp;&nbsp;&nbsp;&nbsp;256MB的内存（512MB内存更优）  
&nbsp;&nbsp;&nbsp;&nbsp;10GB的物理硬盘（以便于jenkins和docker镜像）  
软件配置：  
&nbsp;&nbsp;&nbsp;&nbsp;java8（或以上版本）  
&nbsp;&nbsp;&nbsp;&nbsp;docker（centos有网络的环境可以使用`yum install -y docker`来安装）  

## 安装
### docker
使用docker安装比较简单比较简单，在安装docker的服务器中运行一下命令，等待完成即可。
```
docker run --name jenkins -p 8080:8080 -p 50000:50000 -v ~/jenkins:/var/jenkins_home -d jenkinsci/jenkins:2.89.4-alpine
```
### 手动安装
下载jenkins的war包,点击可以[下载](https://mirrors.tuna.tsinghua.edu.cn/jenkins/war-stable/2.89.4/jenkins.war)最新稳定版2.89.4。
并运行`java -jar jenkins.war --httpPort=8080`等待运行完成。

## 配置
在浏览器输入`http://localhost:8080`即可看到，根据指导进行配置。
### 解锁
![jenkins解锁](../images/jenkins/unlockjenkins.jpeg)
### 插件安装
选择jenkins的插件，一般选择安装默认即可，之后等待安装结束即可。  
![jenkins定制化](../images/jenkins/customizejenkins.jpeg)  

## 安装问题
### docker权限问题
在centos的环境下使用docker安装jenkins会出现以下问题
```
Can not write to /var/jenkins_home/copy_reference_file.log. Wrong volume permissions?
touch: cannot touch ‘/var/jenkins_home/copy_reference_file.log’: Permission denied
```
解决方案
步骤一：关闭selinux
```
setenforce 0
```
步骤二：在原先的docker启动命令中加入`-u 0`的参数，即
```
docker run --name jenkins -p 8080:8080 -p 50000:50000 -v ~/jenkins:/var/jenkins_home -u 0 -d jenkinsci/jenkins:2.89.4-alpine
```
### 插件问题
由于网络等原因，在插件安装的过程中可能会出现无法安装的情况。我们可以通过修改插件的代理来解决。  
在jenkins的主菜单选择Manage Jenkins并点击Manage Plugins，进入jenkins的插件管理。  
![jenkins插件管理](../images/jenkins/pluginmanage.jpeg)
选择tab中的Advanced，在Update Site中输入`https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/current/update-center.json`，并点击submit。
![jenkins更新插件地址](../images/jenkins/pluginproxy.jpeg)