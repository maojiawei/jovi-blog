---
title: "docker入门实战之八:Namespace"
date: 2019-09-24T17:53:25+08:00
draft: false
tags: ["docker"]
categories: "container"
toc: true
---
&emsp; Linux Namespace是Docker容器隔离的基础。而Linux内核中Namespace可以提供六种隔离方式供Docker调用，分别为以下六种。

| Namespace名称     | 隔离内容                   | 隔离效果                                                     |
| ----------------- | -------------------------- | ------------------------------------------------------------ |
| Mount Namespace   | 挂载点（文件系统）         | 每个容器内看到的文件结构都是不同的                           |
| UTS Namespace     | 主机名与域名               | 每个容器可以有自己的 主机名和域名                            |
| IPC Namespace     | 信号量、消息队列和共享内容 | 每个容器有其自己的 System V IPC 和 POSIX 消息队列文件系统，因此，只有在同一个 IPC namespace 的进程之间才能互相通信。 |
| PID Namespace     | 进程ID                     | 每个 PID namespace 中的进程可以有其独立的 PID。每个容器都有一个PID为1的root进程。虽然宿主机也有进程的PID，但是同一进程在容器和宿主机的进程ID并不相同。 |
| Network Namespace | 网络设备、网络栈、端口等等 | 每个容器用有其独立的网络设备，IP 地址，IP 路由表等等。这也使得各个容器都能有自己的端口，互不影响。 |
| User Namespace    | 用户和用户组               | User Namespace可以保证各个容器中有各自的user和group id。     |

### 1.0 Mount NameSpace

&emsp;mount namespace通过隔离文件系统挂载点对隔离文件系统提供支持。Docker在创建Mount Namespace时，会把当前的文件结构复制给新的Namespace，这样可以使得不同的文件系统之间即使文件结构发生变化也互不影响。我们可以通过/proc/[pid]/mounts查看到所有挂载在当前namespace中的文件系统，还可以通过/proc/[pid]/mountstats看到mount namespace中文件设备的统计信息，包括挂载文件的名字、文件系统类型、挂载位置等等。

### 2.0 UTS NameSpace

&emsp;UTS提供了主机名和域名的隔离，可以使得每个Docker都拥有自己独立的主机名和域名。Docker为每个镜像都提供了以自身服务名命名的容器的主机名。

### 3.0 IPC NameSpace

&emsp;进程间通信（Inter-Process Communication，IPC）涉及到的IPC资源，包括常见的信号量、消息队列和共享内存。然而与虚拟机不同的是，容器内部进程间通信对宿主机来说，实际上是具有相同PID namespace中的进程间通信，因此需要一个唯一的标识符来进行区别。申请IPC资源就是申请了一个全局唯一的32位ID，所以IPC namespace中实际上包含了系统IPC标识符以及实现POSIX消息队列的文件系统。在同一个IPC namespace下的进程彼此可见，不同IPC namespace下的进程则互相不可见。

### 4.0 PID NameSpace

&emsp;PID NameSpace在Docker隔离中起了很重要的作用。两个不同的Namespace下同一进程可以有同一个PID。每个PID namespace都有自己的计数程序。内核为所有的PID namespace维护了一个树状结构，最顶层的是系统初始时创建的，被称为root namespace。它创建的新PID namespace就称为child namespace（树的子节点），而原先的PID namespace就是新创建的PID namespace的parent namespace（树的父节点）。通过这种方式，不同的PID namespaces会形成一个层级体系。所属的父节点可以看到子节点中的进程，并可以通过信号等方式对子节点中的进程产生影响。

### 5.0 Network NameSpace

&emsp;network namespace主要提供了关于网络资源的隔离，包括网络设备、IPv4和IPv6协议栈、IP路由表、防火墙、/proc/net目录、/sys/class/net目录、套接字（socket）等等。一个物理的网络设备最多存在在一个network namespace中，可以通过创建veth pair（虚拟网络设备对：有两端，类似管道，如果数据从一端传入另一端也能接收到，反之亦然）在不同的network namespace间创建通道，以此达到通信的目的。

### 6.0 User NameSpace

&emsp;user namespace主要隔离了安全相关的标识符（identifiers）和属性（attributes），包括用户ID、用户组ID、root目录、key（指密钥）以及特殊权限。通俗地讲，一个普通用户的进程通过clone()创建的新进程在新user namespace中可以拥有不同的用户和用户组。这意味着一个进程在容器外属于一个没有特权的普通用户，但是他创建的容器进程却属于拥有所有权限的超级用户，这个技术为容器提供了极大的自由。

### 相关资料

**Docker背后的内核知识** <https://www.cnblogs.com/beiluowuzheng/p/10004132.html> 

**理解Docker（3）：Docker 使用 Linux namespace 隔离容器的运行环境** <https://www.cnblogs.com/sammyliu/p/5878973.html>

**DOCKER基础技术：LINUX NAMESPACE（上）** <https://coolshell.cn/articles/17010.html>

**DOCKER基础技术：LINUX NAMESPACE（下）** <https://coolshell.cn/articles/17029.html>