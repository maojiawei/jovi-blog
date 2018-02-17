---
title: "Jenkins Pipeline 第一个java项目"
date: 2018-02-17T10:30:32+08:00
draft: false
tags: ["jenkins"]
categories: "devops"
---
## 简介
[上文](http://jovi.io/post/jenkins-install/)介绍了安装jenkins的流程，现在我们如何使用Jenkins的pipeline。
## 项目准备
我们先准备一个项目，本文的项目可以在 https://github.com/maojiawei/simple-java-maven-app 中查找到，大家注册一个github的账号，并fork下来。  
其中，有一个非常重要的文件即在项目根目录下的[Jenkinsfile](https://github.com/maojiawei/simple-java-maven-app/blob/master/Jenkinsfile)。  
该文件的作用是用来指定pipeline的流程。  
`agent`表示运行pipeline的代理服务器，本文采用的是docker的方式（运行中jenkins会启动一个docker容器，运行一下pipeline，运行完成后自动销毁）。  
`stages`表示pipeline的步骤，本文先只运行构建命令，所以只有一个stage。
```
pipeline {
    agent {
        docker {
            image 'maven:3-alpine' 
        }
    }
    stages {
        stage('Build') { 
            steps {
                sh 'mvn -B -DskipTests clean package' 
            }
        }
    }
}
```


## 新建item
在jenkins的主页面的左侧可以点击`New Item`的按钮。如果没有创建过item的同学，也可以在点击`create new jobs`来创建。  
![item新建](../images/jenkins/itemcreate.jpeg)
在输入框中输入item的名称，并点击pipeline,选择完成后，点击OK。如果没有，请安装pipeline的插件。
![pipeline新建](../images/jenkins/pipelinecreate.jpeg)

## 配置pipeline
点击OK后会进入pipeline的配置页面，在最上面的tab中，我们选择pipeline，然后我们需要填入以下几个参数。  
1.在`Definition`中，我们选择`Pipeline script from SCM`。  
2.在`SCM`中，我们选择`git`。  
3.在`Repository URL`中，我们填入项目地址：`https://github.com/maojiawei/simple-java-maven-app`。  
4.在`Script Path`中，我们填入Jenkinsfile的地址：`Jenkinsfile`。  
5.完成以上配置，点击Save即可。
 ![pipeline配置](../images/jenkins/pipelineconfig.jpeg)

## 执行pipeline
最后，我们只需要点击`build now`即可进行pipeline的运行。在Build History中有个进度条一样的就在运行了。点击后，可以查看日志。
 ![pipeline执行](../images/jenkins/pipelinebuildnow.jpeg)
看到Finished:SUCCESS，说明pipeline执行成功。
 ![pipeline日志](../images/jenkins/jenkinsconsole.jpeg)
## 彩蛋
jenkins的运行都是以天气作为状态，如果太阳表示成功，下雨标识失败，希望大家的每次构建都是大太阳哦！