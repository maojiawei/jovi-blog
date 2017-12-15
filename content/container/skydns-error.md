---
title: "skydns出现的错误"
date: 2017-12-12T10:09:09+08:00
draft: false
tags: ["kubernetes"]
---
kubernetes的dns一直采用的是第三方dns(skydns)，经常出问题。这篇文章主要提供给他们解决该类问题的方法，一般情况如下。  
```
[root@k8s-1 test]# kubectl exec nginx -i -t -- /bin/bash
root@nginx:/# cat /etc/resolv.conf
nameserver 10.254.0.2
search default.svc.cluster.local. svc.cluster.local. cluster.local.
options ndots:5

root@nginx:/# ping my-nginx
ping: unknown host
```
## dns服务
如果遇到DNS问题，首先查看DNS的服务有没有问题。一般DNS默认为3个，如果3个为说明没有问题。
```
kubectl get po -n kube-system
```

## kube-proxy
kubernetes的DNS出了问题首先要检查各个节点kube-proxy服务是否正常。
```
systemctl status kube-proxy.service
```

## 其他
如果还有问题，请检查iptables，具体如下。
```
systemctl stop kubelet 
systemctl stop docker 
iptables --flush 
iptables -tnat --flush 
systemctl start kubelet 
systemctl start docker
```
