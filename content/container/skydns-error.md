---
title: "skydns出现的错误"
date: 2017-12-12T10:09:09+08:00
draft: true
---
```
systemctl stop kubelet 
systemctl stop docker 
iptables --flush 
iptables -tnat --flush 
systemctl start kubelet 
systemctl start docker
```
