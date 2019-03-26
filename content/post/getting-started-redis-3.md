---
title: "redis入门实战之三:事务机制"
date: 2019-03-24T18:31:35+08:00
draft: false
tags: ["redis"]
categories: "microservice"
toc: true
---

本篇文章主要介绍redis是如何采用事务的。


## 1.0 简介
&emsp;事务在关系型数据库中是一组需要原子化执行的操作，即一组操作必须同时成功或失败。但是在redis中，事务是另外一个概念，redis的事务有一下几个特点:  

- Redis事务提交的命令不会马上执行，在执行`EXEC`这个执行事务的命令执行之前，这些命令都会在队列中
- 在`EXEC`执行事物的过程中，事务中任意命令执行失败，其余的命令依然会执行
- 在事务执行过程，其他客户端提交的命令请求不会插入到事务执行命令序列中。

## 2.0 命令
1. **命令:**DISCARD  
**描述:**取消事务，放弃执行事务块内的所有命令。  

2. **命令:**EXEC  
**描述:**执行所有事务块内的命令。  

3. **命令:**MULTI  
**描述:**标记一个事务块的开始。  

4. **命令:**WATCH ${KEY} [${KEY} ...]  
**描述:**监视一个(或多个) key ，如果在事务执行之前这个(或这些) key 被其他命令所改动，那么事务将被打断。  
**参数:**${key}为redis的键

5. **命令:**UNWATCH   
**描述:**取消 WATCH 命令对所有 key 的监视。 

## 3.0 案例

### 3.1 正常情况下
```
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set name jovi
QUEUED
127.0.0.1:6379> set age 18
QUEUED
127.0.0.1:6379> set job developer
QUEUED
127.0.0.1:6379> exec
1) OK
2) OK
3) OK
```

### 3.2 语法错误情况下
在语法错误情况下，整个事务会快速失败，且事务中的所有命令都不会处理。
```
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set name judy
QUEUED
127.0.0.1:6379> sat age 3
(error) ERR unknown command `sat`, with args beginning with: `age`, `3`,
127.0.0.1:6379> set job programmer
QUEUED
127.0.0.1:6379> exec
(error) EXECABORT Transaction discarded because of previous errors.
127.0.0.1:6379> get name
"jovi"
```

### 3.3 执行过程中错误
事务中所有命令皆已入队列，但在执行过程中发生错误，其余的命令依然会执行。
```
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set name fran
QUEUED
127.0.0.1:6379> set age 3
QUEUED
127.0.0.1:6379> incr job
QUEUED
127.0.0.1:6379> set name fran2
QUEUED
127.0.0.1:6379> exec
1) OK
2) OK
3) (error) ERR value is not an integer or out of range
4) OK
127.0.0.1:6379> get name
"fran2"
```
