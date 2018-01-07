---
title: "Kubernetes通过endpoint对外暴露服务"
date: 2018-01-05T18:38:26+08:00
draft: false
tags: ["kubernetes"]
categories: "container"
---
## 简介
kubernetes针对内部pod对外暴露服务一般是通过将service通过标签选择器关联pod，然后对外暴露服务。如果将kubernetes外部的服务，例如mysql、glusterfs对外部暴露或者对内部采用service的方式提供服务，就可以使用endpoint。

### Endpoint
创建`mysql-endpoint.yaml`文件。
```
kind: Endpoints
apiVersion: v1
metadata:
  name: mysql-endpoint
subsets:
  - addresses:
      - ip: 172.21.7.21
    ports:
      - port: 3306
```
### service
创建`mysql-service.yaml`文件。
```
apiVersion: v1
kind: Service
metadata:
  name: mysql-service
spec:
  type: NodePort
  ports:
    - port: 3306
```
## 结语
该方式可以将mysql的端口在kubernetes中通过NodePort的方式映射出来，直接通过NodePort分配的端口加上任意节点的ip即可访问。
