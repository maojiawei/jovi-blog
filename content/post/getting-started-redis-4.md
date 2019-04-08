---
title: "redis入门实战之四:持久化"
date: 2019-04-07T13:29:36+08:00
draft: false
tags: ["redis"]
categories: "microservice"
toc: true
---

本篇文章主要介绍redis是如何采用实现持久化的。

# 1.0 简介
&emsp;Redis强大在于它主要依赖于内存，但是内存中的数据会由于重启等因素而丢失。为了避免这个问题，需要将数据从内存持久化至硬盘中。目前Redis支持两种持久化方式:一种是RDB持久化方式，另一种是AOF持久化方式。

# 2.0 区别
## 2.1 RDB持久化
&emsp;RDB持久化在指定时间间隔内通过异步方式对数据进行快照存储。它的操作过程是fork一个进程，遍历hashtable，利用copy on write，把整个db dump下来。  
![redis-rdb持久化方式](../images/redis/redis-rdb.jpg)
## 2.2 AOF持久化
&emsp;AOF持久化是以日志的形式记录每一个操作（包括写操作与删除操作，查询除外），以文本的方式记录。  
![redis-aof持久化.jpg](../images/redis/redis-aof持久化.jpg)

# 3.0 优劣势
## 3.1 RDB持久化
**优势:**  
- RDB是经过压缩、准实时的数据文件。RDB文件对于备份来说是完美的。例如，你可能想要在后面的24小时内，每一个小时保存一个RDB文件。它可以很容易地保存多个版本地数据文件，以应对各种风险。  
- RDB在灾难恢复方面可以做得很好，它可以以一个单一的经过压缩的文件被传输至数据中心，甚至是Amazon S3。  
- RDB是通过异步的方式进行的，因此他有助于Redis的性能最优化。  
- RDB相对于AOP来说，重启后的恢复速度更快。  
**劣势:**  
- 如果您需要在Redis停止工作时（例如断电后）将数据丢失的可能性降至最低，则RDB并不好。    
- RDB需要fork()才能调用子进程持久存储在磁盘上。如果数据集很大，Fork()可能会非常耗时，如果数据集非常大且CPU性能不佳，可能会导致Redis停止服务客户端几毫秒甚至一秒钟。 

## 3.2 AOF持久化
**优势:**  
- Redis使用AOF可以更持久：可以使用不同的fsync策略、不使用fsync、每秒执行fsync或者每次查询都使用fsync。使用fsync的默认策略，每秒写入性能仍然很好，如果宕机，也只丢失一秒的写入。    
- AOF的日志是只增的，所以没有搜索或者损坏问题。    
- 当AOF太大时，Redis可以自动后台重写。  
- AOF记录的是一个接一个的连续命令，即使使用flushall误操作，也能进行恢复。  
**劣势:**  
- 对于相同数量的数据集而言，AOF文件通常要大于RDB文件。RDB在恢复时的速度也比AOF的恢复速度要快。  
- 跟据同步策略的不同，AOF在运行效率上往往会慢于RDB。总之，每秒同步策略的效率是比较高的，同步禁用策略的效率和RDB一样高效

# 4.0 持久化配置
&emsp;redis的配置一般在redis.conf中，在通过redis-server启动的时候可以指定配置文件，例如：`redis-server redis.conf`。
## 4.1 RDB持久化配置
&emsp;以下为redis在RDB持久化的相关配置。
```conf
# 时间策略
# 在900秒(15分钟)之后，如果至少有1个key发生变化，则dump内存快照。
save 900 1
# 在300秒(5分钟)之后，如果至少有10个key发生变化，则dump内存快照。
save 300 10
# 在60秒(1分钟)之后，如果至少有10000个key发生变化，则dump内存快照。
save 60 10000

# 文件名称
dbfilename dump.rdb

# 文件保存路径
dir /opt/redis/data/

# 如果持久化出错，主进程是否停止写入
stop-writes-on-bgsave-error yes

# 是否压缩
rdbcompression yes

# 导入时是否检查
rdbchecksum yes
```
**注意:**  
- `stop-writes-on-bgsave-error yes`，该配置当备份进程出错时，主进程就停止接收写的新操作，是为了保持数据原子一致性。  
- 关于压缩的配置`rdbcompression yes`，推荐不开启，因为开启压缩会带来更多的CPU消耗。  
- 如果想要禁用RDB配置，只需写入`save ""`。  

## AOF持久化配置
```conf
# 是否开启aof
appendonly yes

# 文件名称
appendfilename "appendonly.aof"

# 同步方式
appendfsync everysec

# aof重写期间是否同步
no-appendfsync-on-rewrite no

# 重写触发配置
auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb

# 加载aof时如果有错如何处理
aof-load-truncated yes

# 文件重写策略
aof-rewrite-incremental-fsync yes
```
**注意:**  s
- `appendfsync`有三种模式:  
1.always：把每个写命令都立即同步到aof，很慢，但是很安全   
2.everysec：每秒同步一次，是折中方案    
3.no：redis不处理交给OS来处理，非常快，但是也最不安全  
- `aof-load-truncated yes`如果该配置启用，在恢复遇到不正确，会向客户端写入一个log，但是会继续执行;如果设置为 no ，发现错误就会停止，必须修复后才能重新加载。  

参考:  
https://www.cnblogs.com/chenliangcl/p/7240350.html
https://segmentfault.com/a/1190000015983518
