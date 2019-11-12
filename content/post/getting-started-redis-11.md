---
noevictiontitle: "redis入门实战之十一:Redis的缓存淘汰策略"
date: 2019-11-12T17:12:16+08:00
draft: true
tags: ["redis"]
categories: "microservice"
toc: true
---
&emsp;redis的过期时间指的是在key过期之后，如何删除过期的key。而缓存淘汰策略是在内存不足的情况下，淘汰缓存的策略。

### 1.0 缓存淘汰策略

&emsp;我们可以在redis.conf中配置` maxmemory 100mb `，即最高的内存为100mb。而`maxmemory-policy`可以选择对应的缓存淘汰策略，例如:`maxmemory-policy noeviction`。

| 策略            | 描述                                                   |
| --------------- | ------------------------------------------------------ |
| noeviction      | 当内存使用达到阈值的时候，新增会报错（默认）           |
| allkeys-lru     | 优先移除最近未使用的key（推荐）                        |
| volatile-lru    | 在设置了过期时间的key中，优先移除最近未使用的key       |
| allkeys-random  | 在所有的key中，随机移除某个key                         |
| volatile-random | 在设置了过期时间的key中，随机移除某个key               |
| volatile-ttl    | 在设置了过期时间的key中，具有更早过期时间的key优先移除 |

### 2.0 淘汰原则

&emsp;Redi会随机取出N(N由redis.conf中指定)个key，然后按照访问时间排序后，淘汰掉最不经常使用的。
