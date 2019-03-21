---
title: "kubernetes监控(elk技术栈)"
date: 2017-12-29T16:41:27+08:00
draft: false
tags: ["kubernetes","elastic"]
categories: "container"
toc: false
---
# 简介
目前监控kubernetes的工具主要以Prometheus为主，但Prometheus的学习成本很高，而最新的metricbeat6.1开始加入了针对kubernetes的监控，可以解决在kubernetes监控的问题。本文介绍的是如何使用elastic技术栈来解决监控kubernetes的问题。

# 前提条件
## kubernetes
本文中所有的环境都是运行在kubernetes上的，所以需要有kubernetes的运行环境，大家可以链接中的[参考文档](https://github.com/opsnull/follow-me-install-kubernetes-cluster)来搭建基础的kubernetes运行环境。  

## 监控指标
本文中针对kubernetes的监控指标可以在metricbeat的[官方文档](https://www.elastic.co/guide/en/beats/metricbeat/master/metricbeat-module-kubernetes.html)中查看。

# 架构图
metricbeat会在每个kubernetes的node上创建一个节点，可以通过监控kubelet来获取监控的数据。监控的数据会通过redis（消息队列）传输给logstash，可以在logstash中可以进行一系列的信息处理或报警，并将处理的结果发给elasticsearch。kibana作为展示工具，可以进行数据展示。
![kubernetes-elk监控](../images/kubernetes/kubernetes-elk-monitor.png)

# redis
redis在整个技术栈中主要起一个消息队列的作用。
## deployment
创建`redis-deployment.yaml`文件，并执行`kubectl create -f redis-deployment.yaml`,文件如下：
```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: redis
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: redis
    spec:
      hostname: redis
      containers:
        - name: redis
          image: redis:4.0.6
          imagePullPolicy: IfNotPresent
          ports:
            - containerPort: 6379
```
## service
创建`redis-service.yaml`文件，并执行`kubectl create -f redis-service.yaml`,文件如下：
指定service的端口为32357。
```
apiVersion: v1
kind: Service
metadata:
  name: redis
  labels:
    app: redis
spec:
  selector:
    app: redis
  type: NodePort
  ports:
    - name: redis
      port: 6379
      targetPort: 32357
```
# elasticsearch
elasticsearch在技术栈中主要起着接收数据，搜索引擎的作用。
## deployment
创建`elasticsearch-deployment.yaml`文件，并执行`kubectl create -f elasticsearch-deployment.yaml`,文件如下：  
```
apiVersion: extensions/v1beta1 
kind: Deployment 
metadata: 
  name: elasticsearch
spec: 
  replicas: 1
  template: 
    metadata: 
      labels: 
        name: elasticsearch
    spec: 
      containers: 
        - name: elasticsearch 
          image: docker.elastic.co/elasticsearch/elasticsearch:6.1.1
          imagePullPolicy: IfNotPresent
          ports: 
            - containerPort: 80
```
## service
创建`elasticsearch-service.yaml`文件，并执行`kubectl create -f elasticsearch-service.yaml`,文件如下：
指定service的端口为32357。
```
apiVersion: v1
kind: Service
metadata:
  name: elasticsearch
  labels:
    name: elasticsearch
spec:
  selector:
    name: elasticsearch
  type: NodePort
  ports:
  - port: 9200
    targetPort: 31505
```
# metricbeat
metricbeat是elastic公司beat项目下的一个子项目。Metricbeat内部模块可以采集服务的各项指标，支持以下图中的各种服务，你也可以自己用Go语言自己创建MetricBeat模块。
## configmap
创建`metricbeat-configmap.yaml`文件，并执行`kubectl create -f metricbeat-configmap.yaml`,文件内容如下:  
本文会创建两个configmap。  
第一个为`metricbeat-config`。该配置文件为metricbeat默认的项目启动配置文件，`metricbeat.config.modules`会去加载外部的参数会去加载外部的配置文件，`output.redis`主要是将结果输出到redis中。对应的key为`metricbeat-kubernetes`。对应的host为127.0.0.1:32357。（这是kubernetes的service的端口，下文中会介绍通过host模式，启动pod。因此可以直接通过127.0.0.1的方式去访问。）  
第二个为`metricbeat-modules-config`。该配置文件为外部配置文件可以配置一些metricbeat的相关采集模块，本文主要采集的数据为kubernetes。对应的hosts为127.0.0.1:10255（该端口为kubelet默认端口）。
```
---
kind: ConfigMap
apiVersion: v1
metadata:
  name: metricbeat-config
data:
  metricbeat.yml: |-
    metricbeat.config.modules:
      path: /usr/share/metricbeat/modules.d/*.yml
      reload.enabled: false
    
    output.redis:
      hosts: ['127.0.0.1:32357']
      key: 'metricbeat-kubernetes'
      db: 0
---
kind: ConfigMap
apiVersion: v1
metadata:
  name: metricbeat-modules-config
data:
  kubernetes.yml: |-
    - module: kubernetes
      metricsets:
        - node
        - system
        - pod
        - container
        - volume
      period: 10s
      hosts: ["127.0.0.1:10255"]
```
## daemonset
创建`metricbeat-daemonset.yaml`文件，并执行`kubectl create -f metricbeat-daemonset.yaml`,文件内容如下:
本例主要创建一个daemonset，会在所有的node上都创建一个metricbeat。  
本文采用的镜像为elastic官方的镜像`docker.elastic.co/beats/metricbeat:6.1.1`。   
配置文件中会采用挂载的方式将之前创建的configmap挂载至对应的目录下。然后通过args来指定参数启动metricbeat。  
daemonset采用的是`hostNetwork: true`，项目中的pod会通过host模式启动，在容器中的meticbeat可以通过127.0.0.1来采集各个节点的kubelet的数据。同时，以为kubernetes有内部的kube-proxy机制，所以可以通过127.0.0.1:32357调用本地的端口即可访问redis服务。
```
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
  name: metricbeat
spec:
  template:
    metadata:
      labels:
        app: metricbeat
    spec:
      volumes:
      - name: config
        configMap:
          name: metricbeat-config
      - name: modules
        configMap:
          name: metricbeat-modules-config
      hostname: metricbeat
      hostNetwork: true
      containers:
        - name: metricbeat
          image: docker.elastic.co/beats/metricbeat:6.1.1
          args: [
            "-c", "/etc/metricbeat.yml",
            "-e",
          ]
          imagePullPolicy: IfNotPresent
          volumeMounts:
          - name: config
            mountPath: "/etc/metricbeat.yml"
            readOnly: true
            subPath: metricbeat.yml
          - name: modules
            mountPath: /usr/share/metricbeat/modules.d
            readOnly: true
```

# logstash
logstash在整个技术栈中起着接收、处理和转发数据的作用。
## configmap
创建`logstash-configmap.yaml`，文件如下:
input表示接收的数据方，配置文件中为redis，key为metricbeat-kubernetes（需要与metricbeat中output的key一致）。output表示数据的转发方，配置文件中为elasticsearch，index为数据输出对应的索引。
```
kind: ConfigMap
apiVersion: v1
metadata:
  name: logstash-config
data:
  logstash.conf: |
    input {
      redis {
        host => "redis"
        port => 6379
        key => "metricbeat-kubernetes"
        data_type => "list"
      }
    }

    output {
      elasticsearch {
        hosts => ["elasticsearch:9200"]
        index => "kubernetes-monitor-%{+YYYY.MM.dd}"
      }
    }

```
## deployment
创建`logstash-configmap.yaml`，并执行`kubectl create -f logstash-configmap.yaml`，文件如下:
```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: logstash
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: logstash
    spec:
      volumes:
      - name: config
        configMap:
          name: logstash-config
      hostname: logstash
      containers:
        - name: logstash
          image: docker.elastic.co/logstash/logstash:6.1.1
          args: [
            "-f","/usr/share/logstash/config/logstash.conf",
          ]
          imagePullPolicy: IfNotPresent
          volumeMounts:
          - name: config
            mountPath: "/usr/share/logstash/config/logstash.conf"
            readOnly: true
            subPath: logstash.conf
```
# kibana
kibana是elastic中的展示项目，可以从elasticsearch中获取数据并进行展示。
## deployment
创建`kibana-deployment.yaml`，并执行`kubectl create -f kibana-deployment.yaml`，文件如下:
```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: kibana
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: kibana
    spec:
      hostname: kibana
      containers:
        - name: kibana
          image: 172.28.14.126:5000/elastic/kibana:6.1.1
          imagePullPolicy: IfNotPresent
          ports:
            - containerPort: 5601
```
## service
创建`kibana-service.yaml`，并执行`kubectl create -f kibana-service.yaml`，文件如下:
```
apiVersion: v1
kind: Service
metadata:
  name: kibana
  labels:
    app: kibana
spec:
  selector:
    app: kibana
  type: NodePort
  ports:
    - name: kibana
      port: 5601
      targetPort: 31465
```
## 查看数据
通过node的ip加上31465端口来开启kibana。
点击management中的`create index pattern`来创建索引
![kubernetes-elk监控-创建索引](../images/kubernetes/kubernetes-elk-monitor-create-index.jpeg)
点击discover查看数据
![kubernetes-elk监控](../images/kubernetes/kubernetes-elk-monitor-discover.jpeg)
