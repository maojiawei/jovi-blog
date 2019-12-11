---
title: "微服务架构系列：服务的限流与熔断"
date: 2019-12-11T16:58:16+08:00
draft: false
categories: "microservice"
toc: true
---
&emsp;在了解服务熔断与限流之前，我们先来了解一个重要的概念，叫做: **服务雪崩**。

### 1.0 服务雪崩
&emsp;当我们存在以下这样一个架构，我们有 A、B、C、D、E 五个服务（架构如下图所示）。当服务E不可用之后，C和D的`超时`和`重试`机制将会启动。

&emsp;由于新的调用不断增加，旧的调用没有及时处理，造成了服务C和服务D的调用大量积压，慢慢耗尽服务C和D的资源（例如:CPU或内存），最终导致C和D不可用。

&emsp;同样的情况，A和B也会资源耗尽，最终导致整个系统不可用。

![服务雪崩](../images/microservice/服务雪崩.jpg)

&emsp;一般造成雪崩的原因有以下几点：

1. 程序Bug导致。例如：大量的程序异常会导致运行缓慢甚至宕机。
2. 大量突增请求。例如：大量请求导致资源不可用时。
3. 硬件问题。例如：网络异常，硬件故障灯。
4. 其它

&emsp;有时候服务中断不可避免，但我们可以避免服务雪崩的情况。解决方案如下:

1. 缓存。我们可以利用Redis等中间件缓存数据，避免对下游服务的压力。
2. 服务限流
3. 服务熔断

### 2.0 服务限流

&emsp;服务限流又称为流量整形（Traffic Shaping），在突发高并发情况下，为了保护后端的服务节点不被巨大的洪峰冲垮。限流可以通过对一段时间内的请求流量限速来保护系统，一旦达到限流阀值，就会启动限流。限流之后常见的操作如下：

- 拒绝请求：返回一个错误页面或告知用户系统错误。
- 排队等待：将客户端请求放入队列，在服务端有资源处理时再从队列中获取请求，再进行处理并返回客户端。客户端一般会设置超时等待时间，请求失效后再次发起请求（参考接口的幂等性设计）。这种方式适用于“秒杀”或车票抢购等场景。
- 应用降级：提供默认的行为或数据，如默认显示无任何评论等。

#### 2.1 限流的实现方式

1.**客户端限流:** 

&emsp;客户端限流是为了防止某个客户端过多的对服务端进行请求。这种方式的**优点**在于可以在客户端就进行限流，以降低网络传输的消耗。**缺点**在于客户端限流无法做到全局控制。

2.**服务端限流**

&emsp;服务端限流可以在服务端采取限流超时。虽然客户端可以进行限流，但一个服务端需要对多个客户端提供服务。对于每个服务来说，都会有一个极限并发阀值，因此服务端需要根据自身的情况设置限流，防止大量请求涌入将系统击垮。

&emsp;一般来说，会首先通过客户端限流作为第一道屏障，服务端限流作为最终屏障，保护服务的健壮性。

#### 2.2 限流的常用算法

1.**计数器限流算法：** 通过一个计数器统计一段时间内请求数量，超过一定阀值，触发限流措施。

2.**漏桶算法（Leaky Bucket）:** 漏桶算法有个固定容量的桶，桶的底部是一个洞。不管请求多块，漏桶只按照固定的流速让水流出，当水（请求）超过了桶能承载的范围内，即拒绝请求。

<img src="../images/microservice/漏桶算法.jpg" width = "300" height = "400"/> 

3.**令牌桶算法（Token Bucket）：**令牌桶是有一个存放固定容量令牌的桶，当有请求进来的时候，就拿走一个令牌；请求处理完成，归还令牌。如果令牌桶已空，那么新进来的请求将拒绝或放入队列中。

<img src="../images/microservice/令牌桶算法.png" width = "600" height = "300"/> 

### 3.0 服务熔断
&emsp;熔断(circuit breaker)也称为自动停盘机制。这个说法最早起源于股票机制，当股票的波幅达到一定熔断点，交易所谓控制风险所采取的的暂停交易措施。

&emsp;在流量过大的情况下，熔断可以禁止所有客户端对服务端访问。相比限流，限流是允许部分流量通过，而熔断则是完全禁止客户端访问后端服务，他们的目的都是防止流量洪峰冲垮集群。

#### 3.1 熔断状态

1.**关闭状态：** 熔断器关闭时，不会对请求进行任何干涉，仅仅监控请求的阀值。

2.**开启状态：** 当对服务端请求达到一定阀值后，触发熔断，这时熔断器就会进入开启状态。

3.**半开启状态：** 半开启允许少量请求处理，如果达到预期，则认为问题已被修正，熔断器会切换至关闭状态。如果仍有问题，熔断器会切换至开启状态。