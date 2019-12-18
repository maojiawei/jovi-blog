---
title: "限流算法"
date: 2019-12-18T13:05:59+08:00
draft: false
categories: "microservice"
toc: true
---
&emsp;我们在文章[服务的限流与熔断](http://jovi.io/post/getting-started-microservice-6/)中详细介绍过限流，简单介绍过相关的限流算法。今天我们将详细展开介绍几种常见的限流算法。

### 1.0 计数器限流算法

&emsp;计数器限流算法就是统计**单位时间内**进入系统或某个请求的次数，一旦发现超过了**限定的次数**，就会触发限流。

![计数器限流算法](../images/microservice/计数器限流算法.jpeg)

```java
public class CounterTest {
    // 初始时间的时间戳
    public long timeStamp = getNowTime();
    // 统计调用次数
    public int reqCount = 0;
    // 时间窗口内最大请求数
    public final int limit = 100; 
    // 时间窗口ms
    public final long interval = 1000; 
    
    // 判断是否授权
    public boolean grant() {
        long now = getNowTime();
        if (now < timeStamp + interval) {
            // 在时间窗口内
            reqCount++;
            // 判断当前时间窗口内是否超过最大请求控制数
            return reqCount <= limit;
        } else {
            timeStamp = now;
            // 超时后重置
            reqCount = 1;
            return true;
        }
    }

    public long getNowTime() {
        return System.currentTimeMillis();
    }
}
```

&emsp;这种算法虽然简单，但是有个漏洞。如果我们设置单位时间窗口一分钟，而最多调用次数100次。那么有人可能在00:59分的时候调用100次，1:00的时候在调用100次。这样就会瞬间压垮我们的系统。

### 2.0 滑动窗口限流算法

&emsp;在下图中，我们将滑动窗口分为6个格子（单位时间：0:00-1:00，每10分钟一个格子）。每个格子都有自己一个独立的计数器，每过10分钟，我们的时间窗口就会右滑动一格。

![滑动窗口限流算法](../images/microservice/滑动窗口限流算法.jpeg)

### 3.0 令牌桶
令牌桶是有一个存放固定容量令牌的桶，当有请求进来的时候，就拿走一个令牌；请求处理完成，归还令牌。如果令牌桶已空，那么新进来的请求将拒绝或放入队列中。

<img src="../images/microservice/令牌桶算法.png" width = "600" height = "300"/> 


#### 4.0 漏桶算法
&emsp;漏桶算法有个固定容量的桶，桶的底部是一个洞。不管请求多块，漏桶只按照固定的流速让水流出，当水（请求）超过了桶能承载的范围内，即拒绝请求。因为桶容量是不变的，保证了整体处理请求的速率。

<img src="../images/microservice/漏桶算法.jpg" width = "300" height = "400"/> 

### 参考资料
[限流算法介绍](https://www.jianshu.com/p/9f7df2ebbb82)
[三种常见的限流算法](https://cloud.tencent.com/developer/article/1434134)
