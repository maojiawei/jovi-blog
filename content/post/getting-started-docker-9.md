---
title: "docker入门实战之八:Cgroup"
date: 2019-09-30T14:47:02+08:00
draft: false
tags: ["docker"]
categories: "container"
toc: true
---
&emsp;前文介绍了Docker主要使用了Linux Namespace来实现了容器的资源隔离。而本章将介绍Docker的资源限制的实现方式:Cgroup。

&emsp;cgroup是Control Groups 的缩写，是Linux内核提供的一种可以限制进程或进程组使用物理资源（比如:CPU、内存、磁盘等）的机制。

&emsp;它主要提供了如下功能:

- **资源限制(Resource limitation)**: Cgroups可以对一组进程组所涉及的资源总额进行限制，比如内存使用上限以及文件系统的缓存限制。
- **优先级分配(Prioritization)**: 通过对CPU时间片数量及硬盘IO等进行优先级控制，比如：CPU利用和磁盘IO吞吐。
- **资源统计(Accounting)**: 一些审计或一些统计，比如:CPU使用时长、内存用量等。
- **进程控制(Control)**: Cgroups可以对一组进程组挂起进程，恢复执行进程。

&emsp;当我们查看`/sys/fs/cgroup`目录时，可以查看Cgroup主要可以控制以下几种资源。
```shell
[root@jovi ~]# ls /sys/fs/cgroup/
blkio  cpuacct      cpuset   freezer  memory   net_cls,net_prio  perf_event  systemd
cpu    cpu,cpuacct  devices  hugetlb  net_cls  net_prio          pids
```

- blkio — 用于限制块设备I/O速率。
- cpu — 用于限制CPU占用率。
- cpuacct — 统计CPU的使用情况。
- cpuset — 分配指定的CPU和内存节点。
- devices — 允许或拒绝Cgroup任务访问的设备。
- freezer — 挂起或恢复Cgroup的任务。
- memory — 设定 cgroup 任务所使用的内存限制，并自动生成内存资源使用报告。
- net_cls — 用于限制网络带宽（配合traffic controller）。
- net_prio — 设置网络流量的优先级
- hugetlb — 限制HugeTLB的使用。

&emsp;我们可以在`/sys/fs/cgroup/${资源类型}/docker/${容器ID}/`中查看:

- **${资源类型}:**资源类型，以上几种，例如:blkio,cpu等
- **${容器ID}:**容器ID，可以通过`docker ps` 查看